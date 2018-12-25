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

install (TARGETS SOIL EXPORT soilConfig
	LIBRARY DESTINATION lib
	ARCHIVE DESTINATION lib)
install (FILES ${SOIL_HEADERS} DESTINATION include/SOIL)

if (SOIL_BUILD_TESTS)
	add_executable (test_SOIL src/test_SOIL.cpp)
	target_link_libraries (test_SOIL SOIL)
	install (TARGETS SOIL test_SOIL RUNTIME DESTINATION bin)
endif ()