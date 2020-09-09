# cpptemplate
A C++ Project Template

## What's Included

A simple CMake project that stands up a very simple Hello World application in C++

A small VSCode configuration that I use to configure and build the CMake project

The CMake project is configured to my general needs:
* C++98 standard
* Warnings as Errors
* Highest Warning level
* No RTTI
* No Exceptions
* Simple support for Windows, Mac, Linux, iOS and Android
* Builds and Installs correctly to an install tree that I find agreeable
* Comes with my preferred clang-format rules (just LLVM)
* Setup with my preferred clang-tidy rules
* Not IDE specific (CLion, VS2019 etc. should all work great with this)

The VSCode scripts are designed around my preferred environment:
* Windows first (but easy to adjust for other platforms)
* Sets up LLVM for Windows and MSVC build trees

### Known limitations
VSCode configs are very windows-centric. I haven't found a good VSCode workspace config that works across multiple OSes the way I'd like

The CMake scripts assume you're using a multi-config generator. It works best with:
* Ninja Multi-Config
* Visual Studio
* XCode

The Unix Makefiles, Ninja (non-multi-config) and other single config generators are not supported as I don't care for them.

### Notes

You will probably want to edit the CMake scripts to change the name of the project from `template` to whatever your project name is. 

The VSCode config is totally optional. Feel free to use any IDE. Scrap the configs if you want and just use CMake Tools for VSCode or no IDE at all. The CMake scripts should hold up.

This doesn't handle more complex scenarios like packaging. I have some experiments with using CMake to package for Android on Windows but a more comprehensive solution for iOS, Mac, UWP, Linux packages would be neat.

This handles no third-party dependencies. Though I have my own strategy for handling those that I may cover in a more comprehensive template.