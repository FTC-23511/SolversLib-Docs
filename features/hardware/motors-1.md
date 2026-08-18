---
description: Deprecated Classes & Interfaces
---

# Deprecated Hardware

### SolversHardware

SolversLib used to offers much simpler motor wrappers for motor caching & easy usage of [Axon Servos](https://axon-robotics.com/collections/servos). However, it is now deprecated, as these features have been merged into SolversLib's [native hardware classes](./), which has been rewritten to better support it.

### ServoEx Interface and SimpleServo Class

Additionally, the [ServoEx](https://github.com/FTC-23511/SolversLib/blob/master/core/src/main/java/com/seattlesolvers/solverslib/hardware/ServoEx.java) interface and [SimpleServo](https://github.com/FTC-23511/SolversLib/blob/master/core/src/main/java/com/seattlesolvers/solverslib/hardware/SimpleServo.java) class have been Deprecated, in favor of the new and superior [ServoEx](https://github.com/FTC-23511/SolversLib/blob/public-beta/core/src/main/java/com/seattlesolvers/solverslib/hardware/servos/ServoEx.java) class (which has [updated documentation](servos.md#servoex)). A copy of the original ServoEx interface documentation is below.

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
