# Adding new forces to DEMON

As mentioned in the introduction, DEMON was built in a modular way, thus the implementation of new forces is relatively straightforward.

All the forces in DEMON are held in `*Force.*` files in the main DEMON directory. These files are all quite similar and can basically be copied and pasted to and from each other. The cpp files have 3 main sections:

1. OpenMP directions at the start. These do not need to be modified too much, except changing the name of the force. As mentioned, these can use a little work in the future.

2. The `force(...)` function. This must be modified with the formula of your choosing. Here, you will have access to any data that is stored in the `cloud` data structure.

3. The `writeForce(...)` and `readForce(...)` functions. These again do not need to be heavily modified. Just change the names and you should be good to go.

More information will come on these in the future; however, this should probably come after refactoring the DEMON code, simply because each force file has a large amount of unnecessary code (between the OpenMP calls at the top and redundant functions at the bottom). That said, we have not had the time to clean things up yet.

The hardest part of adding a new force is updating the driver flags to read in any necessary variables for the force to function. This is done in the `parseCommandLineOptions(...)` function in `main(...)`. 
