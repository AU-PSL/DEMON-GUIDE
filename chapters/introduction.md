# DEMON GUIDE

DEMON stands for the Dynamic Exploration of Microparticle clouds Optimized Numerically and is a 2D molecular dynamics simulator for dusty plasma systems. This guide intends to demystify several quircks to DEMON installation, usage, and modification such that future students and researchers can use DEMON for any purpose they could need. 

For the most part, there are only a few difficult sections of the demon codebase and each will be mentioned below

### Building with CMake

The DEMON code uses CMake to build, and a short guide for building DEMON can be found in `README.TXT`. Here, we attempt to privide a guide to modifying the build scripts in the future. For the most part, DEMON uses will build all the files in the `CMakeLists.txt` file. Adding a new file to build into DEMON is as simple as adding the new files to the `demon_sources` list in that file. 

Adding binaries is also straightforward and simply requires adding the executable with the `add_executable`, `add_dependencies`, and `target_link_libraries` commands at the end of the file. Here's how they work:

* `add_executable` - a function that takes two arguments separated by a single space: The name of the output binary and the file that holds that binary's `main` function.

* `add_dependencies` - A function that takes two arguments separated by a single space: The name of the output binary and the simulation library defined with a previous `add_library` command that looks like this ``` add_library (simulation STATIC ${demon_sources})```

* `target_link_libraries` - A function that takes multiple arguments, all separated by a single space: The name of the output binary, the name of the simulation library, and the name of any additional libraries needed for the code to compile. Most commonly this is `${CFITSIO_LIB}`.

### Commandline parsing

Commanline parsing in DEMON can be found in `driver.cpp` and is a little messy. It can primarily be found in the `parseCommandLineOptions(...)` function. All demon flags can be found by using the `./DEMON -h` flag and (at the time of writing) looks like this: 

```
                                      DEMON
        Dynamic Exploration of Microparticle clouds Optimized Numerically

Options:

 -B 1.0                 set magnitude of B-field in z-direction [T]
 -c noDefault.fits      continue run from file
 -C 100.0               set confinementConst [V/m^2]
 -D -1.0 10.0           use TimeVaryingDragForce; set scale [Hz/s], offset [Hz]
 -d 2200                set dust density [kg/m^-3]
 -E 100 10              set Electric field strength [V/m]; decay constant [m]
 -e 5.0                 set simulation end time [s]
 -f noDefault.fits      use final positions and velocities from file
 -F 100 1               set verticalElectricField and top and bottom positions
 -g 10.0                set dragGamma (magnitute of drag constant) [Hz]
 -G 0.0                 set Gravitational field strength [m/s^2]
 -h                     display Help (instead of running)
 -I                     use 2nd order Runge-Kutta integrator
 -k 0 0                 kick the particles in the x;y directions [m/s]
 -i 0.003               set initial inter-particle spacing [m]
 -L 0.001 1E-14 1E-14   use ThermalForceLocalized; set radius [m], in,out
                        thermal values [N]
 -M 0.2 100             create Mach Cone; set bullet velocity [m/s], mass factor
 -n 8                   set number of particles
 -o 0.01                set the data Output time step [s]
 -O data.fits           set the name of the output file
 -P Parameters.cfg      Read parameters from file
 -p 0 0                 set initial x;y positions [m] of cloud
 -q 6000.0 100.0        set charge mean and sigma [c]
 -R 100.0 1000.0        use RectConfinementForce; set confineConstX,Y [V/m^2]
 -r 1.45E-6 0.0         set mean particle radius and sigma [m]
 -s 2E4                 set coulomb shielding constant [m^-1]
 -S 1E-15 0.005 0.007   use RotationalForce; set strength [N], rmin, rmax [m]
 -t 0.0001              set the simulation time step [s]
 -T 1E-14               use ThermalForce; set thermal reduction factor [N]
 -v 1E-14 0.0           use TimeVaryingThermalForce; set scale [N/s]
                        and offset [N]
 -V 0.4                 use ConfinementForceVoid; set void decay constant [m^-1]
 -w 1E-13 0.007 0.00001 use DrivingForce; set amplitude [N], shift [m],
                        driveConst [m^-2]

Notes: 

 Parameters specified above represent the default values and accepted type,
    with the exception of -c and -f, for which there are no default values.
 -c appends to file; ignores all force flags (use -f to run with different
    forces). -c overrides -f if both are specified
 -D uses strengthening drag if scale > 0, weakening drag if scale < 0.
 -M is best used by loading up a previous cloud that has reached equilibrium.
 -n expects even number, else will add 1 (required for SIMD).
 -S creates a shear layer between rmin = cloudsize/2 and
    rmax = rmin + cloudsize/5.
 -T runs with heat; otherwise, runs cold.
 -v uses increases temp if scale > 0, decreasing temp if scale < 0.
 -w creates acoustic waves along the x-axis (best with -R).
 -E is set to 0 0 initially. If you would like to run DEMON with an
    Electric force, you may also want to turn the Confinement Force to 0.
```

As a note: The commandline parser has a `-P` flag that allows all of the variables to be set by a Params.cfg file instead of manually setting them each run. This should help ensuring DEMON runs are consistent.

### OpenMP

DEMON runs in parallel with the help of OpenMP. Unfortunately, when we initially implemented OpenMP support, we limited the number of particles allowed in the simulation to multiples of 4. There is not a good reason with modern OpenMP to do this, but it was worth mentioning here that there is paralellization that needs to be taken into account for each force.

### FITS
DEMON stores it's data in a multidimensional file format known as FITS (originally used for astrophysics applications). If we are honest, there are better options available nowadays for the data storage (hdf5); however, we chose FITS and are sticking with it! 

The easiest way to understand how the FITS files work is to go to the `example/` directory and run `DEMON -P Params_vortex.cfg` and then `read_data.py` (requires `astropy.io`. Inside `read_data.py` there is a brief description of how DEMON uses the FITS format to store data. In addition, there is a small tutorial on how to pull precise information from the data.fits file that is created.

For the most part, FITS files separate data into `hdu`'s. To be completely honest, I do not know what that stands for; however, DEMON has 3 of them:

* `PRIMARY`: This does not hold too much information relevant to the simulation

* `CLOUD`: This holds all details about the clout, itself, including most of the parameters read in from the commandline parser

* `TIME_STEP`: This is where all the data for each individual particle is held in a 2 or 3 dimensional array. Here are examples on how you might use the `TIME_STEP` hdu: 
```
data[dt][0]     -- time at dt timestep
data[dt][1][n]  -- x position of particle n at dt timestep
data[dt][2][n]  -- y position of particle n at dt timestep
data[dt][3][n]  -- x velocity of particle n at dt timestep
data[dt][4][n]  -- y velocity of particle n at dt timestep
```

To be honest, the FITS file format is a little hard to deal with, but there are definitely benefits.
