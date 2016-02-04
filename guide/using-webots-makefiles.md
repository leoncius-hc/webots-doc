## Using Webots Makefiles

### What are Makefiles

The compilation of Webots C/C++ and Java controllers can be configured in the
provided Makefiles. A controller's Makefile is a configuration file used by the
`make` utility and that optionally specifies a list of source files and how they
will be compiled and linked to create the executable program.

Note that Python and `MATLAB` are interpreted languages and therefore they don't
need Makefiles. So if you are using any of these programming languages or Visual
C++ then you can ignore this section.

When using C/C++ or Java, the presence of a Makefile in the controller directory
is necessary. If the Makefile is missing Webots will automatically propose to
create one. This Makefile can be modified with a text editor; its purpose is to
define project specific variables and to include the global "Makefile.include"
file. The global "Makefile.include" file is stored in "WEBOTS_HOME/resources/"
directory; it contains the effective build rules and may vary with the Webots
version. Note that Webots Makefiles are platform and language independent.

### Controller with Several Source Files (C/C++)

If a controller requires several C/C++ source files they need to be specified in
the Makefile. The name of each source file must be listed, using one of these
variables:

| Variable | Usage |
| --- | --- |
| C_SOURCES | Specifies a list of .c sources files |
| CXX_SOURCES | Specifies a list of .cpp, *.cc or *.c++ source files |

Every source file specified using these variables, will be added to the
controller build. In addition dependency files will be automatically generated
by the `make` command in order to minimize the build. Note that these variables
should not be used in any language other than C or C++.


For example, if a controller has several ".c" source files, then this can be specified like this in the controller's Makefile:

```
C_SOURCES = my_controller.c my_second_file.c my_third_file.c
```

If a project has several ".cpp" source files, then this can be specified like this:

```
CXX_SOURCES = my_controller.cpp my_second_file.cpp my_third_file.cc
```

Important: the build rules require that one of the source files in the list must correspond to the controller name (i.e. controller directory name),
e.g. if the controller directory is "my_controller" then the list must contain either "my_controller.c, my_controller.cpp" or "my_controller.cc" accordingly.


### Using the Compiler and Linker Flags (C/C++)

These two variables can be used to pass flags to the gcc compiler or linker.

| Variable | Usage |
| --- | --- |
| CFLAGS | Specifies a list of flags that will be passed to the gcc/g++ compiler |
| INCLUDE | Specifies a list of flags that will be passed to the gcc/g++ compiler |
| LIBRARIES | Specifies a list of flags that will be passed to the linker |

#### Adding an External Library (C/C++)

Webots C/C++ controllers are regular binary executable files that can easily be
compiled and linked with external libraries. To add an external library it is
only necessary to specify the path to the header files, and the path and name of
the library in the controller's Makefile. For example the `-I`*dir* flag can be
used to add a directory to search for include files. The LIBRARIES variable can
be used to pass flags to the linker. For example the `-L`*dir* flag can be used
to add a directory to search for static or dynamic libraries, and the `-l` flag
can be used to specify the name of a library that needs to be linked with the
controller.


For example, let's assume that you would like to add an external library called *XYZLib*.
And let's assume that the library's header files and ".dll" file are located like this (Windows):

```
C:\Users\YourName\XYZLib\include\XYZLib.h
C:\Users\YourName\XYZLib\lib\XYZLib.dll
```

Then here is how this should be specified in the Makefile:

```
INCLUDE = -I"C:\Users\YourName\XYZLib\include"
LIBRARIES = -L"C:\Users\YourName\XYZLib\lib" -lXYZLib
```

The first line tells gcc where to look for the *#includeltXYZLib.hgt* file.
The second line tells gcc to link the executable controller with the "XYZLib.dll" and where that ".dll" can be found.
Note that this would be similar on Linux and Mac OS X, you would just need to use UNIX-compatible paths instead.
If more external libraries are required, it is always possible to use additional `-I, -L` and `-l` flags.
For more information on these flags, please refer to the `gcc` man page.


#### Using Webots C API in a C++ Controller


Normally, C++ controllers use Webots C++ API.
The C++ API is a set of C++ classes provided by C++ header files, e.g. `#include ltwebots/Robot.hppgt`.
If you prefer, C++ controllers can use Webots C API instead.
The C API is a set of C functions starting with the `wb` prefix and provided by C header files, e.g. `#include ltwebots/robot.hgt`.
To use the C API in a C++ controller you need to add this line in your controller Makefile:

```
USE_C_API = true
```



#### Adding Debug Information


If you need to debug your controller, you need to recompile it with the `debug` target from a terminal:

```
make debug
```

This will instruct gcc to add debugging information so that the executable can be debugged using gcc.

