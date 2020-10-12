How to get exact exchange (EXX) energy?
=======================================
Our KPM-RPA code provides us the RPA correlation energy E_RPA. 
To get the total energy of the systems, we still need other energy 
components, e.g., Kohn-Sham kinetic energy, Hartree energy, 
exact-exchange (EXX) energy, and external energy. How to get them? 


Path to the code 
================
/gpfs/home/chuang3/codes/abinit_KPM_RPA/abinit-7.10.4_kpm-just-compute-exx



A sample job
============
  
------------  ABINIT INPUT FILE  ----------------  
nsym 1  
istwfk 1  
  
ecut  400 eV  
acell 10.0 8.0 8.0 angstrom  
rprim
1.0 0.0 0.0  
0.0 1.0 0.0  
0.0 0.0 1.0  

ngkpt 1  1  1  
nshiftk 1   
shiftk  
0 0 0  
occopt 3  
tsmear 0.01 eV  
  
ntypat 1  
znucl  7  
natom  2  
typat 1 1  
xangst  
  5.0   4.000   4.0  
  6.1   4.0     4.0  
nband  10  
nbdbuf 2  
nstep   100  
toldfe  1.0d-5  
iscf  17  
------------------- END OF ABINIT INPUT FILE -----------------------  
  
  
  

rpa_kpm.ini file 
================
This program needs the rpa_kpm.ini file in which we type "exx" in  
the first line to let the program compute EXX energy.  
The numbers listed in the "manual" section are not important for EXX calculations.  
----------------------- rpa_kpm.ini file ----------------------------  
exx            ! eig_min: compute eigenvalues, eig_all: compute neig eigenvalues, kpm: compute RPA energy  
123            ! rand_seed  
30             ! # of moments  
1000           ! # of random vectors  
12             ! # of eigenvalues to compute  
2              ! eig_buffer  
16             ! # of freq  
10.0           ! max freq  
manual  
-1.41785659504  
-0.345503807374  
-0.164477817557  
-0.0971551665653  
-0.0580692666892  
-0.036736655213  
-0.0247642215758  
-0.0177154525144  
-0.0135639625544  
-0.0107932449497  
-0.00890626442533  
-0.00760904754274  
-0.00672287444342  
-0.00613085567429  
-0.00575848899419  
-0.0055632821038  
--------------------- end of rpa_kpm.ini file -------------------------------




job submission script 
=====================
Run this ABINIT input file with our modified ABINIT program which is located here  
/gpfs/home/chuang3/codes/abinit_KPM_RPA/abinit-7.10.4_kpm/src/98_main/abinit  
Run the job with 1 CPU. The sample job.s file is given  
---------------- Job.s file --------------------------  
#!/bin/bash  
#SBATCH -J "EXX_job"  
#SBATCH --constrain="intel"  
#SBATCH -p huang_q  
#SBATCH --ntasks=1  
#SBATCH --time=4:00:00  
#SBATCH -o output-%J-%a.o  
#SBATCH -e output-%J-%a.e  
module purge  
module load gnu-openmpi  
ulimit -s unlimited  
cd $SLURM_SUBMIT_DIR/  
srun /gpfs/home/chuang3/codes/abinit_KPM_RPA/abinit-7.10.4_kpm-just-compute-exx/src/98_main/abinit  < g.files >log &  
wait  
----------------- end of job.s file ---------------------  





Results & Outputs
=================

Check your results in kpm.log.0 file, in which we have the system XC energy  
(using other low-level, fast XC functionals) and EXX energy.  
"Etotal - XC + EXX" gives the energy to be added with RPA correlation energy  
to obtain the total energy.  
  
-------------------------------------------------------------------------------  
Solving KS-DFT in scfcv()...  
iter:   1   after vtorho() => etotal:     -19.58123552  e(1):   -1.054444  
iter:   2   after vtorho() => etotal:     -19.64660131  e(1):   -1.008939  
iter:   3   after vtorho() => etotal:     -19.64488608  e(1):   -1.040728  
iter:   4   after vtorho() => etotal:     -19.64503688  e(1):   -1.040868  
iter:   5   after vtorho() => etotal:     -19.64498670  e(1):   -1.041768  
iter:   6   after vtorho() => etotal:     -19.64502107  e(1):   -1.043914  
iter:   7   after vtorho() => etotal:     -19.64505531  e(1):   -1.045767  
iter:   8   after vtorho() => etotal:     -19.64505562  e(1):   -1.047028  
(scfcv.F90) KPM start. myrank=    0  
  
XC energy=       -4.85305422  
EXX energy=      -4.44403266  
Etotal - XC + EXX=    -19.23603175   (This is to be added to RPA correlation energy to  
                                      get the total energy)  
   
KPM-RPA is done. Cheerio!  
