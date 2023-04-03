################################
# Global
################################
cmake_minimum_required(VERSION 3.25)
project(glgui_lib VERSION 1.0 DESCRIPTION "OpenGL GUI Library" HOMEPAGE_URL "https://github.com/" LANGUAGES CXX)

if (NOT CMAKE_BUILD_TYPE)
	set(CMAKE_BUILD_TYPE "Debug" CACHE STRING "Build type" FORCE)
endif()
set_property(CACHE CMAKE_BUILD_TYPE PROPERTY STRINGS Release Debug)

set(BUILD_SHARED_LIBS ON CACHE BOOL "Build using shared libraries")
set(BUILD_TEST ON CACHE BOOL "Build testing app")

################################
# glgui
################################
file(GLOB_RECURSE
	SOURCES_GLGUI CONFIGURE_DEPENDS
	"src/*.cpp")

add_library(glgui ${SOURCES_GLGUI})

target_include_directories(
	glgui PUBLIC
	$<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}/include>
	$<INSTALL_INTERFACE:include>
)

target_link_libraries(glgui PRIVATE
	fmt
	glew32
	glfw3
	opengl32
)

target_compile_options(
	glgui PRIVATE
	-Wall -Wextra -pedantic)

target_compile_features(
	glgui PRIVATE
	cxx_std_17)

target_compile_definitions(
	glgui PRIVATE
	GLFW_INCLUDE_NONE)

set_target_properties(glgui PROPERTIES
	CXX_EXTENSIONS OFF)

# Generate glguiConfig.cmake stuff
install(TARGETS glgui
	EXPORT glguiTargets
	LIBRARY DESTINATION ${CMAKE_INSTALL_LIBDIR}
	ARCHIVE DESTINATION ${CMAKE_INSTALL_LIBDIR}
	RUNTIME DESTINATION ${CMAKE_INSTALL_BINDIR}
	INCLUDES DESTINATION ${CMAKE_INSTALL_INCLUDEDIR}
)

include(CMakePackageConfigHelpers)
write_basic_package_version_file(
	"${CMAKE_CURRENT_BINARY_DIR}/glguiConfigVersion.cmake"
	VERSION 1.0
	COMPATIBILITY AnyNewerVersion
)

export(EXPORT glguiTargets
	FILE "${CMAKE_CURRENT_BINARY_DIR}/glguiTargets.cmake"
)

configure_file(cmake/glguiConfig.cmake
	"${CMAKE_CURRENT_BINARY_DIR}/glguiConfig.cmake"
	COPYONLY
)

################################
# glgui Test App
################################
file(GLOB_RECURSE
	SOURCES_TEST CONFIGURE_DEPENDS
	"test/*.cpp")

add_executable(glgui_test ${SOURCES_TEST})

target_include_directories(
	glgui_test PRIVATE
	${CMAKE_CURRENT_SOURCE_DIR}/test/include
)

target_link_libraries(glgui_test PRIVATE
	glgui
)

target_compile_options(
	glgui_test PRIVATE
	-Wall -Wextra -pedantic)

target_compile_features(
	glgui_test PRIVATE
	cxx_std_17)

set_target_properties(glgui_test PROPERTIES
	CXX_EXTENSIONS OFF)

if (NOT BUILD_TEST)
	set_target_properties(glgui_test PROPERTIES
		EXCLUDE_FROM_ALL TRUE)
endif()
