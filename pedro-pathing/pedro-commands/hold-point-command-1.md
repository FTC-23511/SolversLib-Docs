---
description: package com.seattlesolvers.solverslib.pedropathing
---

# TurnCommand

This command calls Pedro Pathing's [follower.turn(radians, isLeft)](https://github.com/Pedro-Pathing/PedroPathing/blob/main/core/src/main/java/com/pedropathing/follower/Follower.java#L665), which allows you to easily turn a certain amount of radians (or degrees) in place.

It has four parameters, with the first three being mandatory:

1. Pedro Pathing's Follower (which controls the robot movement)
2. The angle to turn by (default is Radians)
3. Whether or not the robot should turn left (counterclockwise)

```java
TurnCommand(Follower follower, double angle, boolean isLeft)

// Example
new TurnCommand(follower, Math.PI / 2, true)
```

4. An optional parameter for a custom `AngleUnit` to turn by (`AngleUnit.Radians` or `AngleUnit.Degrees`)

```java
TurnCommand(Follower follower, double angle, boolean isLeft, AngleUnit angleUnit)

// Example
new TurnCommand(follower, 90.0, true, AngleUnit.DEGREES)
```

To see how you can use this command in a [`CommandOpMode`](../../command-base/command-system/robot-and-commandopmode.md#commandopmode), you can look at this [example](https://github.com/FTC-23511/SolversLib/blob/master/examples/src/main/java/org/firstinspires/ftc/teamcode/PedroCommandSample/PedroCommands.java).
