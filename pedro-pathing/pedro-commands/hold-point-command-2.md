---
description: package com.seattlesolvers.solverslib.pedropathing
---

# TurnToCommand

This command calls Pedro Pathing's [follower.turnTo(radians)](https://github.com/Pedro-Pathing/PedroPathing/blob/main/core/src/main/java/com/pedropathing/follower/Follower.java#L675), which allows you to easily turn directly to a certain amount of radians (or degrees) in place.

It has three parameters, with the first two being mandatory:

1. Pedro Pathing's Follower (which controls the robot movement)
2. The angle to turn by (default is Radians)

```java
TurnToCommand(Follower follower, double angle)

// Example
new TurnToCommand(follower, Math.PI / 2)
```

3. An optional parameter for a custom `AngleUnit` to turn to (`AngleUnit.Radians` or `AngleUnit.Degrees`)

```java
TurnToCommand(Follower follower, double angle, AngleUnit angleUnit)

// Example
new TurnToCommand(follower, 90.0, AngleUnit.DEGREES)
```

To see how you can use this command in a [`CommandOpMode`](../../command-base/command-system/robot-and-commandopmode.md#commandopmode), you can look at this [example](https://github.com/FTC-23511/SolversLib/blob/master/examples/src/main/java/org/firstinspires/ftc/teamcode/PedroCommandSample/PedroCommands.java).
