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

## Add Build Option
Adds a CMake togglable variable that will also appear in cmake-gui, for conditional build settings.  
If the variable is set via the command line or the GUI the default initial value will be ignored:

```cmake
option(ENABLE_PYTHON_BINDINGS "Build Python bindings" OFF)    # Adds option, defaults to OFF

if(ENABLE_PYTHON_BINDINGS)                            
    add_subdirectory(external_libs/pybind11)
endif(ENABLE_PYTHON_BINDINGS)
```
> [option()](https://cmake.org/cmake/help/latest/command/option.html)

## (Unit) Testing

### Activate testing

Activate testing support for [ctest](https://cmake.org/cmake/help/latest/manual/ctest.1.html) via [enable_testing](https://cmake.org/cmake/help/latest/command/enable_testing.html)

### Adding a test

Test programs are normal executable created via [add_executable](https://cmake.org/cmake/help/latest/command/add_executable.html).

For that cmake knows an executable is a test it has to be registered as such. This is done via the [add_test](https://cmake.org/cmake/help/latest/command/add_test.html) command.

### Setting test properties

It is possible to add properties to a test, these are set via the [set_tests_properties](https://cmake.org/cmake/help/v3.0/command/set_tests_properties.html) command.

* `WILL_FAIL` true tells cmake that a test is expected to fail
* `TIMEOUT` limits the test runtime to the number of seconds specified. 

### Example

```cmake
enable_testing()
add_executable(mytest testcode1.cpp testcode2.cpp)
add_test(NAME myapp_mytest COMMAND mytest)
set_tests_properties(myapp_mytest TIMEOUT 1)
```

### Running tests

If testing is enabled and at least one test has ben registered there is now a `test` target available, which can be invoked via `make test` in the build directory.

Alternative, running directly `ctest` in the build directory is also a possibility.

If there are multiple test binaries in a project it is possible to execute them in parallel.
This works by the `-j` (jobs) command line switch.   
`ctest -j8` in case of running ctest, `make test ARGS=-j8` to pass the switch via make test to ctest. 


