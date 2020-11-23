file(GLOB source "*.cpp" "*.c")
file(GLOB headers "*.h")

file(GLOB os_source "${emu_os}/*.cpp" "${emu_os}/*.c" "${emu_os}/*.mm")
file(GLOB os_headers "${emu_os}/*.h")

if(WIN32)
    file(GLOB plat_source "win32/*.cpp")
else()
    file(GLOB plat_source "posix/*.cpp")
endif()

if(ANDROID)
    add_library(emu SHARED ${source} ${headers} ${os_source} ${os_headers} ${plat_source})
else()
    add_executable(emu ${source} ${headers} ${os_source} ${os_headers} ${plat_source})
endif()

# Prefer C11 Standard
target_compile_features(emu PUBLIC c_std_11)

# Enforce correct subsystem
if(WIN32)
    if(MSVC)
        set_property(TARGET emu APPEND_STRING PROPERTY LINK_FLAGS "/subsystem:console")
    elseif(MINGW)
        set_property(TARGET emu APPEND_STRING PROPERTY LINK_FLAGS "--subsystem,console")
    else()
        set_property(TARGET emu APPEND_STRING PROPERTY LINK_FLAGS "-Xlinker /subsystem:console")
    endif()
endif()

# Disable "Security" warnings
if(WIN32)
    target_compile_definitions(emu PUBLIC _CRT_SECURE_NO_WARNINGS)
endif()

# Disable RTTI and Exceptions
if(MSVC)
    target_compile_options(emu PUBLIC /GR-)
else()
    target_compile_options(emu PUBLIC -fno-rtti -fno-exceptions)
endif()

# Warnings as errors
if(MSVC)
    target_compile_options(emu PRIVATE /W4 /WX)
else()
    target_compile_options(emu PRIVATE -Wall -Wextra -pedantic -Werror)
endif()

# Define what's going to end up in the install tree
set(install_destination "$<LOWER_CASE:$<CONFIG>>")
if(ANDROID)
    install(TARGETS emu
            DESTINATION ${install_destination}/lib/${CMAKE_ANDROID_ARCH_ABI})
else()
    install(TARGETS emu
            DESTINATION ${install_destination})
endif()
