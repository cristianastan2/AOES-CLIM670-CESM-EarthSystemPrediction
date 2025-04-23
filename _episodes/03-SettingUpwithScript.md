---
title: "An Initialized Prediction Experiment"
questions:
- "How to setup an Initialized Prediction Experiment"
- "How to setup an experiment with your own script"
objectives:
keypoints:
- ""
---

## An Initialized Prediction Experiment

We will run a subseasonal initialized prediction experiment with CESM.  A whole suite of these experiments have been run with CESM1 and CESM2 following a protocol set forth by [SubX](http://cola.gmu.edu/subx/) which is a multi-model subseasonal prediction experiment.  Many modeling groups ran a suite of hindcasts for subseasonal prediction following this protocol so they could all be compared. A paper about it is [here](https://journals-ametsoc-org.mutex.gmu.edu/bams/article/100/10/2043/344809)

The experiment will be initialized on Jan 7, 2019 and run for 45 days.

#### Which version of the model?

We will use CESM2.1.  This is a little different than the one we have been using (CESM2.1.5).  It is located in `/glade/u/home/ssfcst/cesm2_1`

#### Which Resolution?

We will use f09_g17 (about 1x1deg). 

####  Which compset?

We will run with all components active, so a `B` compset.
The correct GHGs will need to be defined and this is also handled by the compset.  For dates initialized before 2014, we use the `BHIST` compset.  For dates initialized in 2014 or later, we use the `BSSP585` compset (this is nearly equivalent to RCP8.5 scenario).  GHGs in 2014 are projected based on this scenario.

Since we will initialize on Jan 7, 2019, we will use the `BSSP585` compset.

#### Namelist Configurations

To meet the output requirements of the SubX protocol, we will want to set the namelists to give us daily data for many variables and some additional variables that are not default in CESM.  The namelist files can be copied from my experiment directory: `~cstan/cases/s2sfcst/user_nl_*`

#### Initial Conditions

It is not easy or advisable to make your own initial conditions for each component of the model.  If you want initial conditions, you need to get them from someone at NCAR.  The [Earth System Prediction Working Group](https://www.cesm.ucar.edu/working-groups/earth-system) is the best source.

The experiment will be a hybrid run (`RUN_TYPE=hybrid`) in which the initial conditions are staged by the user (`GET_REFCASE=FALSE`).  They are located in:

`/glade/derecho/scratch/cstan/fcst/rest/2019-01-07/`

~~~
$ ls /glade/scratch/cstan/fcst/rest/2019-01-07/
~~~
{: .language-bash}

~~~
b.e21.f09_g17.cam.i.2019-01-07-00000.nc
I2000Clm50BgcCrop.002runRealtime.cism.r.2019-01-07-00000.nc
I2000Clm50BgcCrop.002runRealtime.clm2.h0.2019-01-01-00000.nc
I2000Clm50BgcCrop.002runRealtime.clm2.h1.2019-01-01-00000.nc
I2000Clm50BgcCrop.002runRealtime.clm2.r.2019-01-07-00000.nc
I2000Clm50BgcCrop.002runRealtime.clm2.rh0.2019-01-07-00000.nc
I2000Clm50BgcCrop.002runRealtime.clm2.rh1.2019-01-07-00000.nc
I2000Clm50BgcCrop.002runRealtime.cpl.r.2019-01-07-00000.nc
I2000Clm50BgcCrop.002runRealtime.mosart.h0.2018-12.nc
I2000Clm50BgcCrop.002runRealtime.mosart.h1.2019-01-01-00000.nc
I2000Clm50BgcCrop.002runRealtime.mosart.r.2019-01-07-00000.nc
I2000Clm50BgcCrop.002runRealtime.mosart.rh0.2019-01-07-00000.nc
I2000Clm50BgcCrop.002runRealtime.mosart.rh1.2019-01-07-00000.nc
rpointer.atm
rpointer.drv
rpointer.glc
rpointer.ice
rpointer.lnd
rpointer.ocn.ovf
rpointer.ocn.restart
rpointer.ocn.tavg
rpointer.rof
~~~
{: .output}

The model will look for restart files that begin with the name `RUN_REFCASE`, but our files don't all begin with that name.  We will need to copy them to our run directory and change their name so the model will find them. We will change most of them to `b.e21.f09_g17`

#### SourceMods

There are changes to source code in this run.  We will need to copy the new SourceMods from my experiment directory `~cstan/cases/s2sfcst/SourceMods/` to the SourceMods in the experiment directory.

#### Configuration changes in `env_run.xml`

RUN_TYPE=hybrid

RUN_STARTDATE=2019-01-07

RUN_REFDATE=2019-01-07

RUN_REFCASE=b.e21.f09_g17

GET_REFCASE=FALSE

STOP_OPTION=ndays

STOP_N=45

Specific to this run configuration (these were obtained from NCAR and are not something you would know offhand):

./xmlchange --force CLM_NAMELIST_OPTS=use_init_interp=.true.

./xmlchange CCSM_BGC=CO2A

./xmlchange OCN_CHL_TYPE=diagnostic

./xmlchange OCN_TRACER_MODULES=iage 

Since the run is short, we can reduce the wallclock times the run and archiving:

./xmlchange JOB_WALLCLOCK_TIME=01:00:00

./xmlchange --subgroup case.st_archive JOB_WALLCLOCK_TIME=01:00:00

## Setup Experiment with a script

Go to your home directory and create a scripts directory.

Go to your scripts directory, create a file called `s2sfcsts.sh` and open it in your preferred editor..

The `.sh` indicates that we will write a `bash` shell script.  A bash shell script is a collection of Unix commands combined with a set of syntax rules for assigning variables and making loops and conditionals.   

*Note: The script we got from Dr. Swenson for the added heading experiments was a `c-shell` script.  It is also a collection of Unix commands, but it has a different set of syntax rules for assigning variables and making loops and conditionals.*

Which to use is a matter of personal preference.  You will likely see bash shell (`.sh`) more often than c-shell (`.csh`), so I will show you how to write a bash shell script.

We will write the script together.  A copy is located here on Cheyenne for reference: `~cstan/scripts/s2sfcsts.sh`

Once your script is ready, you can make is executable.

~~~
$ chmod +x s2sfcsts.sh
~~~
{: .language-bash}

and run it

~~~
$ ./s2sfcsts.sh
~~~
{: .language-bash}


It will take a few minutes to run since it has to do everything so setup  and build the model.  If it fails to build, you will get an error.

Want to learn more about unix commands and shell scripting?  See the [Software carpentry tutorial](http://swcarpentry.github.io/shell-novice/)
