file(GLOB source "*.cpp" "*.c")
file(GLOB headers "*.h")

file(GLOB os_source "${template_os}/*.cpp" "${template_os}/*.c" "${template_os}/*.mm")
file(GLOB os_headers "${template_os}/*.h")

if(WIN32)
    file(GLOB plat_source "win32/*.cpp")
else()
    file(GLOB plat_source "posix/*.cpp")
endif()

if(ANDROID)
    add_library(template SHARED ${source} ${headers} ${os_source} ${os_headers} ${plat_source})
else()
    add_executable(template ${source} ${headers} ${os_source} ${os_headers} ${plat_source})
endif()

# Prefer C++98 Standard
target_compile_features(template PUBLIC cxx_std_98)

# Disable "Security" warnings
if(WIN32)
    target_compile_definitions(template PUBLIC _CRT_SECURE_NO_WARNINGS)
endif()

# Disable RTTI and Exceptions
if(MSVC)
    target_compile_options(template PUBLIC /GR-)
else()
    target_compile_options(template PUBLIC -fno-rtti -fno-exceptions)
endif()

# Warnings as errors
if(MSVC)
    target_compile_options(template PRIVATE /W4 /WX)
else()
    target_compile_options(template PRIVATE -Wall -Wextra -pedantic -Werror)
endif()

# Define what's going to end up in the install tree
set(install_destination "$<LOWER_CASE:$<CONFIG>>")
if(ANDROID)
    install(TARGETS template
            DESTINATION ${install_destination}/lib/${CMAKE_ANDROID_ARCH_ABI})
else()
    install(TARGETS template
            DESTINATION ${install_destination})
endif()
