---
description: package com.seattlesolvers.solverslib.hardware
---

# Servos

## ServoEx

The [ServoEx](https://github.com/FTC-23511/SolversLib/blob/master/core/src/main/java/com/seattlesolvers/solverslib/hardware/ServoEx.java) interface allows for more methods and actions than the normal servo class in the SDK. You can change the position of the servo relative to the last position or set it to an absolute position. You can either specify a position within the range of the servo's motion or have it rotate a certain number of specified angle units.

An example implementation of this can be found in the [SimpleServo](https://github.com/FTC-23511/SolversLib/blob/master/core/src/main/java/com/seattlesolvers/solverslib/hardware/SimpleServo.java) class. You can create a simple servo like this:

```java
ServoEx servo = new SimpleServo(
    hardwareMap, "servo_name", MIN_ANGLE, MAX_ANGLE
);

// the above is functionally equivalent to
servo = new SimpleServo(
    hardwareMap, "servo_name", MIN_ANGLE, MAX_ANGLE,
    AngleUnit.DEGREES
);

// if you want to set the range in radians in the constructor
// you can use the following
servo = new SimpleServo(
    hardwareMap, "servo_name", MIN_ANGLE, MAX_ANGLE,
    AngleUnit.RADIANS
);
```

`MIN_ANGLE` and `MAX_ANGLE` are the minimum and maximum angle positions in degrees you would like to set the servo. This functionally serves as the servo's effective range. If you want to change the effective range at any point, you can do the following:

```java
// change the effective range to a min and max in DEGREES
servo.setRange(MIN_ANGLE, MAX_ANGLE);

// change the range to a min and max in RADIANS
servo.setRange(MIN_ANGLE, MAX_ANGLE, AngleUnit.RADIANS);

// return the effective range
double degreeRange = servo.getAngleRange();

// return the effective range in RADIANS
degreeRange = servo.getAngleRange(AngleUnit.RADIANS);
```

You can invert the servo's direction as well:

```java
// invert the servo
servo.setInverted(true);

// get if the servo is inverted (true if inverted, false if not)
boolean isInverted = servo.getInverted();
```

To turn to positions and angles, utilize the following methods:

* `rotateByAngle`: turns the servo a number of angle units relative to the current angle
* `turnToAngle`: sets the absolute angle of the servo
* `rotateBy`: turns the servo a relative positional distance from the current position
* `setPosition`: set the absolute position of the servo (from 0 to 1)

### Power Caching

Additionally, as an `Ex` class, `ServoEx` supports caching. If the power set to that hardware is less than an adjustable threshold, it will not send a write to it to help with loop speeds. The default threshold, which is called `cachingTolerance` , is 0.0001.

You can use `.setCachingTolerance` to adjust `cachingTolerance` it as needed.

```java
ServoEx s_servoEx = new CRServoEx(hardwareMap, "s_servoEx");

s_servoEx.setCachingTolerance(0.0001);
```

## CRServo

The [CRServo](https://github.com/FTC-23511/SolversLib/blob/master/core/src/main/java/com/seattlesolvers/solverslib/hardware/motors/CRServo.java) class is just a motor object intended to be used for a continuous rotation servo. Its general purpose is to be used in SolversLib classes that require a `Motor` input. It works just like a regular motor, without any of the encoder stuff. As such, it extends the `Motor` class, and can be used in a [MotorGroup](motors.md#motorgroup).

```java
CRServo s_crServo = CRServo(hardwareMap, "CRServo");

s_crServo.set(0.5);
```

## CRServoEx & AbsoluteAnalogEncoder

The `CRServoEx` class is an advanced wrapper for continuous rotation servos (CRServos), adding key features for enhanced control and integration, including:

* **Absolute analog encoder support** (e.g., for Axon servos)
* **Optimized positional control using PIDF** (required for absolute encoder)
* **Power caching for improved loop performance**
* **Custom PWM range support**

It it extends `CRServo`, which in turn extends the `Motor` class, and can also be used in a [MotorGroup](motors.md#motorgroup).

### Constructors

#### 1. Positional Control with Encoder

```java
CRServoEx(HardwareMap hwMap, String id, AbsoluteAnalogEncoder absoluteEncoder, RunMode runmode)
```

* `hwMap`: FTC HardwareMap instance
* `id`: Configuration name of the CRServo
* `absoluteEncoder`: Instance of AbsoluteAnalogEncoder (must be initialized separately)
* `runmode`: Mode to run (see below)

{% hint style="danger" %}
**Warning:** If you have set the runmode to `OptimizedPositionalControl`, regardless if you want angle-based control, you must use a valid `AbsoluteAnalogEncoder` and also set the `PIDF` (see below for more information). Failing to do will result in an error being thrown.
{% endhint %}

#### 2. Advanced Encoder Configuration

```java
CRServoEx(HardwareMap hwMap, String id, String encoderID, double analogRange, AngleUnit angleUnit, RunMode runmode)
```

Instead of passing an `AbsoluteAnalogEncoder` in, this overloaded constructor has parameters to create one inside of the instance of the `CRServoEx`.

* Allows specifying encoder parameters:
  * `encoderID`: Name of the absolute encoder in hardware map
  * `analogRange`: Voltage range for the encoder (e.g., 3.3V or 5V, depending on hardware)
    * **Default: 3.3V**
  * `angleUnit`: Angle unit for moving servo to position (`AngleUnit.DEGREES` or `AngleUnit.RADIANS`)
    * **Default**: If not specified, defaults to `AngleUnit.RADIANS` unless overridden
  * `runmode`: See below

#### 3. Basic CRServo (Raw Power)

```java
CRServoEx(HardwareMap hwMap, String id)
```

* No encoder required; runs in `RawPower` mode by default. This is pretty much the same as `CRServo`.

### Using a CRServoEx RunMode

Like the `Motor`'s  RunMode, the CRServoEx RunMode is a method of running the CRServoEx when power is supplied. However, there are only two modes: `OptimizedPositionalControl`, and `RawPower`.

```java
// in CRServoEx.java

/**
 * The mode in which the CR servo should behave.
 */
public enum RunMode {
    /**
     * Mode in which the CR servo takes the shortest path to reach a specific angle
     * Requires PIDF tuning (see below) + absolute encoder
     */
    OptimizedPositionalControl,
    /**
     * Default mode in which the CR servo is controlled with raw power
     */
    RawPower
}
```

#### `OptimizedPositionalControl`

* **Requirements**: An absolute encoder and PIDF coefficients.
* **Behavior**: When you call `.set(angle)`, the servo will move to the target angle using the shortest path.
  *   Example:

      ```java

      AbsoluteAnalogEncoder encoder = new AbsoluteAnalogEncoder(hardwareMap, "absoluteEncoder");
      CRServoEx s_crServoEx = new CRServoEx(hardwareMap, "crServoEx", encoder, CRServoEx.RunMode.OptimizedPositionalControl)

      s_crServoEx.setPIDF(new PIDFCoefficients(0.001, 0.0, 0.1, 0.0001));
      s_crServoEx.set(Math.toRadians(90)); // move to 90 degrees (in radians)
      ```
  * If PIDF not set, positional control will throw an error.

#### `RawPowerMode`

* Acts like a normal CRServo's `RawPower`.

### PIDF Control

For positional control, you **must** set PIDF coefficients:

```java
s_crServoEx.setPIDF(new PIDFCoefficients(double P, double I, double D, double F));
```

* These coefficients will be used by the internal PIDF controller to compute the necessary power to reach the target angle smoothly and quickly.

{% hint style="warning" %}
**Important:** These PIDF coeffecients are used in SolversLib's PIDF class for the calculations. As such, you should tune it as if you were to tune a normal PIDF>
{% endhint %}

### Power Caching

Finally, like `MotorEx` , `CRServoEx` supports power caching. If the power set to that hardware is less than an adjustable threshold, it will not send a write to it to help with loop speeds. The default threshold, which is called `cachingTolerance` , is 0.0001.

You can use `.setCachingTolerance` to adjust `cachingTolerance` it as needed.

```java
CRServoEx s_crServoEx = new CRServoEx(hardwareMap, "s_crServoEx");

s_crServoEx.setCachingTolerance(0.0001);
```

### Example: Full Setup

```java
AbsoluteAnalogEncoder encoder = new AbsoluteAnalogEncoder(hardwareMap, "absoluteEncoder");
CRServoEx s_crServoEx = new CRServoEx(hardwareMap, "s_crServoEx", encoder, CRServoEx.RunMode.OptimizedPositionalControl);
s_crServoEx.setPIDF(new PIDFCoefficients(0.8, 0.02, 0.1, 0.0));
s_crServoEx.setCachingTolerance(0.0002);

s_crServoEx.set(Math.toRadians(135));
```

### Additional Methods

* `.setRunMode(RunMode runmode)`: Change runmode after construction.
* `.setAbsoluteEncoder(AbsoluteAnalogEncoder encoder)`: Switch encoder instance.
* `.getAbsoluteEncoder()`: Get associated encoder.
* `.getCachingTolerance()`: Retrieve current tolerance.
* `.getController()`: Get extended controller instance.
* `.getServo()`: Get underlying SDK CRServo object.
* `.getDeviceType()`: Returns device type (string).
* `.`setPwm`()`: Sets the PWM range for the servo using



For more details, refer to the Javadocs within `CRServoEx.java`. The class supports method chaining for convenient setup and configuration.
