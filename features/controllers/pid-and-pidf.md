---
description: package com.seattlesolvers.solverslib.controller
---

# PID and PIDF

## PID Control

The following post from Noah in the [FTC Discord](https://discord.gg/first-tech-challenge) best explains PID control.

> You'll hear the term PID Controller used a lot (F is tacked on sometimes) in robotics. It's relatively simple and pretty effective at a lot of simple tasks. A PID controller is a form of "closed loop control." This basically just means you're altering an input to some "plant" based on feedback. This concept applies to a wide range of actions but we'll take a look at velocity PID control as that is what's relevant for this year's game. So say you have a goBILDA 3:1 1620RPM motor powering a flywheel. You want that flywheel to spin at a constant speed to ensure consistency between your shots. So you run a `motor.setPower(0.5)` which sends 50% of 12v to the motor. The motor is getting a 6v signal (technically not true bc of PWM but that's another topic). The motor should be running at 810 RPM right? That's 50% of 1620RPM. Chances are, it's not actually running at this speed. Motors have +- 10% tolerance between them. The voltage-torque curve isn't linear. Or there is something resisting the motor (like the inertia of the flywheel) so it takes extra power to get it up to that speed. So how do we actually ensure that our motor is running at exactly 810RPM? Most FTC motors come with an encoder built it. This allows us to measure the velocity of the output shaft. So with the encoder all hooked up, we know that our motor isn't spinning as fast as we want it to. But how do we actually correct for this? You slap a PID Controller on it. A PID controller is a pretty basic controller (despite the daunting equation when you look it up) that basically responds to your the difference between your measured velocity and desired velocity (error) and will add more or less power based on error. You just check the velocity every loop, feed the value in the controlled, and it gives you the power you want to set it to the desired velocity.

For information on the theory behind PID control, we recommend reading [this page](https://gm0.org/en/stable/docs/software/control-loops.html) in gm0 and [this page](https://www.ctrlaltftc.com/the-pid-controller) in CtrlAltFtc.&#x20;

The following video does a good job explaining each gain:

{% embed url="https://www.youtube.com/watch?v=XfAt6hNV8XM" %}

Our base class is `PIDFController` for the SolversLib PID control scheme. This class performs the calculations for PIDF, which are proportional, integral, derivative, and feedforward values. The additional F term is an additional gain for creating offset, for purposes like maintaining a position, counteracting weight/gravity, or overcoming friction.

### Using the PIDFController Class

#### Constructing a PIDFController

In order to use SolversLib's PIDF control functionality, users must first construct a `PIDFController` object with the desired gains:

```java
// Creates a PIDFController with gains kP, kI, kD, and kF
PIDFController pidf = new PIDFController(kP, kI, kD, kF);

/*
 * Here are the constructors for the other controllers
 */
PIDController pid = new PIDController(kP, kI, kD);
PDController pd = new PDController(kP, kD);
PController p = new PController(kP);
```

You can also pass in an additional two parameters: the setpoint and previous value. The default values for these are 0.

You can also change the gain constants even after creating the controller object.

```java
// set our gains to some value
pidf.setP(0.37);
pidf.setI(0.05);
pidf.setD(1.02);

// get our gain constants
kP = pidf.getP();
kI = pidf.getI();
kD = pidf.getD();

// set all gains
pidf.setPIDF(kP, KI, kD, 0.7);

// get all gain coefficients
double[] coeffs = pidf.getCoefficients();
kP = coeffs[0];
kI = coeffs[1];
kD = coeffs[2];
kF = coeffs[3];
```

These gains must be tuned. An example of tuning PID can be found on [this page](https://www.learnroadrunner.com/drive-velocity-pid-tuning.html) of Learn Road Runner.

#### Using the Feedback Loop Output

The `calculate()` method should be called each iteration of the control loop. The controller uses timestamps to calculate the difference in time between each call of the method, which means it adjusts based on the loop time. You can obtain the cycle time of your current loop iteration by calling `getPeriod()`.

Using the constructed `PIDFController` is simple: call the `calculate()` method from the main loop.

```java
// Calculates the output of the PIDF algorithm based on sensor
// readings. Requires both the measured value
// and the desired setpoint
double output = pidf.calculate(
    motor.getCurrentPosition(), setpoint
);

/*
 * A sample control loop for a motor
 */
PController pController = new PController(kP);

// We set the setpoint here.
// Now we don't have to declare the setpoint
// in our calculate() method arguments.
pController.setSetPoint(1200);

// perform the control loop
/*
 * The loop checks to see if the controller has reached
 * the desired setpoint within a specified tolerance
 * range
 */
while (!pController.atSetPoint()) {
  double output = pController.calculate(
    m_motor.getCurrentPosition()  // the measured value
  );
  m_motor.setVelocity(output);
}
m_motor.stopMotor(); // stop the motor

// NOTE: motors have internal PID control
```

#### Checking Errors

The methods `getPositionError()` and `getVelocityError()` are named assuming that the loop is controlling a position - for a loop that is controlling a velocity, these return the velocity error and the acceleration error, respectively.

The current error of the measured process variable is returned by the `getPositionError()` function, while its derivative is returned by the `getVelocityError()` function.

#### Specifying and Checking Tolerances

If only a position tolerance is specified, the velocity tolerance defaults to infinity.

As above, “position” refers to the process variable measurement, and “velocity” to its derivative - thus, for a velocity loop, these are actually velocity and acceleration, respectively.

Occasionally, it is useful to know if a controller has tracked the setpoint to within a given tolerance - for example, to determine if a command should be ended, or (while following a motion profile) if motion is being impeded and needs to be re-planned.

To do this, we first must specify the tolerances with the `setTolerance()` method; then, we can check it with the `atSetPoint()` method.

```java
// Sets the error tolerance to 5, and the error derivative
// tolerance to 10 per second
pidf.setTolerance(5, 10);

// Returns true if the error is less than 5 units, and the
// error derivative is less than 10 units
pidf.atSetPoint();
```

#### Resetting the Controller

It is sometimes desirable to clear the internal state (most importantly, the integral accumulator) of a `PIDFController`, as it may be no longer valid (e.g. when the `PIDFController` has been disabled and then re-enabled). This can be accomplished by calling the `reset()` method.
