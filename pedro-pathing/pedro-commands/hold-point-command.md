---
description: import com.seattlesolvers.solverslib.pedroCommand.HoldPointCommand;
---

# HoldPointCommand

This command calls Pedro Pathing's `follower.hold(Pose)`, which allows you to easily hold a pose or make a small adjustment to the robot's position.

It has three mandatory parameters:

* Pedro Pathing's Follower (which controls the robot movement)
* The Pose to hold (heading in radians)
* A boolean parameter called isFieldCentric that decides whether the pose is field centric or robot centric (relative to the follower's pose at the time the command starts)

```java
HoldPointCommand(Follower follower, Pose pose, boolean isFieldCentric)

// Examples
new HoldPointCommand(follower, new Pose(72, 72, Math.toRadians(90)), true) // a pose on the field
new HoldPointCommand(follower, new Pose(4, 0, 0), false) // 4 inches forwards from where the robot is
```

For robot centric poses (`isFieldCentric` false) the pose is an offset in the robot's frame, following Pedro Pathing's coordinate conventions:

* `Pose.x()`: +x is forwards, -x is backwards
* `Pose.y()`: +y is left, -y is right
* `Pose.heading()`: heading is in radians, +heading turns left (counterclockwise) and -heading turns right

## How it ends

The command finishes once the follower is no longer busy, which Pedro Pathing decides the same way as at the end of a path: the robot has settled within Foresight's [end constraints](https://pedropathing.com/docs/pathing/reference/endconstraints), or the `timeoutConstraint` (100 ms by default) has elapsed. That timeout starts on the first follower update after the command starts, so for a pose that is far away the command ends while the robot is still moving. Use a [`FollowPathCommand`](followpathcommand.md) for longer moves, or raise the timeout constraint if you need to wait for the robot to arrive. The follower keeps holding the pose after the command ends, until it is given something else to do.

{% hint style="info" %}
**Changed from SolversLib 0.3.5:** with Pedro Pathing 2.x this command finished immediately, used the follower's hold point scaling, and ignored `isFieldCentric` because of a bug (the pose was always treated as field centric). It now holds at full correction power and the robot centric mode works as described above.
{% endhint %}

To see how you can use this command in a [`CommandOpMode`](../../command-base/command-system/robot-and-commandopmode.md#commandopmode), you can look at this [example](https://github.com/FTC-23511/SolversLib/blob/master/examples/src/main/java/org/firstinspires/ftc/teamcode/PedroCommandSample/PedroCommands.java).
