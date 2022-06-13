## Build and Install Instructions
---

### Prerequisites
A supported Fortran compiler (see table below).  Other versions may work, in particular if close to the versions listed below.

| Compiler vendor | Supported (tested) versions                                |
|-----------------|------------------------------------------------------------|
| Intel           | 18.0.3.222 and above                                       |
| GNU             | 10.3.0 and above                                           |

A supported MPI library (see table below).  Other versions may work, in particular if close to the versions listed below.

| MPI library     | Supported (tested) versions                                |
|-----------------|------------------------------------------------------------|
| MPICH           | 3.3.1 and above                                            |
| Open MPI        | 3.1.5 and above                                            |
| Intel MPI       | 2018.0.4 and above                                         |

Third-party libraries (TPL) compiled with the same compiler and MPI library (where applicable).

| Library         | Supported (tested) versions                                |
|-----------------|------------------------------------------------------------|
| CMake           | 3.20.1 and above                                           |
| NetCDF-Fortran  | 4.5.2 and above                                            |

### Building the GSI NetCDF Diagnostic Library

`CMake` employs an out-of-source build.  Create a directory for configuring the build and cd into it:

```bash
mkdir -p build && cd build
```

Set the compilers, if needed, to match those being used for compiling the TPL listed above: `FC` environment variable can be used to point to the desired Fortran compiler.

Execute `cmake` from inside your build directory.

```bash
cmake -DCMAKE_INSTALL_PREFIX=<install-prefix> <CMAKE_OPTIONS> /path/to/GSI-ncdiag-source
```

If the dependencies are not located in a path recognized by `cmake` e.g. `/usr/local`, it may be necessary to provide the appropriate environment variables e.g. `<package_ROOT>` or `CMAKE_PREFIX_PATH` so that `cmake` is able to locate these dependencies.

The installation prefix for GSI-ncdiag library is provided by the `cmake` command-line argument `-DCMAKE_INSTALL_PREFIX=<install-prefix>`

To build and install:

```
make -j<x>
make install
```

### CMake Options

CMake allows for various options that can be specified on the command line via `-DCMAKE_OPTION=VALUE` or from within the ccmake gui. The list of options currently available is as follows:

| Option                 | Description (Default)                                |
|------------------------|------------------------------------------------------|
| `ENABLE_NCDIAG_SERIAL` | Additionally build serial `ncdiag` library (`ON`)    |
