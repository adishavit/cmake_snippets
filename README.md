# CMake Snippets `${✂️}`
*Short copy-pasteable "Modern" CMake snippets.*  

These snippets are not comprehensive and are not a replacement for documentation.    
Use them to avoid needing to memorize them.

## New Project
Name your project and set the minimum CMake version

```cmake
cmake_minimum_required(VERSION 3.4)
project(MyProj)
```
> [`cmake_minimum_required()`](https://cmake.org/cmake/help/latest/command/cmake_minimum_required.html), [`project()`](https://cmake.org/cmake/help/latest/command/project.html)


## Default Build Type
Set default build type, e.g. Release. Turn on Debug with `-DCMAKE_BUILD_TYPE=Debug`
```cmake
if(NOT CMAKE_BUILD_TYPE)
  set(CMAKE_BUILD_TYPE Release)
endif()
```
> [`CMAKE_BUILD_TYPE`](https://cmake.org/cmake/help/latest/variable/CMAKE_BUILD_TYPE.html)

## Adding `#include` Paths
Prefer per-target includes to scope-based commands:
```cmake
target_include_directories(my_app PUBLIC mylib/include)
```
This command adds `mylib/include` to the `my_app` include search paths.

When another dependent target uses `target_link_libraries()` to *link* with a lib with `INTERFACE` or `PUBLIC` `target_include_directories()`, the linking target will automatically have the library include directories added.

Thus you should prefer `target_include_directories()` to the older command:
```cmake
include_directories(my_app mylib/include)
```
`include_directories()` works at directory scope. All targets in this `CMakeList.txt`, as well as those in all subdirectories added after the point of its call, will have the path `mylib/include` added to their include path.



> [`target_include_directories()`](https://cmake.org/cmake/help/latest/command/target_include_directories.html), [`include_directories()`](https://cmake.org/cmake/help/latest/command/include_directories.html) [SO-1](https://stackoverflow.com/a/31969632/135862), [SO-2](https://stackoverflow.com/a/40244458/135862)

## Create Header Only Library
```cmake
add_library(my_header_only_lib INTERFACE)                              # No sources provided
target_sources(my_header_only_lib INTERFACE my_header.hpp)             # Adds header files to target, will appear in IDEs
target_include_directories(my_header_only_lib INTERFACE my_header.hpp) # Allow lib dependent target to auto add includes too 
```
When another target uses `target_link_libraries()` to link with `my_header_only_lib`, the linking target  will automatically have the library include directories added.

> [`add_library()`](https://cmake.org/cmake/help/latest/command/add_library.html), [`target_sources()`](https://cmake.org/cmake/help/latest/command/target_sources.html), [`target_include_directories()`](https://cmake.org/cmake/help/latest/command/target_include_directories.html), [SO-2](https://stackoverflow.com/a/40244458/135862)  

## Setting C++ Standard
Set the *default* C++ standard for *all* targets:
```cmake
set(CMAKE_CXX_STANDARD 17) # C++17
```
Set the C++ standard for a specific target `my_app`:
```cmake
set_property(TARGET my_app PROPERTY CXX_STANDARD 17) # C++17
```
This is preferable to setting the compiler-specific commands such as `-std=c++17`.
> [`CMAKE_CXX_STANDARD`](https://cmake.org/cmake/help/latest/variable/CMAKE_CXX_STANDARD.html), [`CXX_STANDARD`](https://cmake.org/cmake/help/latest/prop_tgt/CXX_STANDARD.html)

## Put Targets in IDE/Solution Folders
Turn on project folder support property and set each target to its named folder 
```cmake
set_property(GLOBAL PROPERTY USE_FOLDERS ON) # Turn on this feature
...
set_property(TARGET my_lib PROPERTY FOLDER "libs")
set_property(TARGET another_lib PROPERTY FOLDER "libs/3rdParty")
set_property(TARGET my_app PROPERTY FOLDER "utils")
```
> [`USE_FOLDERS`](https://cmake.org/cmake/help/latest/prop_gbl/USE_FOLDERS.html), [`FOLDER`](https://cmake.org/cmake/help/latest/prop_tgt/FOLDER.html#prop_tgt:FOLDER)
