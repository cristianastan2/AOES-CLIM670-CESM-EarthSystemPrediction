---
title: "Initializing the Model"
questions:
- "How to setup model initial conditions?"
objectives:
keypoints:
- ""
---

## Setting up Experiments - Review

To setup experiments with CESM, we setup and configure the following things:

* Compset to define what components are active
* Resolution
* Configuration changes in the `env_run.xml` file:
- where do initial conditions come from?   `RUN_TYPE`, `RUN_STARTDATE`, `RUN_REFDATE`, `RUN_REFCASE`, `GET_REFCASE`
- how long to run? `STOP_OPTION`, `STOP_N`, `RESTART_N`, `CONTINUE_RUN`
* Output and other component specific details in the namelist (`user_nl_xxx`) files
* Stage the initial conditions in the run directory if `GET_REFCASE=FALSE`
* SourceMods for any source code changes

## Understanding Initial Conditions in CESM

Initial conditions tell the model what state to start from.  The files that contain this information are called restart and intial condition files.  They represent an instantanous state of each model component at a given time. There are multiple restarts for each componenet of the model.  


#### Initial Conditions in our B1850 simulation (Assignment #3)

Let's take a look at the initial conditions we used for some of our previous experiments:

~~~
$ cd /glade/u/home/cstan/cases/b.day1.0
$ ./xmlquery RUN_TYPE,RUN_STARTDATE,RUN_REFDATE,RUN_REFCASE,GET_REFCASE,RUN_REFDIR
~~~
{: .language-bash}

~~~
Results in group run_begin_stop_restart
	RUN_TYPE: hybrid
	RUN_STARTDATE: 0001-01-01
	RUN_REFDATE: 0301-01-01
	RUN_REFCASE: b.e20.B1850.f19_g17.release_cesm2_1_0.020
	GET_REFCASE: TRUE
	RUN_REFDIR: cesm2_init
~~~
{: .output}

`RUN_TYPE=hybrid`, so this indicates the information in the other variables need to be set and that this run uses initial conditions from another CESM run to start it.

`GET_REFCASE=TRUE` means that the code will get the initial condition/restart files for you from the `RUN_REFDIR`. If it is FASLE, we have to "stage" (meaning copy) the initial condition/restart files in the case run directory ourselves. As an example, we did this in Assignment #4.

`RUN_REFDIR` is set to the default value of `cesm2_init`. This is a preset directory of initial condition files for starting CESM2 simulations. The full path is:
`/glade/p/cesmdata/inputdata/cesm2_init/`

`RUN_REFCASE` tells it which files in `RUN_REFDIR` to use.  Let's take a look:

~~~
$ ls /glade/p/cesmdata/inputdata/cesm2_init/b.e20.B1850.f19_g17.release_cesm2_1_0.020*
~~~
{: .language-bash}

~~~
0101-01-01  0161-01-01  0301-01-01
~~~
{: .output}

The `RUN_REFDATE` tells us which of these we are using (`0301-01-01`)
~~~
$ ls /glade/p/cesmdata/cseg/inputdata/cesm2_init/b.e20.B1850.f19_g17.release_cesm2_1_0.020/0301-01-01/
~~~
{: .language-bash}

~~~
b.e20.B1850.f19_g17.release_cesm2_1_0.020.cam.i.0301-01-01-00000.nc     b.e20.B1850.f19_g17.release_cesm2_1_0.020.ww3.r.0301-01-01-00000
b.e20.B1850.f19_g17.release_cesm2_1_0.020.cam.r.0301-01-01-00000.nc     rpointer.atm
b.e20.B1850.f19_g17.release_cesm2_1_0.020.cam.rs.0301-01-01-00000.nc    rpointer.drv
b.e20.B1850.f19_g17.release_cesm2_1_0.020.cice.r.0301-01-01-00000.nc    rpointer.glc
b.e20.B1850.f19_g17.release_cesm2_1_0.020.cism.r.0301-01-01-00000.nc    rpointer.ice
b.e20.B1850.f19_g17.release_cesm2_1_0.020.clm2.r.0301-01-01-00000.nc    rpointer.lnd
b.e20.B1850.f19_g17.release_cesm2_1_0.020.cpl.r.0301-01-01-00000.nc     rpointer.ocn.ovf
b.e20.B1850.f19_g17.release_cesm2_1_0.020.mosart.r.0301-01-01-00000.nc  rpointer.ocn.restart
b.e20.B1850.f19_g17.release_cesm2_1_0.020.pop.r.0301-01-01-00000.nc     rpointer.ocn.tavg.5
b.e20.B1850.f19_g17.release_cesm2_1_0.020.pop.ro.0301-01-01-00000       rpointer.rof
~~~
{: .output}

These are all the files that the model needs to start this run.  Notice that they begin with `RUN_REFCASE`

So what does `RUN_STARTDATE` do?

Even though the restarts we start from begin on 0003-01-01, we can tell the model to begin our experiment with whatever we want the startdate to be.  This is because our simulation does not need to match any specific, real date. You can see in my experiment that the `RUN_STARTDATE` is set to `0001-01-01` and the output begins at that date:

~~~
$ cd /glade/scratch/cstan/archive/b.day1.0/atm/hist
$ ls -lt
~~~
{: .language-bash}

*Important Note: You can specify a new startdate for your run if the RUN_TYPE=hybrid.  You cannot do this if your RUN_TYPE=branch.*


#### Initial Conditions in our Added Heating Experiments (Assignment #5)

In the previous example, all this initial condition information was set for us by default as part of the compset definition.  We did not have to set any of this ourselves.

In our added heating experiments (Assignment #5), we set this up ourselves using Dr. Swenson's scripts.  Let's take a look:

~~~
$ cd /glade/u/home/cstan/cases/addheat
$ ./xmlquery RUN_TYPE,RUN_STARTDATE,RUN_REFDATE,RUN_REFCASE,GET_REFCASE,RUN_REFDIR
~~~
{: .language-bash}

~~~
Results in group run_begin_stop_restart
	RUN_TYPE: branch
	RUN_STARTDATE: 0005-01-01
	RUN_REFDATE: 0005-01-01
	RUN_REFCASE: test1
	GET_REFCASE: FALSE
	RUN_REFDIR: cesm2_init
~~~
{: .output}

`RUN_TYPE` is `branch`, meaning that the model components are initialized from restart files generated by a user specified CESM.

`RUN_REFCASE` is set to `test1` which is the name of my case for experiment we did for Assignment #2.  

`GET_REFCASE` is set to `FALSE`, so we must stage the restart files in the run directory for this case.

`RUN_REFDIR` is not used since `GET_REFCASE` is set to `FALSE`.

`RUN_STARTDATE` must match `RUN_REFDATE` since this is a branch run.


#### Staging the restart files

One of the lines in our script for setting up the added heating runs is:

~~~
# Initial conditions for Branch run
cp $refdir/* $ptmp/$expname/run/
~~~
{: .language-bash}

This resolves to:

`cp /glade/scratch/cstan/archive/b.day1.0/rest/0005-01-01-00000/* /glade/scratch/cstan/addheat/run/`

We copied the restart files from the restart directory to the run directory for our new case.  

~~~
ls /glade/scratch/cstan/addheat/run/
~~~
{: .language-bash}

~~~
CASEROOT              rpointer.rof                      b.day1.0.clm2.r.0005-01-01-00000.nc
rpointer.atm          b.day1.0.cam.h0.0004-12.nc           b.day1.0.clm2.rh0.0005-01-01-00000.nc
rpointer.drv          b.day1.0.cam.h1.0005-01-01-00000.nc  b.day1.0.cpl.r.0005-01-01-00000.nc
rpointer.glc          b.day1.0.cam.i.0005-01-01-00000.nc   b.day1.0.mosart.h0.0004-12.nc
rpointer.ice          b.day1.0.cam.r.0005-01-01-00000.nc   b.day1.0.mosart.r.0005-01-01-00000.nc
rpointer.lnd          b.day1.0.cam.rs.0005-01-01-00000.nc  b.day1.0.mosart.rh0.0005-01-01-00000.nc
rpointer.ocn.ovf      b.day1.0.cice.r.0005-01-01-00000.nc  b.day1.0.pop.r.0005-01-01-00000.nc
rpointer.ocn.restart  b.day1.0.cism.r.0005-01-01-00000.nc  b.day1.0.pop.ro.0005-01-01-00000
rpointer.ocn.tavg.5   b.day1.0.clm2.h0.0004-12.nc          b.day1.0.ww3.r.0005-01-01-00000
~~~
{: .output}

Now that we know better how initializing the model works, we will learn about initialized prediction experiments.
