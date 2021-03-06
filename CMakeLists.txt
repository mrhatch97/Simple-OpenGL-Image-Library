cmake_minimum_required (VERSION 3.0)
project (SOIL)

option (SOIL_BUILD_TESTS "Build tests" off)

if(ANDROID)
  # Android requires GL ES 2.0 package automatically
  set(LIBRARIES GLESv2)
else()
  find_package (OpenGL REQUIRED)
  include_directories (${OPENGL_INCLUDE_DIR})
  set(LIBRARIES ${OPENGL_LIBRARIES})
endif()

add_library (SOIL 
	src/image_DXT.c
	src/image_helper.c
	src/SOIL.c
	src/stb_image_aug.c)

target_link_libraries (SOIL ${LIBRARIES})

target_include_directories(SOIL 
	PUBLIC
		$<BUILD_INTERFACE:${PROJECT_SOURCE_DIR}/include>
		$<INSTALL_INTERFACE:include>
    PRIVATE 
		${PROJECT_SOURCE_DIR}/src
)

include(GNUInstallDirs)

install(TARGETS SOIL EXPORT soilConfig
    ARCHIVE  DESTINATION ${CMAKE_INSTALL_LIBDIR}
    LIBRARY  DESTINATION ${CMAKE_INSTALL_LIBDIR})

install(DIRECTORY include/ DESTINATION ${CMAKE_INSTALL_INCLUDEDIR})

export(TARGETS SOIL
	FILE soilConfig.cmake
	NAMESPACE SOIL::
)

install(EXPORT soilConfig
	DESTINATION share/SOIL/cmake)

if (SOIL_BUILD_TESTS)
	add_executable (test_SOIL src/test_SOIL.cpp)
	target_link_libraries (test_SOIL SOIL)
	install (TARGETS SOIL test_SOIL RUNTIME DESTINATION bin)
endif ()