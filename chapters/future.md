# Future Work

As with any open source project, DEMON has plenty of potential for growth; however, due to the somewhat sporadic nature of DEMON development, there have been many proposed projects that have failed to move forward (primarily due to time constraints).

The following is a list of all known avenues of future DEMON development, along with a brief description of how to proceed with the project.

### DEMON refactoring
DEMON is an open-source project and has been touched by many individuals, each of which have different practices and ideas about how to make the code match their personal style. Because of this, there are plenty of admittedly ugly bits in the DEMON code where we seem to flip between tabs and spaces or our naming conventions are improper. This is most obvious in the `driver.cpp` file, which is the longest file and also holds the heart and soul of DEMON (including command-line parsing). 

Certain additional projects should also be removed (Such as ANGEL, which did not work as intended and has a much simpler python version in the `example/` directory). Additionally, our OpenMP implementation for parallelization could use some work. This would make each force (and the code, itself) more efficient, especially on computing clusters

The commandline parser also needs a little work. Ideally, the help command is not created in c++, but instead resides in another file (maybe simply called `helpfile`). This file can be freely modified. Rather than using an incredily long cout statement in the -h flag, we simply need to read the file and output it's contents line-by-line. 

### Gridding

After the surprising findings of the MDPX experiment, where dust grains seem to align themselves to an external grid in a magnetic field, we have the plans to use DEMON to simulate the movement of the particles within a "grid-like" electric field. This will be our focus in the immediate future.

### 3D DEMON

It would take some time to do 3D DEMON simulations; however, it is not impossible, nor does it seem difficult. With 3D DEMON simulations, we could lower the mass of each dist grain and give them all exactly 1 electron charge such that they simulate electron motion. We could then place a large dist grain in the simulation and watch how the electrons move. This would be a method to further simulate Brian Lynch's thesis. 
