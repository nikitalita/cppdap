cmake_minimum_required (VERSION 3.8)

if (NOT DEFINED VCPKG_ROOT AND DEFINED ENV{VCPKG_ROOT})
	set(VCPKG_ROOT $ENV{VCPKG_ROOT})
endif ()
message(STATUS "CMAKE TOOLCHAIN FILE IS ${CMAKE_TOOLCHAIN_FILE}")
message(STATUS "VCPKG_TARGET_TRIPLET is ${VCPKG_TARGET_TRIPLET}")

if (DEFINED VCPKG_TARGET_TRIPLET)
	set(_VCPKG_DETECTED TRUE)
elseif (DEFINED CMAKE_TOOLCHAIN_FILE)
	string(FIND ${CMAKE_TOOLCHAIN_FILE} "vcpkg.cmake" _VCPKG_DETECTED)
endif()

if (_VCPKG_DETECTED)
	message(STATUS "HALDO!!!")
endif()


if (DEFINED VCPKG_ROOT)
	#set(CMAKE_TOOLCHAIN_FILE "$ENV{VCPKG_ROOT}/scripts/buildsystems/vcpkg.cmake" CACHE STRING "")
	set(VCPKG_TARGET_TRIPLET "x64-windows-static-md" CACHE STRING "")
else ()
	message(
		WARNING
		"Variable VCPKG_ROOT is not set. Continuing without vcpkg."
	)
endif ()
project (hello_debugger LANGUAGES CXX)

include(GNUInstallDirs)
find_package(cppdap CONFIG REQUIRED)
source_group(
	TREE "${CMAKE_CURRENT_SOURCE_DIR}"
	FILES "hello_debugger.cpp"
)
add_executable ("${PROJECT_NAME}" "hello_debugger.cpp")

target_include_directories(
	"${PROJECT_NAME}"
	PUBLIC
		"$<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}/include>"
		"$<INSTALL_INTERFACE:${CMAKE_INSTALL_INCLUDEDIR}>"
)
target_include_directories(
	"${PROJECT_NAME}"
	PRIVATE
	  ${cppdap_INCLUDE_DIRS}

)
target_link_libraries("${PROJECT_NAME}" PRIVATE cppdap::cppdap)


if (CMAKE_VERSION VERSION_GREATER 3.12)
  set_property(TARGET "${PROJECT_NAME}" PROPERTY CXX_STANDARD 20)
endif()