# create-cpp-project
----------

`create-cpp-project` is a Python script that creates a template C/C++ project with a configuration that I usually use for my personal hacks. 

***Requirements***

* CMake
* Python 2 or 3
* Clang or GCC 


***Usage***

```sh
$ ./create-cpp-project -h
usage: create-cpp-project [-h] [-f] name [dir]

Creates a new CMake based C/C++ project with Catch testing enabled.

positional arguments:
  name         project's name
  dir          the parent directory for the project. default = current working
               dir

optional arguments:
  -h, --help   show this help message and exit
  -f, --force  delete the project directory contents if it exists
```  

This is what the output looks like:

```
[project name]
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

***Notes***

* The `demo.hpp`, `demo.cpp`, and `demo_test.cpp` are placeholder files to demonstrate the expected project structure. 
* The files in the `inc` directory are added to the include path, and should be referenced accordingly. For example, a header file `inc/somedir/some_file.hpp` can be included in another file as `#include <somedir/some_file.hpp>`.
* The `.gitignore` and `.travis.yml` are good starting points for most simple projects.
* The `test/inc` directory should be used for keeping testing library headers. The script automatically downloads the latest version of [Catch](https://github.com/catchorg/Catch2) that can be used for testing.
* The CMake project is configured as follows:
	*  Support for both C and C++. CMake differentiates between the two languages based on the `.c` or `.cpp` file extensions
	*  Language standards: C++14 and C99
	*  `-Wall -Wextra -Werror -Wsign-conversion -pedantic` enabled
	*  All the source files except `main.cpp` will be used for creating a static lib target, `<projectName>_lib`. 
		*  This static lib will be linked with `src/main.cpp` to create the actual binary, `<projectName>`. 
		*  The static lib will be linked with `test/runner.cpp` to create the test binary, `<projectName>_test`.


Since the output is a standard CMake project, you can use it however you prefer interacting with CMake. I personally don't like to pollute my root with the temporary cache files that CMake creates, so I created a helper Python `build` script that manages everything in a git ignored `out` directory:

```sh
$ cd path/to/created/project
$ ./build -h
usage: build [-h] [-d] [-r] [-t]

builds the project

optional arguments:
  -h, --help     show this help message and exit
  -d, --debug    build the debug flavour instead of release
  -r, --rebuild  clean the flavour's temporary files and the build artifacts
                 before building
  -t, --test     perform the tests after building the specified flavour
  
$ ./build
$ ./out/release/bin/[projectName]
2 + 2 = 4
$ ./out/release/bin/[projectName]_test
===============================================================================
All tests passed (3 assertions in 1 test case)

```