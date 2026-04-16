# Building Noah-OWP-Modular with CMake

Noah-OWP-Modular can be built as a standalone executable or a shared library. The CMakelists.txt file supports both of these options.

## Running in Standalone
The following describes how to install the run noah-owp-modular as a standalone model.

Clone repository if necessary and change to the repo root directory:
```bash
git clone https://github.com/NOAA-OWP/noah-owp-modular.git
cd noah-owp-modular
```

The Standalone version of noah-owp-modular requires Netcdf for FORTRAN. This may be an installed resource, or provided by a module, depending on the platform.

The path to the Netcdf library is passed to CMake using the NETCDF environment variable;
```bash
export NETCDF=<path to netcdf directory>
```
Generate a CMake build system and directory (in the example below and those that follow, the directory is assumed to be `cmake_build` within the repo root):
```bash
# You can also include the '-DCMAKE_BUILD_TYPE=Debug' option if you want to build for debugging
cmake -B cmake_build -S .
```

Note that you may need or want to specify the Fortran compiler, done by supplying a value for the `FC` variable when you generate the build directory.  There are many reasons for that:  maybe you have multiple compilers installed, or maybe your compiler isn't installed to the standard system path or using a standard name CMake will recognize.

Regardless, if necessary just append `FC=<path_to_compiler>` to the rest of the command:

```bash
# Here we are manually telling CMake to use '/opt/local/bin/gfortran-mp-14' as the Fortran compiler, rather than 
# whatever compiler it would find on its own.
FC=/opt/local/bin/gfortran-mp-14 cmake -B cmake_build -S .
```

With the build directory generated, build the stand-alone executable:
```bash
cmake --build cmake_build --target noah_owp_modular.exe
```

You should now see the `cmake_build/noah_owp_modular.exe` stand-alone executable. 

You can test the executable using the namelist.input file from the run subdirectory;
```bash
cd run
../cmake_build/noah_owp_modular.exe namelist.input
```

You can examine model output in the `/data/output.nc` file (requires [Panoply](https://www.giss.nasa.gov/tools/panoply/) or other NetCDF viewer).

## Building the Shared Library

The noah-owp-modular model code can also be built as a shared library, accessible using BNI. In particular, this is necessary to run the noah-owp-modular model in the [Next Generation Water Resources Modeling Framework](https://github.com/NOAA-OWP/ngen).

As described above for stand-alone builds, clone the repo if necessary and change into the repo root directory;
```bash
git clone https://github.com/NOAA-OWP/noah-owp-modular.git
cd noah-owp-modular
```

The shared library requires the iso_c_fortran_bmi bindings (https://github.com/NOAA-OWP/iso_c_fortran_bmi). There are two ways to provide this to CMake. It can simply be placed in a directory at the same level as the noah-owp-modular repo, like this;
```bash
>$ ls
iso_c_fortran_bmi  noah-owp-modular 
```
The CMake build will find it automatically;
```bash
cmake -B cmake_build 
```
 
Alternatively, the path can be passed to CMake on the command line;
```bash
cmake -B cmake_build -DISO_C_FORTRAN_BMI_PATH=<path to iso_c_fortran_bmi directory>
```

To build the library;
```bash
cmake --build cmake_build --target noah_owp_modular_bmi
```

When the iso_c_fortran_bmi bindings are provided, CMake will also prepare to build the bmi test program;
To build the test program;
```bash
cmake --build cmake_build --target noah_owp_testbmi.exe
```

To run the test;
```bash
cd run
../cmake_build/noah_owp_testbmi.exe
```
