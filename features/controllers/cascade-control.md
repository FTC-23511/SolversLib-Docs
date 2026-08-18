---
description: package com.seattlesolvers.solverslib.controller
---

# Cascade Control

A `CascadeController` chains two controllers together into a single closed-loop system. The **primary** (outer) controller runs on position: it compares the measured position against the position setpoint and outputs a _velocity command_. The **secondary** (inner) controller runs on velocity: it compares the mechanism's measured velocity against that velocity command and outputs the final control value (typically motor power).

This nested structure is called cascade control. The inner velocity loop reacts quickly to disturbances (battery sag, friction, gravity, a game element landing in your intake) before they ever show up as position error, while the outer position loop only has to steer a well-behaved velocity loop. The result is generally smoother, more consistent motion than a single position PID - especially on heavy arms and high-speed slides.

You can find the source code for the `CascadeController` class [here](https://github.com/FTC-23511/SolversLib/blob/master/core/src/main/java/com/seattlesolvers/solverslib/controller/CascadeController.java), and the theory behind closed-loop control on CtrlAltFtc [here](https://www.ctrlaltftc.com/).,&#x20;

### When would an FTC team want this?

* **Heavy arms and lifts:** the inner velocity loop fights gravity and load changes immediately, instead of waiting for position error to build up
* **Fast slides:** commanding velocity rather than raw power gives controlled acceleration and less slamming at the ends of travel
* **Consistency across battery voltage:** a velocity loop compensates for voltage drop automatically, so the same setpoint behaves the same at 14V and at 12V

If a single well-tuned PIDF already does the job for your mechanism, you probably don't need a cascade. Consider using a dual PIDF controller (one for large error, one for small error) before this.

#### Constructing a CascadeController

`CascadeController` takes any two SolversLib `Controller` objects - most commonly a `PIDController` or `PIDFController` for each loop:

```java
// Outer loop: position error (ticks) -> velocity command (ticks per second)
PIDController positionController = new PIDController(kP_pos, kI_pos, kD_pos);

// Inner loop: velocity error (ticks per second) -> motor power
PIDFController velocityController = new PIDFController(kP_vel, kI_vel, kD_vel, kF_vel);

CascadeController cascade = new CascadeController(positionController, velocityController);
```

Because the two gain sets multiply through the cascade, the outer controller's output is in _velocity units_, and the inner controller's gains convert velocity error into power. Expect the outer `kP` to be much larger than a standalone position PID's, and the inner gains to be small.

#### Setting Setpoints

```java
// Target a position; velocity setpoint is cleared to 0
cascade.setSetPoint(1200);

// Target a position AND cruise toward it at a desired velocity
cascade.setSetPoints(1200, 800); // position setpoint, velocity setpoint
```

The velocity setpoint from `setSetPoints()` is added to the primary controller's output before it reaches the inner loop, so it acts as a velocity feedforward - useful for following motion profiles, where each loop you feed in the profile's instantaneous target position and target velocity.

{% hint style="warning" %}
`setSetPoint(sp)` resets the velocity setpoint back to 0. If you are using a nonzero velocity setpoint, keep using `setSetPoints()`.
{% endhint %}

#### Usage Example

A lift driven to a scoring position with a cascade of two controllers:

```java
PIDController positionController = new PIDController(8.0, 0, 0.1);
PIDFController velocityController = new PIDFController(0.0004, 0.00004, 0, 0.0002);

CascadeController cascade = new CascadeController(positionController, velocityController);

cascade.setMaxOutput(1.0);
cascade.setTolerance(15);

waitForStart();

cascade.setSetPoint(1200);

while (opModeIsActive()) {
    double power = cascade.calculate(lift.getCurrentPosition());
    lift.setPower(power);

    telemetry.addData("measured velocity", cascade.getMeasuredVel());
    telemetry.addData("at target", cascade.atSetPoint());
    telemetry.update();
}
```

#### Measuring Velocity

`getMeasuredVel()` returns the velocity the controller measured on its most recent `calculate()` call, in position units per second. This is handy for telemetry, logging, and tuning the inner loop.

```java
double ticksPerSecond = cascade.getMeasuredVel();
```

#### Resetting the Controller

`reset()` clears the measured velocity and timestamps, and also resets both the primary and secondary controllers, so any wound-up integral term in either loop is discarded. This is useful for when you need to re-enable the mechanism or switching between control tasks.

```java
cascade.reset();
```
