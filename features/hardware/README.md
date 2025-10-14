---
description: package com.seattlesolvers.solverslib.hardware
---

# Hardware

Each hardware device in SolversLib is based on the `HardwareDevice` interface. This comes with two methods inherited by every device:

* `disable()`: disables the device
* `getDeviceType()`: returns a String characterization of the device

Optional power caching has also been added to the `Ex` type classes as well to help further loop times as well.

SolversLib offers _a lot_ of hardware devices that can be implemented or customized into your program. The best advice we can give to users is to take a look at the subpages in this catogery ([Servos](servos.md), [Motors](motors.md), and [Sensors](sensors.md)) and the [hardware package](https://github.com/FTC-23511/SolversLib/tree/master/core/src/main/java/com/seattlesolvers/solverslib/hardware) in the SolversLib repository.
