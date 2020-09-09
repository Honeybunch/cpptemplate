cmake_minimum_required(VERSION 3.17)

project(template)

# Add third party libraries
if(CMAKE_HOST_WIN32)
    if(WIN32)
        set(CMAKE_PREFIX_PATH $ENV{LOCALAPPDATA}/cmake/windows)
    elseif(ANDROID)
        set(CMAKE_PREFIX_PATH $ENV{LOCALAPPDATA}/cmake/android)
    endif()
elseif(CMAKE_HOST_UNIX)
    if(ANDROID)
        set(CMAKE_PREFIX_PATH $ENV{HOME}/.cmake/android)
    elseif(APPLE)
        if(IOS)
            set(CMAKE_PREFIX_PATH $ENV{HOME}/.cmake/ios)
        else()
            set(CMAKE_PREFIX_PATH $ENV{HOME}/.cmake/macos)
        endif()
    else()
        set(CMAKE_PREFIX_PATH $ENV{HOME}/.cmake/linux)
    endif()
endif()
file(TO_CMAKE_PATH ${CMAKE_PREFIX_PATH} CMAKE_PREFIX_PATH)

# Disable /GR by default, can be forced on manually later
if(MSVC)
    string(REPLACE "/GR" "" CMAKE_CXX_FLAGS ${CMAKE_CXX_FLAGS})
    string(REPLACE "/GR" "" CMAKE_C_FLAGS ${CMAKE_C_FLAGS})
endif()

# A host's clang-tidy and CMake doesn't seem to play nicely with Android builds
# NOTE: Some platforms install clang-tidy as clang-tidy-X (where X is the major version).
# If clang-tidy isn't found on your machine, configure with:
# -DCMAKE_C_CLANG_TIDY=clang-tidy-X -DCMAKE_CXX_CLANG_TIDY=clang-tidy-X 
if(NOT ANDROID)
    set(CMAKE_C_CLANG_TIDY clang-tidy)
    set(CMAKE_CXX_CLANG_TIDY clang-tidy)
endif()

# Decide which OS were on and what needs to be done per-os
if(WIN32)
    set(template_os "windows")
elseif(APPLE)
    if(IOS)
        set(template_os "ios")
    else()
        set(template_os "macos")
    endif()
elseif(ANDROID)
    set(template_os "android")
elseif(UNIX)
    set(template_os "linux")
else()
    message(FATAL_ERROR "Unknown Platform")
endif()

# Decide which compiler wer're using and do any per-compiler logic
if(MSVC)
    if(CMAKE_CXX_COMPILER_ID MATCHES "Clang")
        set(template_compiler "llvm")
    else()
        set(template_compiler "msvc")
    endif()
elseif(CMAKE_CXX_COMPILER_ID MATCHES "Clang")
    set(template_compiler "llvm")
elseif(CMAKE_CXX_COMPILER_ID MATCHES "GNU")
    set(template_compiler "gcc")
else()
    message(FATAL_ERROR "Unknown Compiler")
endif()

if(CMAKE_GENERATOR MATCHES "Ninja")
    set(template_generator "ninja")
elseif(CMAKE_GENERATOR MATCHES "Visual Studio 16 2019")
    set(template_generator "vs2019")
elseif(CMAKE_GENERATOR MATCHES "Xcode")
    set(template_generator "xcode")
else()
    set(template_generator "unknown")
    message("Unknown build generator")
endif()

# Set install prefix if not already overridden
set(template_install "${CMAKE_CURRENT_SOURCE_DIR}/template/${template_os}/${template_generator}/${template_compiler}/${template_config}")
if (CMAKE_INSTALL_PREFIX_INITIALIZED_TO_DEFAULT)
    set (CMAKE_INSTALL_PREFIX ${template_install} CACHE PATH "Default Install Path" FORCE )
else()
    set(template_install ${CMAKE_INSTALL_PREFIX})
endif()

add_subdirectory(source)