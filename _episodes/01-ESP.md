---
title: "Initialized Prediction"
questions:
- "What are Initialized Prediction Experiments?"
objectives:
keypoints:
- ""
---

## Types of Experiments

#### Simulation: what the model thinks the climate looks like under certain conditions

These are not necessarily real conditions, they are typically idealized and do not correspond to any specific day or time.  That is why we evaluate them using statistics (e.g. mean or variability over the simulation) and often compare these statistics between two simulations (e.g. a control and an experiment) or with the observed statistics. 

* You ran a B1850 case and looked at the seasonal means and equatorial precipitation (Assignment #3)
* You ran an sensitivity experiment with the DWG SSTs and looked at how the mean precip compared with a control simulation (Assignment #4)
* You ran a ocean model simulations where you changed different things and looked at how the mean changed under the different experiments
<!-- * You ran the added heating experiments and compared the mean with and without added heating (Assignment #5)-->

#### Projection: what the model thinks the climate will look like in the future under different scenarios

* projections are a special case of a simulation under different future scenarios
* because they are run and evaluated in much the same way as standard simulations, we have not done any specific projection experiments. 
* Example projection compset:  [BSSP585](https://www.cesm.ucar.edu/models/cesm2/config/2.1.3/compsets.html) This is the SSP585 scenario for GHGs (similar to RCP8.5 used in CMIP5) or [BSSP585cmip6](https://www.cesm.ucar.edu/models/cesm2/config/2.1.3/compsets.html) is the exact configuration used for CMIP6 projections.
* A common comparison is between the projection experiment and the historical or pre-industrial control experiment

#### Prediction: a forecast for a specific date given initial conditions for each component

These types of experiment correspond to the model attempting to predict actual conditions that occurred.  We can compare directly between what the model predicted and what actually happened (observations).

* Today we will learn about how to setup the model with initial conditions for making a forecast

