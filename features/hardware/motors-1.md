---
description: package com.seattlesolvers.solverslib.solversHardware
---

# SolversHardware

SolversLib also offers much simpler motor wrappers for motor caching & easy usage of [Axon Servos](https://axon-robotics.com/collections/servos).&#x20;

## powerThreshold

Each SolversHardware class has a second parameter called `powerThreshold` . If the power set to that hardware is less than that threshold, it will not send a hardware write. The intent of this is to reduce the amount of unnecessary hardware writes.&#x20;

## Creating a SolversMotor Object

SolversMotor takes a DcMotor as a parameter. As such, you can create the DcMotor seperately or put it inside the SolversMotor.

```java
// Seperate
DcMotor dcMotor = hardwareMap.dcMotor.get("dcMotor");
SolversMotor intakeMotor = new SolversMotor(dcMotor, 0.01); // Second paramater is tolerance

// Combined
SolversMotor solversMotor = new SolversMotor(hardwareMap.get(DcMotor.class, "solversMotor"), 0.01); // Second paramater is tolerance
```

