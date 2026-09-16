---
description: import com.seattlesolvers.solverslib.pedroCommand.TurnCommand;
---

# TurnCommand

This command turns the robot in place by a certain amount of radians (or degrees), relative to its heading when the command starts. Pedro Pathing 3 removed `follower.turn()`, so the command holds the follower's current position with the new heading using `follower.hold(Pose)`.

It has four parameters, with the first three being mandatory:

1. Pedro Pathing's Follower (which controls the robot movement)
2. The angle to turn by (default is Radians)
3. Whether or not the robot should turn left (counterclockwise)

```java
TurnCommand(Follower follower, double angle, boolean isLeft)

// Example
new TurnCommand(follower, Math.PI / 2, true)
```

4. An optional parameter for a custom `AngleUnit` to turn by (`AngleUnit.RADIANS` or `AngleUnit.DEGREES`)

```java
TurnCommand(Follower follower, double angle, boolean isLeft, AngleUnit angleUnit)

// Example
new TurnCommand(follower, 90.0, true, AngleUnit.DEGREES)
```

## How it ends

The command finishes once the heading error is within the heading tolerance, which is 0.01 radians (about 0.57 degrees) by default, the same default as Pedro Pathing 2.x's `turnHeadingErrorThreshold`. You can change it with the `setHeadingTolerance` decorator, in radians or with a custom `AngleUnit`:

```java
new TurnCommand(follower, 90.0, true, AngleUnit.DEGREES).setHeadingTolerance(1.0, AngleUnit.DEGREES)
```

Only the heading error is checked, not the angular velocity, so the command can finish while the robot is still rotating through the target. There is no timeout: if the heading controller cannot get within the tolerance the command never finishes, so loosen the tolerance or add `withTimeout(...)` in autonomous routines. The follower keeps holding the pose after the command ends.

To see how you can use this command in a [`CommandOpMode`](../../command-base/command-system/robot-and-commandopmode.md#commandopmode), you can look at this [example](https://github.com/FTC-23511/SolversLib/blob/master/examples/src/main/java/org/firstinspires/ftc/teamcode/PedroCommandSample/PedroCommands.java).
