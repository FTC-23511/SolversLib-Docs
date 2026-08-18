---
description: >-
  packages com.seattlesolvers.solverslib.trajectory,
  .controller.wpilibcontroller, and .command
---

# Motion Profiling

A plain PID loop only reacts to error. If you jump a lift's setpoint from 0 to 1200 ticks in one instant, the error is suddenly huge, the controller slams maximum power into the motor, and the result is jerky motion, belt skipping, and overshoot. Motion profiling fixes this by moving the _setpoint_ smoothly instead of teleporting it: a profile generates a stream of intermediate setpoints between where the mechanism is and where you want it to be, so the PID loop only ever chases a small, achievable error.

The most common profile in FTC is the **trapezoid profile**, which splits a movement into three phases:

1. **Accelerate** at a constant rate up to a maximum velocity
2. **Cruise** at that maximum velocity
3. **Decelerate** at a constant rate, arriving at the goal with the desired end velocity (usually zero)

Plot velocity against time and the graph forms a trapezoid, hence the name. For short movements the mechanism may never reach cruise velocity, in which case the trapezoid degenerates into a triangle - the classes below handle this automatically.

{% hint style="info" %}
These classes are ports of older versions of WPILib's `TrapezoidProfile` and `ProfiledPIDController`. Note that recent WPILib versions changed the `TrapezoidProfile` API, so older WPILib documentation matches SolversLib more closely than current WPILib docs. For the theory behind motion profiling, we recommend [this page](https://www.ctrlaltftc.com/advanced/motion-profiling) on CtrlAltFtc.
{% endhint %}

## The TrapezoidProfile Class

[`TrapezoidProfile`](https://github.com/FTC-23511/SolversLib/blob/master/core/src/main/java/com/seattlesolvers/solverslib/trajectory/TrapezoidProfile.java) represents a single trapezoidal movement and lets you sample the position and velocity the mechanism _should_ have at any point in time.

### Constraints and States

The profile is described by two small nested classes:

* `TrapezoidProfile.Constraints` holds the `maxVelocity` and `maxAcceleration` the mechanism is allowed to reach.
* `TrapezoidProfile.State` holds a `position` and a `velocity` - a snapshot of where the mechanism is (or should be) and how fast it is moving.

```java
// The lift may travel at up to 1800 ticks/sec, accelerating at 2400 ticks/sec^2
TrapezoidProfile.Constraints constraints = new TrapezoidProfile.Constraints(1800, 2400);

// End at 1200 ticks, at rest
TrapezoidProfile.State goal = new TrapezoidProfile.State(1200, 0);

// Start from the lift's current position, at rest
TrapezoidProfile.State initial = new TrapezoidProfile.State(liftMotor.getCurrentPosition(), 0);
```

You can use any units you like (encoder ticks, inches, radians) as long as you are consistent: velocities are units per second and accelerations are units per second squared.

### Creating and Sampling a Profile

```java
TrapezoidProfile profile = new TrapezoidProfile(constraints, goal, initial);

// If the initial state is omitted, the profile starts at rest at position 0
TrapezoidProfile profile2 = new TrapezoidProfile(constraints, goal);
```

`calculate(t)` returns the `State` the mechanism should be at `t` seconds after the start of the profile:

```java
ElapsedTime timer = new ElapsedTime();

while (opModeIsActive() && !profile.isFinished(timer.seconds())) {
    TrapezoidProfile.State setpoint = profile.calculate(timer.seconds());
    // Feed setpoint.position into your own position controller here.
    // setpoint.velocity is also available, e.g. for a velocity feedforward.
}
```

A few other useful methods:

* `totalTime()` returns how long the whole profile takes, in seconds.
* `isFinished(t)` returns true once `t` has passed the profile's total time.
* `timeLeftUntil(target)` returns the time remaining until the profile reaches a target distance.

Most teams never need to sample a profile by hand, though - the `ProfiledPIDController` below does it for you.

## The ProfiledPIDController Class

[`ProfiledPIDController`](https://github.com/FTC-23511/SolversLib/blob/master/core/src/main/java/com/seattlesolvers/solverslib/controller/wpilibcontroller/ProfiledPIDController.java) wraps a trapezoid profile around a normal PID loop. Instead of giving the controller a setpoint, you give it a **goal**. On every call to `calculate()`, the controller builds a profile from its current intermediate setpoint toward the goal, steps that profile forward by the measured loop period, and then runs regular PID math against the new intermediate setpoint. The effect is that the setpoint glides toward the goal under your velocity and acceleration constraints, and the PID loop just has to keep up.

### Constructing a ProfiledPIDController

```java
ProfiledPIDController controller = new ProfiledPIDController(
    kP, kI, kD,
    new TrapezoidProfile.Constraints(maxVelocity, maxAcceleration)
);
```

The gains can be changed later with `setP()`, `setI()`, `setD()`, or `setPID()`, and the constraints with `setConstraints()`.

### Goal vs. Setpoint

Because the controller manages its own intermediate setpoint, it distinguishes between the two:

* `setGoal(1200)` or `setGoal(new TrapezoidProfile.State(1200, 0))` sets where you ultimately want to end up. The `double` overload assumes a goal velocity of zero.
* `getSetpoint()` returns the current intermediate `State` along the profile.
* `atSetpoint()` checks whether the PID loop is tracking the intermediate setpoint within the tolerance set by `setTolerance()`.
* `atGoal()` checks whether the mechanism has actually reached the goal (at the setpoint _and_ the setpoint has reached the goal).

### Using the Controller in a Loop

```java
ProfiledPIDController controller = new ProfiledPIDController(
        0.01, 0, 0.0002,
        new TrapezoidProfile.Constraints(1800, 2400)
);
controller.setTolerance(15);

// Start the profile from the lift's actual position so the setpoint does not jump
controller.reset(liftMotor.getCurrentPosition());
controller.setGoal(1200);

while (opModeIsActive() && !controller.atGoal()) {
    double output = controller.calculate(liftMotor.getCurrentPosition());
    liftMotor.set(output);
}
liftMotor.stopMotor();
```

You can also pass the goal (and even new constraints) directly into `calculate(measurement, goal)` each loop, which is handy when the goal changes on the fly - for example, a lift height chosen by the driver.

{% hint style="warning" %}
The intermediate setpoint persists between movements. Always call `reset(measuredPosition)` (or `reset(measuredPosition, measuredVelocity)`) before starting a new movement - otherwise the first `calculate()` call plans a profile from a stale setpoint and the output can jump.
{% endhint %}

## Command-Based Wrappers

If you use SolversLib's command-based framework, two ready-made commands wrap the classes above.

### ProfiledPIDCommand

[`ProfiledPIDCommand`](https://github.com/FTC-23511/SolversLib/blob/master/core/src/main/java/com/seattlesolvers/solverslib/command/ProfiledPIDCommand.java) runs a `ProfiledPIDController` inside a command. Each `execute()` it reads the measurement supplier, calls `calculate()`, and passes both the output and the current profile setpoint to your `BiConsumer` - the setpoint's velocity is useful for adding a feedforward term. On `initialize()` it automatically resets the controller to the current measurement, and on `end()` it sends an output of zero.

The goal can be given as a fixed `double` or `TrapezoidProfile.State`, or as a `DoubleSupplier`/`Supplier<State>` that is re-read every loop.

```java
// lift is a user-defined Subsystem with getPosition() and setPower() methods
ProfiledPIDCommand raiseLift = new ProfiledPIDCommand(
        new ProfiledPIDController(0.01, 0, 0.0002,
                new TrapezoidProfile.Constraints(1800, 2400)),
        lift::getPosition,                            // measurement
        1200,                                         // goal (goal velocity is implicitly zero)
        (output, setpoint) -> lift.setPower(output),  // use the output
        lift                                          // required subsystem
);

// The command runs forever by default, so add an end condition
raiseLift.interruptOn(() -> raiseLift.getController().atGoal()).schedule();
```

To add more complex exit conditions or behavior, subclass `ProfiledPIDCommand` and override `isFinished()`.

### TrapezoidProfileCommand

[`TrapezoidProfileCommand`](https://github.com/FTC-23511/SolversLib/blob/master/core/src/main/java/com/seattlesolvers/solverslib/command/TrapezoidProfileCommand.java) runs a raw `TrapezoidProfile` on a timer, feeding each sampled `State` to a `Consumer`. It finishes automatically once the profile's total time has elapsed. This is a good fit when the mechanism already has its own position controller and you just need to stream setpoints to it.

```java
TrapezoidProfile profile = new TrapezoidProfile(
        new TrapezoidProfile.Constraints(1800, 2400),
        new TrapezoidProfile.State(1200, 0),               // goal
        new TrapezoidProfile.State(lift.getPosition(), 0)  // initial
);

TrapezoidProfileCommand followProfile = new TrapezoidProfileCommand(
        profile,
        state -> lift.setTargetPosition(state.position),
        lift
);
```

Note that the profile is baked in when the command is constructed, so build the command right before scheduling it (or in a subclass) so the initial state matches the mechanism's actual position at that moment.
