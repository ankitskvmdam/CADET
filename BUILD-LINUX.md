# Prerequisites

* CMake (>= 3.1.0)
* GCC >= 4.7 or Clang >= 3.3
* Optional: MATLAB R2009a or greater
* Optional: Git

Assumed directory structure:

<pre>
&lt;ROOT&gt;
   |-libs
   |   |- sundials
   |   |- hdf5
   |-code
   |-cadet
   |-build
</pre>

Note that the version numbers of the files and packages below are subject to change and will not always reflect the most recent version.

# Build dependencies

## HDF5

You can either build HDF5 yourself or rely on the packages provided by your Linux distribution.

Obtain HDF5 from your distribution:
* Install the development package of your distribution (e.g., `libhdf5-dev` for Ubuntu and Debian, `hdf5-devel` OpenSUSE)
* Additional postfixes or prefixes in the library names (e.g., Debian uses `libhdf5_serial.*` instead of `libhdf5.*`) can cause troubles when CMake is trying to find HDF5. If this is the case, you can circumvent this by using symlinks `ln -s libhdf5_serial.* libhdf5.*`. However, this can later cause troubles because other packages might contain this file.

Build HDF5 yourself:
* Download CMake-enabled source from https://support.hdfgroup.org/HDF5/release/cmakebuild.html or https://support.hdfgroup.org/HDF5/release/cmakebuild5110.html
* Unzip and make sure that the directory path does not contain blank spaces
* Open a terminal and change to the unzipped directory
* Execute `ctest -S HDF5config.cmake,BUILD_GENERATOR=Unix,INSTALLDIR="<ROOT>/Libs/hdf5" -C Release -V`
* Extract the created `HDF5-1.10.0-patch1-Linux.tar.gz` file to `<ROOT>/Libs/hdf5` such that you have `<ROOT>/Libs/hdf5/lib`

## SUNDIALS

* Download SUNDIALS source from http://computation.llnl.gov/projects/sundials/sundials-software
* Unzip
* Open a terminal and change to the parent directory of the unzipped directory
* Create a new folder `sundialsbuild` and change to it
* Execute `cmake -DCMAKE_INSTALL_PREFIX="<ROOT>/Libs/sundials" -DEXAMPLES_ENABLE=OFF -DOPENMP_ENABLE=ON -DBUILD_SHARED_LIBS=OFF -DCMAKE_C_FLAGS=-fPIC ../sundials-2.7.0/`
* Execute `make install`
* Delete the folder `sundialsbuild` (e.g., execute `rm -rf sundialsbuild` in the parent directory of `sundialsbuild`)

## LAPACK

You can either use a LAPACK implementation provided by your distribution or install the freely available [Intel MKL](https://software.intel.com/sites/campaigns/nest/) which is very fast and probably faster than your distribution's implementation.

Obtain LAPACK from your distribution:
* Install the packages (LAPACK and BLAS) of your distribution (e.g., `liblapack3`, `liblapack-dev`, `libblas3`, `libblas-dev` for Ubuntu and Debian). Note that some packages only provide reference (i.e., slow) implementations and others (e.g., ATLAS, GOTO) perform much faster.

# Build CADET

* Download release of CADET or checkout from git
* Place the source in `<ROOT>/code` and create the directory `<ROOT>/build`
* Open a terminal and change to `<ROOT>/build`
* If you have built HDF5 yourself, execute `export HDF5_ROOT=<ROOT>/Libs/hdf5`
* Execute `export SUNDIALS_ROOT=<ROOT>/Libs/sundials`
* Using standard LAPACK: Execute `cmake -DCMAKE_INSTALL_PREFIX="<ROOT>/cadet" ../code/`
 
    Using MKL (sequential): Execute `cmake -DCMAKE_INSTALL_PREFIX="<ROOT>/cadet" -DCMAKE_LIBRARY_PATH="<MKL_ROOT>/lib/intel64" -DBLA_VENDOR=Intel10_64lp_seq ../code/`
 
    Using MKL (parallel): Execute `cmake -DCMAKE_INSTALL_PREFIX="<ROOT>/cadet" -DCMAKE_LIBRARY_PATH="<MKL_ROOT>/lib/intel64" -DBLA_VENDOR=Intel10_64lp ../code/`
* If CMake was not able to locate Matlab, execute `export MATLAB_ROOT=/path/to/my/MATLAB/R2016a` and try to run CMake again
* Execute `make`
* Execute `make install`

