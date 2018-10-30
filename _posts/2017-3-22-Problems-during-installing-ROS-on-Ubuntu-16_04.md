---
layout: post
title: Problems during installing ROS on Ubuntu 16.04
date: 2017-3-22 13:54:00
tags: tools
postPatterns: 'circuitBoard'
---

```
sudo apt-get install ros-kinetic-desktop-full
```

You would got the error as below

```
Reading package lists... Done

Building dependency tree

Reading state information... Done

Some packages could not be installed. This may mean that you have requested an impossible situation or if you are using the unstable distribution that some required packages have not yet been created or been moved out of Incoming. The following information may help to resolve the situation:

The following packages have unmet dependencies:  ros-kinetic-desktop-full :

Depends: ros-kinetic-desktop but it is not going to be installed

Depends: ros-kinetic-perception but it is not going to be installed

Depends: ros-kinetic-simulators but it is not going to be installed E: Unable to correct problems, you have held broken packages.
```

How to solve this error? 

If you trace the error you might found it is caused by the library `libpng-dev`, it depends on the library **libpng12-0(1.2.54-1ubuntu1)** while the default installation in Ubuntu 16.04 is **libpng12-0(1.2.54-1ubuntuk1)**, it is the root cause. 

So, use the following command to install the right libpng12-0.

```
sudo apt-get install libpng12-0=1.2.54-1ubuntu1
``` 

Then 

```
sudo apt-get install ros-kinetic-desktop-full
```


> Written with [StackEdit](https://stackedit.io/).
