---
layout: post
title: Acclerometer Calibration
date: 2017-4-14 11:26:00
tags: sensor
postPatterns: 'circuitBoard'
---

# Accelerometer Calibration

## Thermal drift measurement

The accelerometer has thermal drift, we can sample the drift data in a temperature range $t_{0}$ to $t_{n}$.  

The thermal drift experiment should be done in a thermal chamber, which can control the temperature inside as we expected. 

And we have to place the DUT acclerometer be horizontal as possible as we can, it means we make the coordinate system be as same as the world coordinate system, then control the thermal chamber's temperature from $t_{0}$ to $t_{n}$, as sample the sensor's output.  

In this way, we can collect the experiment data into a table like below:

| Temperature | Drift |
| -------
| $T_{0}$ | $D_{0}$ |
| $T_{1}$ | $D_{1}$ |
| $\vdots$ | $\vdots$ |
| $T_{n}$ | $D_{n}$ |

## zero g offset measurement

It is known as the popular six orientation calibration. We can get the offset for each axis from this measurement, however, it is precise only at the temperature at which it was doing the experiment. 

Lets mark the offset as a set of data like, 

| Temperature | Offset |
| ----
| $T_{m}$ | $O_{m}$ |

## Calculation and regression

From the experiment data, we can obtain a table like below:

| Temperature Difference | Drift Difference |
| ----
| $\Delta{T_{0}}$ | $D_{0} - O_{m}$ |
| $\Delta{T_{1}}$ | $D_{0} - O_{m}$ |
| $\vdots$ | $\vdots$ |
| $\Delta{T_{n}}$ | $D_{n} - O_{m}$ |

We can regress the formula for this table 

$$
D - O_{m} = f(\Delta{T})
$$

$$
D = O_{m} + f(\Delta{T})
$$

## Accelerometer calibrated data

$$
Accel = Accel_{raw} - D = Accel_{raw} - O_{m} - f(T - T_{m}) 
$$

> Written with [StackEdit](https://stackedit.io/).
