---
description: package com.seattlesolvers.solverslib.controller
---

# Cascade Control

A `CascadeController` chains two controllers together into a single closed-loop system. The **primary** (outer) controller runs on position: it compares the measured position against the position setpoint and outputs a _velocity command_. The **secondary** (inner) controller runs on velocity: it compares the mechanism's measured velocity against that velocity command and outputs the final control value (typically motor power).

This nested structure is called cascade control. The inner velocity loop reacts quickly to disturbances (battery sag, friction, gravity, a game element landing in your intake) before they ever show up as position error, while the outer position loop only has to steer a well-behaved velocity loop. The result is generally smoother, more consistent motion than a single position PID - especially on heavy arms and high-speed slides.

You can find the source code for the `CascadeController` class [here](https://github.com/FTC-23511/SolversLib/blob/master/core/src/main/java/com/seattlesolvers/solverslib/controller/CascadeController.java), and the theory behind closed-loop control on CtrlAltFtc [here](https://www.ctrlaltftc.com/).

{% hint style="info" %}
`CascadeController` was stabilized in SolversLib 0.3.5: velocity is now measured correctly between calls, the first loop iteration no longer produces `NaN` or a velocity spike when the mechanism starts away from zero, and `reset()` now fully clears the cascade (including both sub-controllers).
{% endhint %}

### When would an FTC team want this?

* **Heavy arms and lifts** - the inner velocity loop fights gravity and load changes immediately, instead of waiting for position error to build up
* **Fast slides** - commanding velocity rather than raw power gives controlled acceleration and less slamming at the ends of travel
* **Consistency across battery voltage** - a velocity loop compensates for voltage drop automatically, so the same setpoint behaves the same at 14V and at 12V

If a single well-tuned PIDF already does the job for your mechanism, you don't need a cascade. Reach for it when one loop can't be tuned both stiff enough and smooth enough at the same time.

### Using the CascadeController Class

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

#### How calculate() works

Call `calculate(pv)` once per loop with the measured position (e.g. an encoder reading), exactly like any other SolversLib controller. Internally, each call:

1. Measures velocity as the **derivative of the measured position between calls** - `(current position - previous position) / elapsed time`, in position units per second. No separate velocity sensor is needed.
2. Runs the primary controller on the measured position against the position setpoint, producing a velocity command.
3. Runs the secondary controller on the measured velocity against that velocity command (plus the velocity setpoint, see below), producing the final output.

{% hint style="info" %}
Because velocity is derived from your position readings, feed `calculate()` the same encoder every loop and call it at a reasonably steady rate. On the very first call there is no previous reading yet, so measured velocity is simply 0 - starting the OpMode with the mechanism away from position 0 will not cause a velocity spike.
{% endhint %}

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

cascade.setMaxOutput(1.0); // clamp the final output to valid motor power
cascade.setTolerance(15);  // within 15 ticks counts as at the setpoint

waitForStart();

cascade.setSetPoint(1200); // target lift position in encoder ticks

while (opModeIsActive()) {
    // one call per loop with the encoder reading
    double power = cascade.calculate(lift.getCurrentPosition());
    lift.setPower(power);

    telemetry.addData("measured velocity", cascade.getMeasuredVel());
    telemetry.addData("at target", cascade.atSetPoint());
    telemetry.update();
}
```

Since `CascadeController` extends the base `Controller` class, everything you already know carries over: `calculate(pv, sp)`, `atSetPoint()`, `setTolerance()`, `setMinOutput()`, `setMaxOutput()`, `getPeriod()`, `getPositionError()`, and `getVelocityError()` all work as usual. For the cascade, `getPositionError()` is the outer-loop (position) error, and `getVelocityError()` is the error between your velocity setpoint and the measured velocity.

#### Measuring Velocity

`getMeasuredVel()` returns the velocity the controller measured on its most recent `calculate()` call, in position units per second. This is handy for telemetry, logging, and tuning the inner loop.

```java
double ticksPerSecond = cascade.getMeasuredVel();
```

#### Resetting the Controller

`reset()` clears the cascade's internal state - the measured velocity and timestamps - **and also resets both the primary and secondary controllers**, so any wound-up integral term in either loop is discarded. Call it when re-enabling the mechanism or switching between control tasks, just like you would for a standalone `PIDFController`.

```java
cascade.reset();
```

#### Tuning Notes

* Tune the **inner velocity loop first** with the outer gains effectively disabled: command velocities via `setSetPoints(currentPosition, targetVelocity)` and adjust the inner gains (and `kF`) until measured velocity tracks the command
* Then tune the **outer position loop**: raise the outer `kP` until the mechanism reaches position quickly without oscillating
* As always with cascaded loops, the inner loop should be tuned faster (more responsive) than the outer loop, or the cascade will fight itself
