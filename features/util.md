---
description: package com.seattlesolvers.solverslib.util;
---

# Utility Functions

SolversLib comes with many different Utility Functions:

* [Look Up Tables](https://docs.seattlesolvers.com/features/util#what-is-a-look-up-table)
* [Timing Functions](https://docs.seattlesolvers.com/features/util#timing-functions)
* [Math Utilities](https://docs.seattlesolvers.com/features/util#math-utilities)
* [Directional Enums](https://docs.seattlesolvers.com/features/util#directional-enums)

## What is a Look Up Table?

A look up table or LUT for short is used to store values and be able to quickly recall them.

The SolversLib provides 2 different variations of look up tables. In this year's game they can be used to store different set and tested velocities or angles. You can either retrieve the closest reference or you can interpolate through them.

## LUT (Look Up Table)

Provides a way to store values in a table to quickly retrieve them. For example, this might be used to store different speeds or angles based on certain distances. This class allows you to find the closest entry to the input.

For example if you enter:

| Input | Output |
| :---: | :----: |
|   0   |    0   |
|   1   |    1   |
|   2   |    1   |

When you request 1.1, it will return 1.

### Example Usage:

```java
import com.seattlesolvers.solverslib.util.LUT;

LUT<Double, Double> speeds = new LUT<Double, Double>()
{{
    add(5.0, 1.0);
    add(4.0, 0.9);
    add(3.0, 0.75);
    add(2.0, 0.5);
    add(1.0, 0.2);
}};

double distance = odometry.getPose().getTranslation().getDistance(new Translation2d(5, 10));
shooter.set(speeds.getClosest(distance));
```

## InterpLUT (Interpolated Look Up Table)

Provides a way to fill in the gaps in the data. Similarly to the LUT above, this allows you to add data points and retrieve a data point given an output. The difference between a normal LUT and InterpLUT is that the interpolated LUT uses math to fill in all the gaps. Effectively generating filler data based on the data around it.

### Example Usage:

```java
import com.seattlesolvers.solverslib.util.InterpLUT;

//Init the Look up table
InterpLUT lut = new InterpLUT();

//Adding each val with a key
lut.add(1.1, 0.2);
lut.add(2.7, .5);
lut.add(3.6, 0.75);
lut.add(4.1, 0.9);
lut.add(5, 1);
//generating final equation
lut.createLUT();

double distance = odometry.getPose().getTranslation().getDistance(new Translation2d(5, 10));
shooter.set(lut.get(distance));

//getting the velo required and passing it to the shooter.
```

## Timing Functions

SolversLib Comes with multiple timers and Timing Functions. They let you set the length, unit, can act as a stopwatch or even return the loop time.

### Timer

A timer can be created with a length or length and Time Unit. The various functions nested within the Timer object are:

| Function                | Return Type | Description                       |
| ----------------------- | ----------- | --------------------------------- |
| `timer.start()`         | Void        | Starts the Timer                  |
| `timer.pause()`         | Void        | Pauses the Timer                  |
| `timer.resume()`        | Void        | Resumes the Timer                 |
| `timer.elapsedTime()`   | long        | Returns the Passed Time           |
| `timer.remainingTime()` | long        | Returns Time Left                 |
| `timer.done()`          | Boolean     | Returns if the Timer is Completed |
| `timer.isTimerOn()`     | Boolean     | Returns if the Timer is Active    |

## Math Utilities

SolversLib provides its math utilities through the [MathUtils](https://github.com/FTC-23511/SolversLib/blob/master/core/src/main/java/com/seattlesolvers/solverslib/util/MathUtils.java) class. Every method is `static`, so you never create a `MathUtils` object — just import the class and call the methods on it directly:

```java
import com.seattlesolvers.solverslib.util.MathUtils;
```

### Clamp

`clamp` lets you restrict a value to a certain max and min and is usable in double and int.

**Example Usage:**

Double Method:

```java
import com.seattlesolvers.solverslib.util.MathUtils;

double ValueToClamp;
double LowestPossibleValue;
double HighestPossibleValue;

double OutputVal = MathUtils.clamp(ValueToClamp,
                         LowestPossibleValue,
                         HighestPossibleValue);
```

Int Method:

```java
import com.seattlesolvers.solverslib.util.MathUtils;

int ValueToClamp;
int LowestPossibleValue;
int HighestPossibleValue;

int OutputVal = MathUtils.clamp(ValueToClamp,
                         LowestPossibleValue,
                         HighestPossibleValue);
```

### Round

`round(double number, int places)` rounds a number to the given amount of decimal places (rounding half up), which is handy for cleaning up telemetry values.

```java
double rounded = MathUtils.round(3.14159, 2); // 3.14
```

### Angle Normalization

These methods wrap an angle back into a standard range, which is useful for heading math — for example, making sure your turn-to-angle code always takes the shortest way around. The boolean parameter picks the output range: `true` gives 0 to 360° (or 0 to 2π), while `false` gives -180° to 180° (or -π to π).

| Method                                                                 | Description                                                                                 |
| ---------------------------------------------------------------------- | ------------------------------------------------------------------------------------------- |
| `normalizeAngle(double angle, boolean zeroToMax, AngleUnit angleUnit)` | Normalizes an angle in the unit given by the FTC SDK's `AngleUnit` (`DEGREES` or `RADIANS`) |
| `normalizeDegrees(double angle, boolean zeroToFull)`                   | Shortcut for `normalizeAngle` with `AngleUnit.DEGREES`                                      |
| `normalizeRadians(double angle, boolean zeroToFull)`                   | Shortcut for `normalizeAngle` with `AngleUnit.RADIANS`                                      |
| `returnMaxForAngleUnit(AngleUnit angleUnit)`                           | Returns the full-circle value for the unit (360 for degrees, 2π for radians)                |

```java
double heading = MathUtils.normalizeDegrees(450, true);  // 90.0
double error = MathUtils.normalizeDegrees(190, false);   // -170.0
```

### Signed Square Root

`sqrtWithSig(double val)` returns the square root of the absolute value while keeping the original sign — useful for shaping joystick inputs without losing direction.

```java
double shaped = MathUtils.sqrtWithSig(-0.25); // -0.5
```

## Directional Enums

SolversLib comes with multiple directional enums for all your directional needs! You can use these for any autonomous or TeleOp States or anything you want!

| Direction | Index |
| --------- | ----- |
| LEFT      | 0     |
| RIGHT     | 1     |
| UP        | 2     |
| DOWN      | 3     |
| FORWARD   | 4     |
| BACKWARDS | 5     |

You can find some examples in the [sample folder](https://github.com/FTC-23511/SolversLib/tree/master/examples/src/main/java/org/firstinspires/ftc/teamcode).
