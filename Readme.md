# create-cpp-app

Create CMake based C/C++ projects with a simple configuration.

## Quick Overview

```sh
$ create-cpp-app my-app
$ cd my-app/

$ ./build

$ out/release/bin/my-app
2 + 2 = 4. 4 - 1 = 3, quick maths

$ out/release/bin/my-app-test
===============================================================================
All tests passed (3 assertions in 1 test case)
```

## Getting Started

### Installation

Simply [download](https://raw.githubusercontent.com/udeyrishi/create-cpp-app/master/create-cpp-app) the script to any location in your `PATH`, and you're good to go.

**You'll need Python 2 or 3 installed on your system**. Python isn't necessary for the actual project itself, but only for the `create-cpp-app` script.

The generated project requires **CMake 3.2+**, and a C/C++ compiler that will work with it (tested with GCC and Clang on macOS and Linux).


### Creating a Project

```sh
$ ./create-cpp-app my-app
$ cd my-app
```

It will create a directory `my-app` in the current folder. Alternatively, you can provide a destination path for `my-app`:

```sh
$ ./create-cpp-app my-app ~/Developer/
$ cd ~/Developer/my-app
```

You can use the `-f` option to first delete the `my-app` directory if it exists.

This is what the initial project structure will look like:

```
my-app
    - inc
        - demo.hpp
    - src
        - demo.cpp
        - main.cpp
    - test
        - inc
            - catch.hpp
        - demo_test.cpp
        - runner.cpp
    - .gitignore
    - .travis.yml
    - build
    - CMakeLists.txt
```

Once the project is setup, you can start working with it through whatever tooling you use for a standard CMake project. However, if you have Python installed on your system, a handy Python script `build` is included for running the most common actions.

### `./build`

`./build` is a handy Python script that:

* Manages all the temporary CMake files and built artifacts in a git-ignored `out` directory
* Lets you manage the `release` and `debug` variants' artifacts cleanly and separately
* Lets you perform common actions such as a rebuild + test + run the app quickly

```sh
$ cd my-app

$ ./build

$ # Clean and rebuild
$ ./build -c

$ # Can also create the debug variant
$ ./build -d
$ lldb out/debug/bin/my-app
$ # start debugging

$ # Can run the tests for the specified variant
$ ./build -t
===============================================================================
All tests passed (3 assertions in 1 test case)

$ # Can run the main binary for the specified variant
$ ./build -r
2 + 2 = 4. 4 - 1 = 3, quick maths

$ # or do everything, such as for verifying a change: clean + build the debug variant + run the tests + start the app
$ ./build -cdtr
```


## Noteworthy Information

* The header files in the `inc` directory are added to the include path at compile time, and should be referenced accordingly. For instance, a header file `inc/somedir/some_file.hpp` can be included in another file as `#include <somedir/some_file.hpp>`.

* The `.travis.yml` is a good starting Travis CI YAML file for simple projects. Feel free to remove it if you don't use Travis.

* The `test/inc` directory should be used for keeping testing library headers. The script automatically downloads the latest version of [Catch](https://github.com/catchorg/Catch2), which is the recommended testing library.

### CMake Configuration
The CMake project is configured as follows:

*  Support for both C and C++. CMake differentiates between the two languages based on the `.c` or `.cpp` file extensions.
*  Language standards C++14 and C99 enabled.
*  `-Wall -Wextra -Werror -Wsign-conversion -pedantic` enabled.
*  All the source files except `main.cpp` will be used for creating a static lib target, `lib-my-app-lib`.
	*  This static lib will be linked with `src/main.cpp` to create the actual binary, `my-app`.
	*  The static lib will be linked with `test/runner.cpp` to create the test binary, `my-app-test`.

The above settings can be easily tweaked in the `CMakeLists.txt` file to fit your needs.
