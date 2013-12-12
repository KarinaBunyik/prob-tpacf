prob-tpacf
==========

Fast two-point correlation code with probabilistic weighting.
Correlation Code - v.0.1
Based on Dolence & Brunner TPACF code

(Brief) Instructions:

***********************
***** 1. Building *****
***********************

Change the "make.config" file to reflect the compiler(s) and flags appropriate to your machine and "params.h" to set the build options.

Type "make all"

If the build was successful, the following files should be located in the "bin" directory:

precompute
correlate
estimate

To build specific programs, running make with no arguments gives a list of targets.

To clean the build, type "make clean"


***********************
***** 2. Testing ******
***********************

After compiling the code, it's important to verify that the code is functioning.  Test data and correct outputs can be found in the test directory.  If the code has been compiled to run serially, simply enter the test directory and run the "runserial.sh" script.  This script automates the precomputing and bin counting phases described below and checks the results against the correct answers, reporting if any problems were detected.  If the code is to be run in parallel, in particular through a batch queue, you'll need to run the code appropriately for your system, then use the "compare.sh" script which simply checks the output of the parallel run with the correct answers and reports any problems.


***********************
***** 3. Usage ********
***********************

Computing a correlation involves 3 steps: precomputing, counting pairs, and estimating the correlation from those counts.


**** Precomputing *****

Source data files should be plain text and stored as "RA DEC [z ...]", one source per line.
A file should be created that lists each data file, one per line (just use ls data* > filelist).  For an autocorrelation, the source file should be listed first followed by the randoms.  For a cross-correlation, the two files should be ...

Now run precompute with the file list, angular/spatial flag (0 for angular, 1 for spatial), and, optionally, the number of jackknife samples (default set in "params.h") as command line arguments.

Ex.	./precompute filelist 0 32

for an angular calculation, or

Ex.	./precompute filelist 1 16

for a spatial calculation.

Depending on the build parameters in params.h and the size of the data, 2 or more files should be created for each data file.  One (or more) should have .bin and likewise for .tree.


**** Getting bin counts ******

Create a parameter file for the correlation run using the example in the samples directory.  See Dolence & Brunner (2008) for details on the work level parameter.
To run the code, be sure that the working directory of the program contains the *bin and *tree files generated from precomputing, the parameter file which is the command line argument, and the file list used in precomputing.

SERIAL:
	./correlate params.file

PARALLEL WITH OPENMP:
	After building the code with OpenMP enabled (both in params.h and make.config), set the environment variable OMP_NUM_THREADS to the desired number of processes then run the code as if it were serial.  This may be different if your system has a batch queue.

PARALLEL WITH MPI:
	Follow the instructions for your system.

PARALLEL WITH MPI/OPENMP:
	See above.

Once complete, there should be files ending in *bins which contain the unnormalized bin counts and information about the calculation required for estimating the correlation function.


**** Estimation *****

Work in progress....


****************************************************************************

From raw data to the final correlation estimation, the process is as follows:

(1) In the directory where the data resides, generate a file that lists the names of the data files, one per line.  The "source" file should be first in the list, followed by the targets being compared against.

(2) Run the precompute code with the file list and angular/spatial flag as a command line arguments, as well as the desired number of jackknife samples if different from the default.  If no jackknife sample number is supplied, the code defaults to 16 as set in "params.h".  NOTE:  the number of jackknife samples MUST be a power of 2.

(3) Create a parameter file for the correlation code.  Samples can be found in the "samples" directory.

(4) Run the correlation code with the parameter file as a command line argument.

(5) Create a parameter file for the estimation code.  Again, a sample can be found in the "samples"  directory.

(6) Run the estimation code with the parameter file as a command line argument.

DONE!
