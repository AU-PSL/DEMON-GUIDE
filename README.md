# DEMON-GUIDE
This is a guide on how to use the Dynamic Exploration of Microparticle clouds Optimized Numerically (DEMON) simulation code. In this guide, we will focus primarily on developers who desire to work with or on the DEMON codebase. For this reason, shorthand notation may be used frequently to describe methods common in the area of computer science; moreover, because DEMON is written in C++, a minimal understanding of C++ is required. Even so, we will attempt to write this documentation in a way that is simultaneously understandible and concise. 

If you have any questions, please feel free to e-mail us at any of the following e-mail addresses:

* jrs.schloss@gmail.com
* More to come!

### Requirements:

Intel CPU with SSE4.2 or higher support. 
Compiler with C++11 support and C++11 standard library support.
libcfitsio.a Avalible from http://heasarc.gsfc.nasa.gov/fitsio/

Before installing DEMON, be sure you have the cfitsio library installed from
here: http://heasarc.gsfc.nasa.gov/fitsio/fitsio.html

Also make sure you have CMake installed from here:
http://www.cmake.org/

A step by step installation guide for mac and linux devices will be in section 4

### 1. Using CMake:

DEMON requires that the cfitsio library and include paths be set with the
following command.

```
    cmake -DCFITSIO_INC_PATH=/path/to/include -DCFITSIO_LIB_PATH=/path/to/lib
```

extra build flags maybe included by setting

```
    cmake -DCMAKE_CXX_FLAGS="flags"
```

A optimized build maybe specifed by

```
    cmake -DCMAKE_BUILD_TYPE=Release
```

Cmake will generate a make file. The built executable can be found in `build/bin`

### 2. Using xcode:

In the xcode project, click on the Simulation project. In the "Build
Settings" Tab, under the "Search Paths" section add the path to the cfitsio 
header files and libcfitsio headers. Under the linking tab check to make sure 
"Other Linking Flags" is set to -lcfitsio.


### 3. Multicore Support on Linux:

For parallel processing support on linux, use the -fopenmp flag,

```
    cmake -DCMAKE_CXX_FLAGS="-fopenmp"
```


### 4. Step-by-step installation on linux and mac devices

Install cfitsio, if you use your distribution's software repository,
be sure to keep track of where the library and include paths are located.
Otherwise do the following:

```
    mkdir builds
    cd builds
    wget ftp://heasarc.gsfc.nasa.gov/software/fitsio/c/cfitsio3360.tar.gz
    tar xvzf cfitsio3360.tar.gz
    cd cfitsio
    ./configure
    make
    cd ..
```

The FFTAnalysis code requires fftw3 to be installed. This can be done
with the package manager, or it can be done through the command line
as follows:
```
    wget http://www.fftw.org/fftw-3.3.4.tar.gz
    tar xvzf fftw-3.3.4.tar.gz
    cd fftw-3.3.4
    ./configure
    make
    make install
```

Now, make sure that cmake is installed correctly. More than likely,
you can do this with your package manager, but if you insist on doing this
through the command line, do the following:

```
    wget http://www.cmake.org/files/v2.8/cmake-2.8.12.2.tar.gz
    tar xvzf cmake-2.8.12.2.tar.gz
    cd cmake-2.8.12.2
    ./bootstrap
    make
    make install
```

Now we can start to work with DEMON. If you want to use git, please use:

```
    git clone git@github.com:leios/demonsimulationcode.git 
    cmake -DCFITSIO_INC_PATH=../cfitsio -DCFITSIO_LIB_PATH=../lib
    make
```


Now DEMON should be on your system, with the executable in the following
directory: ~builds/demonsimulationcode-read-only/build/bin/ . Now we just need
to make this directory callable in any directory on your system. 
Do the following:

```
    pwd
```

Now place a path to that directory in your bashrc file. On Linux:

```
    echo export PATH=$PATH:/home/USER/builds/demonsimulationcode-read-only/build/bin >> ~/.bashrc 
```

On Mac:

```
    echo export PATH=$PATH:/home/USER/builds/demonsimulationcode-read-only/build/bin >> ~/.PROFILE
```

And there you have it, a full copy of DEMON on your machine!


### 5. Notes on github

DEMON is now available on github and can be modified with both svn and git.


### 6. Looking at Data

DEMON data will be stored in a fits file. Please use additional software, such 
as astropy.io to use this data. An example of how to use astropy.io can be found
in the example directory.
