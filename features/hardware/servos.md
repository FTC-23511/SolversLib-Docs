---
description: package com.seattlesolvers.solverslib.hardware
---

# Servos

## ServoEx

The [ServoEx](https://github.com/FTC-23511/SolversLib/blob/master/core/src/main/java/com/seattlesolvers/solverslib/hardware/ServoEx.java) class allows for more methods and actions than the normal servo class in the SDK. You can change the position of the servo relative to the last position or set it to an absolute position. You can either specify a position within the range of the servo's motion or have it rotate a certain number of specified angle units.

It serves as a successor to the [ServoEx](https://github.com/FTC-23511/SolversLib/blob/master/core/src/main/java/com/seattlesolvers/solverslib/hardware/ServoEx.java) interface and [SimpleServo](https://github.com/FTC-23511/SolversLib/blob/master/core/src/main/java/com/seattlesolvers/solverslib/hardware/SimpleServo.java) class, which have now been Deprecated. A copy of the original ServoEx interface documentation can be viewed [here](motors-1.md#servoex-interface-and-simpleservo-class).

### Constructors:

#### 1. Regular (no Angle Control)

```java
ServoEx(HardwareMap hwMap, String id)

ServoEx servoEx = new ServoEx(hardwareMap, "servoEx");
```

#### 2. Angle Control (with Min and Max angle)

```java
ServoEx(HardwareMap hwMap, String id, double min, double max)
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

#### 3. Angle Control (with Range and AngleUnit)

```java
ServoEx(HardwareMap hwMap, String id, double range, AngleUnit angleUnit)
```

This is similar to the 2nd constructor, except that you specify the angular range (from when the servo is set to 0 to 1). As such, you also need to define the AngleUnit (Degrees or Radians) for that range.

### Utility Methods:

You can use `setInverted()` invert the servo's direction as well:

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
ServoEx servoEx = new ServoEx(hardwareMap, "servoEx");

servoEx.setCachingTolerance(0.0001);
```

## CRServo

The [CRServo](https://github.com/FTC-23511/SolversLib/blob/master/core/src/main/java/com/seattlesolvers/solverslib/hardware/motors/CRServo.java) class is just a motor object intended to be used for a continuous rotation servo. Its general purpose is to be used in SolversLib classes that require a `Motor` input. It works just like a regular motor, without any of the encoder stuff. As such, it extends the `Motor` class, and can be used in a [CRServoGroup](servos.md#crservogroup).

```java
CRServo crServo = CRServo(hardwareMap, "CRServo");

crServo.set(0.5);
```

## CRServoEx & AbsoluteAnalogEncoder

The `CRServoEx` class is an advanced wrapper for continuous rotation servos (CRServos), adding key features for enhanced control and integration, including:

* **Absolute analog encoder support** (e.g., for Axon servos)
* **Optimized positional control using PIDF** (required for absolute encoder)
* **Power caching for improved loop performance**
* **Custom PWM range support**

It it extends `CRServo`, which in turn extends the `Motor` class, and can also be used in a [CRServoGroup](servos.md#crservogroup).



The `AbsoluteAnalogEncoder` class is an advanced wrapper for Analog input AnalogInput absolute encoders, which are most commonly seen on servos with a 4th wire (like Axon Servos). It is best used in conjuction with `CRServoEx` .

### Constructors (AbsoluteAnalogEncoder)

#### 1. Basic AbsoluteAnalogEncoder

```java
AbsoluteAnalogEncoder(HardwareMap hwMap, String id)

// Example
AbsoluteAnalogEncoder encoder = new AbsoluteAnalogEncoder(hardwareMap, "absoluteEncoder");
```

This defaults to a range of 3.3, and an `AngleUnit` of Radians. If you do not know what you need, this is most likely it (and is functional for Axon servos).

#### 2. Advanced CRServo

```java
AbsoluteAnalogEncoder(HardwareMap hwMap, String id, double range, AngleUnit angleUnit)

// Example
AbsoluteAnalogEncoder absoluteEncoder = new AbsoluteAnalogEncoder(hwMap, "encoder", 3.3, AngleUnit.RADIANS)
```

This allows you to set your own range and An`gleUnit` if necessary. These constructors are used automatically in the designated CRServoEx constructors below as well.

* `angleUnit`: Angle unit for moving servo to position (`AngleUnit.DEGREES` or `AngleUnit.RADIANS`)
* `analogRange`: Voltage range for the encoder (e.g., 3.3V or 5V, depending on hardware)
  * **Default: 3.3V**
* `encoderID`: Name of the absolute encoder in hardware map

&#x20;

&#x20;   &#x20;

### Constructors (CRServoEx)

#### 1. Basic CRServo (Raw Power)

```java
CRServoEx(HardwareMap hwMap, String id)
```

* No encoder required; runs in `RawPower` mode by default. This is similar to `CRServo` , with a few extra features.

#### 2. Positional Control with Encoder

```java
CRServoEx(HardwareMap hwMap, String id, AbsoluteAnalogEncoder absoluteEncoder, RunMode runmode)

// Example
CRServoEx crServoEx = new CRServoEx(hardwareMap, "crServoEx", encoder, CRServoEx.RunMode.OptimizedPositionalControl)

```

* `hwMap`: FTC HardwareMap instance
* `id`: Configuration name of the CRServo
* `absoluteEncoder`: Instance of `AbsoluteAnalogEncoder` (must be initialized separately)
* `runmode`: Mode to run (see below)

{% hint style="danger" %}
**Warning:** If you have set the runmode to `OptimizedPositionalControl`, regardless if you want angle-based control, you must use a valid `AbsoluteAnalogEncoder` and also set the `PIDF` (see below for more information). Failing to do will result in an error being thrown.
{% endhint %}

#### 3. Advanced Encoder Configuration

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
      CRServoEx crServoEx = new CRServoEx(hardwareMap, "crServoEx", encoder, CRServoEx.RunMode.OptimizedPositionalControl)

      crServoEx.setPIDF(new PIDFCoefficients(0.001, 0.0, 0.1, 0.0001));
      crServoEx.set(Math.toRadians(90)); // move to 90 degrees (in radians)
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
**Important:** These PIDF coeffecients are used in SolversLib's PIDF class for the calculations. As such, you should tune it as if you were to tune a normal PIDF.
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
CRServoEx crServoEx = new CRServoEx(hardwareMap, "s_crServoEx", encoder, CRServoEx.RunMode.OptimizedPositionalControl);
crServoEx.setPIDF(new PIDFCoefficients(0.8, 0.02, 0.1, 0.0));
crServoEx.setCachingTolerance(0.0002);

crServoEx.set(Math.toRadians(135));
```

### Additional Methods

* `.setRunMode(RunMode runmode)`: Change runmode after construction.
* `.setAbsoluteEncoder(AbsoluteAnalogEncoder encoder)`: Switch encoder instance.
* `.getAbsoluteEncoder()`: Get associated encoder.
* `.getCachingTolerance()`: Retrieve current tolerance.
* `.getController()`: Get extended controller instance.
* `.getServo()`: Get underlying SDK CRServo object.
* `.getDeviceType()`: Returns device type (string).
* `.setPwm`: Sets the PWM range for the servo using

For more details, refer to the Javadocs within `CRServoEx.java`. The class supports method chaining for convenient setup and configuration.

## CRServoGroup

The CRServoGroup is like a MotorGroup, but for CRServo/CRServoEx. A CRServo group object takes several CRServos and runs them in parallel like a single CRServo. CRServo groups have one leader and a set of followers. For any group, there _must_ be a leader, but the number of followers can be zero. This makes creating different drive profiles simpler. The constructor for a `CRServoGroup` is as follows:

```java
CRServoEx myCRServos = new CRServoGroup(leader, follower1, follower2, ...);
```

The number of followers is variable. The other methods of the `CRServoGroup` are the same as the ones found in `CRServo`. You can very simply treat a `CRServoGroup` object like a single `CRServo` object. The [flywheel sample](https://github.com/FTC-23511/SolversLib/blob/master/examples/src/main/java/org/firstinspires/ftc/teamcode/FlywheelSample.java) in the examples folder shows a few other methods you can utilize with the `MotorGroup`.

{% hint style="danger" %}
**Warning:** CRServo/CRServoEx should use a CRServoGroup (this) instead of a [MotorGroup](motors.md#motorgroup)
{% endhint %}
