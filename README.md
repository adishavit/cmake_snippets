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
