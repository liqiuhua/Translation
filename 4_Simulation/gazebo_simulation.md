# Gazebo仿真
---
官网英文原文地址：http://dev.px4.io/simulation-gazebo.html
# Gazebo Simulation

[Gazebo](http://gazebosim.org) is a 3D simulation environment for autonomous robots. It can be used with ROS as complete robotics simulation suite or standalone, and this guide covers the simpler to set up standalone operation.

{% raw %}
<video id="my-video" class="video-js" controls preload="auto" width="100%" 
poster="../pictures/diagrams/PX4-Flight.JPG" data-setup='{"aspectRatio":"16:9"}'>
  <source src="http://7xvob5.com1.z0.glb.clouddn.com/2-PX4%20Flight%20Stack%20ROS%203D%20Software%20in%20the%20Loop%20Simulation%20(SITL).mp4" type='video/mp4' >
  <p class="vjs-no-js">
    To view this video please enable JavaScript, and consider upgrading to a web browser that
    <a href="http://videojs.com/html5-video-support/" target="_blank">supports HTML5 video</a>
  </p>
</video>
{% endraw %}

{% mermaid %}
graph LR;
  Gazebo-->Plugin;
  Plugin-->MAVLink;
  MAVLink-->SITL;
{% endmermaid %}


## Installation

The installation requires to install Gazebo and our simulation plugin.

<aside class="tip">
Gazebo version 6 should be used. Linux users: If you installed a ROS version earlier than Jade, be sure to uninstall the bundled Gazebo (sudo apt-get remove ros-indigo-gazebo) version as it is too old.
</aside>

### Mac OS

Mac OS requires Gazebo 6.

<div class="host-code"></div>

```sh
brew install gazebo6
```

### Linux

The PX4 SITL uses the Gazebo simulator, but does not depend on ROS. The simulation can be [interfaced to ROS](../4_Simulation/interfacingto_ros.md) the same way as normal flight code is.

#### ROS Users

If you plan to use PX4 with ROS, make sure to follow the [Gazebo version guide for version 6](http://gazebosim.org/tutorials?tut=ros_wrapper_versions#Gazebo6.xseries) for ROS.

#### Normal Installation

Follow the [Linux installation instructions](http://gazebosim.org/tutorials?tut=install_ubuntu&ver=6.0&cat=install) for Gazebo 6.

## Running the Simulation

From within the source directory of the PX4 Firmware run the PX4 SITL with one of the airframes (Quads, planes and VTOL are supported, including optical flow):

### Quadrotor

<div class="host-code"></div>

```sh
cd ~/src/Firmware
make posix_sitl_default gazebo
```

### Quadrotor with Optical Flow

<div class="host-code"></div>

```sh
cd ~/src/Firmware
make posix gazebo_iris_opt_flow
```

### Standard VTOL

<div class="host-code"></div>

```sh
cd ~/src/Firmware
make posix_sitl_default gazebo_standard_vtol
```

### Tailsitter VTOL

<div class="host-code"></div>

```sh
cd ~/src/Firmware
make posix_sitl_default gazebo_tailsitter
```

<aside class="tip">
Make sure you followed the [Installing Files and Code](http://dev.px4.io/starting-installing-mac.html) guide appropiate for your OS if you run into any errors or missing dependencies.
<aside>

This will bring up the PX4 shell:

```sh
[init] shell id: 140735313310464
[init] task name: mainapp

______  __   __    ___
| ___ \ \ \ / /   /   |
| |_/ /  \ V /   / /| |
|  __/   /   \  / /_| |
| |     / /^\ \ \___  |
\_|     \/   \/     |_/

Ready to fly.


pxh>
```

<aside class="note">
Right-clicking the quadrotor model allows to enable follow mode from the context menu, which is handy to keep it in view.
</aside>

## Taking it to the Sky

 ![gazebo](../pictures/sim\gazebo.png)

The system will print the home position once it finished intializing (`telem> home: 55.7533950, 37.6254270, -0.00`). You can bring it into the air by typing:

```sh
pxh> commander takeoff
```

<aside class="tip">
Joystick or thumb-joystick support is available through QGroundControl (QGC). To use manual input, put the system in a manual flight mode (e.g. POSCTL, position control). Enable the thumb joystick from the QGC preferences menu.
</aside>

## Extending and Customizing

To extend or customize the simulation interface, edit the files in the `Tools/sitl_gazebo` folder. The code can be accessed through the [sitl_gazebo repository](https://github.com/px4/sitl_gazebo) on Github.

<aside class="note">
The build system enforces the correct submodule to be checked out for all dependencies, including the simulator. It will not overwrite changes in files in the directory, however, when these changes are comitted the submodule needs to be registered in the Firmware repo with the new commit hash. To do so, `git add Tools/sitl_gazebo` and commit the change. This will update the GIT hash of the simulator.
</aside>

## Interfacing to ROS

The simulation can be [interfaced to ROS](../4_Simulation/interfacingto_ros.md) the same way as onboard a real vehicle.