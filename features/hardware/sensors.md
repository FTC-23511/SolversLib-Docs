---
description: package com.seattlesolvers.solverslib.hardware
---

# Sensors

## Sensors

There are a few sensors that are offered in SolversLib:

* [SensorColor](https://github.com/FTC-23511/SolversLib/blob/master/core/src/main/java/com/seattlesolvers/solverslib/hardware/SensorColor.java) & [SensorRevColorV3](https://github.com/FTC-23511/SolversLib/blob/master/core/src/main/java/com/seattlesolvers/solverslib/hardware/SensorRevColorV3.java)
* [SensorDistance](https://github.com/FTC-23511/SolversLib/blob/master/core/src/main/java/com/seattlesolvers/solverslib/hardware/SensorDistance.java) & [SensorDistanceEx](https://github.com/FTC-23511/SolversLib/blob/master/core/src/main/java/com/seattlesolvers/solverslib/hardware/SensorDistanceEx.java)
* [SensorRevTOFDistance](https://github.com/FTC-23511/SolversLib/blob/master/core/src/main/java/com/seattlesolvers/solverslib/hardware/SensorRevTOFDistance.java)

The `SensorColor` class is just an extension for the `ColorSensor` class that is in the SDK, and the `SensorRevColorV3` is the same but for the [REV Color Sensor V3](https://www.revrobotics.com/rev-31-1557/).

`SensorDistance` and `SensorDistanceEx` are interfaces for creating custom distance sensors if desired. An implementation of the `SensorDistanceEx` interface is `SensorRevTOFDistance` which utilizes the time-of-flight mechanic to track distance. It has a minimum and maximum tolerance, that can be used via `DistanceTarget`.

## Gyro Extensions

The `GyroEx` class is an extended gyro that allows users to add more configurable methods and possible control to their gyro. An example would be creating a `ModernRoboticsGyro` class. The abstract class has the following methods:

* `init()`: initializes the gyro and sets the current direction to the 0 heading
* `getHeading()`: returns the heading of the robot compared to the last reset
* `getAbsoluteHeading()`: returns the absolute heading relative to the initial direction
* `getAngles()`: returns the x, y, and z orientation of the gyro. This is functionally the same as yaw, pitch, and roll.
* `getRotation2d()`: transforms the heading into a `Rotation2d` object
* `reset()`: applies an offset so that `getHeading()` returns the 0 position

A useful implementation of this is the [RevIMU](https://github.com/FTC-23511/SolversLib/blob/master/core/src/main/java/com/seattlesolvers/solverslib/hardware/RevIMU.java) class for the built-in imu on your REV hub.
