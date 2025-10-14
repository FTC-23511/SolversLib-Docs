---
description: package com.seattlesolvers.solverslib.solversHardware
---

# SolversHardware

SolversLib also offers much simpler motor wrappers for motor caching & easy usage of [Axon Servos](https://axon-robotics.com/collections/servos). The motors and servos also include their parent hardware methods (such as `isBusy()` for DcMotors).

## powerThreshold

Each SolversHardware class has a second parameter called `powerThreshold` . If the power set to that hardware is less than that threshold, it will not send a hardware write. The intent of this is to reduce the amount of unnecessary hardware writes.&#x20;

## SolversMotor

```java
SolversMotor(DcMotor motor, double powerThreshold)
```

SolversMotor takes a DcMotor as a parameter. As such, you can create the DcMotor seperately or put it inside the SolversMotor.

```java
// Seperate
DcMotor dcMotor = hardwareMap.dcMotor.get("dcMotor");
SolversMotor solversMotor = new SolversMotor(dcMotor, 0.01); // Second paramater is powerThreshold

// Combined
SolversMotor solversMotor = new SolversMotor(hardwareMap.get(DcMotor.class, "dcMotor"), 0.01); // Second paramater is powerThreshold
```

## SolversServo

```java
SolversServo(Servo servo, double posThreshold)
```

SolversServo takes a Servo as a parameter. As such, you can create the Servo seperately or put it inside the SolversServo.

```java
// Seperate
Servo servo = hardwareMap.dcMotor.get("servo");
SolversServo solversServo = new SolversServo(servo, 0.01); // Second paramater is powerThreshold

// Combined
SolversServo solversServo = new SolversServo(hardwareMap.get(Servo.class, "servo"), 0.01); // Second paramater is powerThreshold
```

## SolversCRServo

```java
SolversCRServo(CRServo servo, double posThreshold)
```

SolversCRServo takes a CRServo as a parameter. As such, you can create the CRServo seperately or put it inside the SolversCRServo.

```java
// Seperate
CRServo crservo = hardwareMap.dcMotor.get("crservo");
SolversCRServo solversServo = new SolversCRServo(servo, 0.01); // Second paramater is powerThreshold

// Combined
SolversCRServo solversServo = new SolversCRServo(hardwareMap.get(CRServo.class, "crservo"), 0.01); // Second paramater is powerThreshold
```

## SolversAxonServo

```java
SolversAxonServo(@NonNull CRServo crservo, double powerThreshold)
```

SolversAxonServo, is similar to a CRServo, and features caching.

```java
// Seperate
CRServo crservo = hardwareMap.dcMotor.get("crservo");
SolversAxonServo solversAxonServo = new SolversAxonServo(crservo, 0.01); // Second paramater is powerThreshold

// Combined
SolversAxonServo solversAxonServo = new SolversAxonServo(hardwareMap.get(CRServo.class, "solversAxonServo"), 0.01); // Second paramater is powerThreshold
```

But not only that, it also features easy usage of the Axon absolute encoder. To set it to an absoluteEncoder plugged into a hub's AnalogInput, simply  call `setServoEncoder(AnalogInput absoluteEncoder)` :

```java
solversAxonServo.setServoEncoder(hardwareMap.get(AnalogInput.class, "axonServoEncoder"));
```

You can also seperate the `AnalogInput` if desired.

To get the Axon Servo's raw position, simply call `solversAxonServo.getRawPosition()`, which uses the math provided on Axon Documentation's guide for [getting the position of Axon Servos](https://docs.axon-robotics.com/axon-servos/analog-jst-board#code-for-getting-the-position-of--series-servos). This method returns a double of the current, actual position of the servo in radians. Keep in mind that the readings are absolute, not relative.

If you instead want the Axon Servo's normalized position (degrees), call`solversAxonServo.getPosition()`  instead. This returns the position in degrees minus an offset (default offset is 0).

To set a custom offset other than, you can call `solversAxonServo.setOffset(double offset)`.
