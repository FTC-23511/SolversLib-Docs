---
description: import com.seattlesolvers.solverslib.pedroCommand.FollowPathCommand;
---

# FollowPathCommand

This command calls Pedro Pathing's `follower.follow(Path)`, which allows you to easily follow a `Path`. In Pedro Pathing 3 a chain of paths is also a `Path` (`Paths.path(path1, path2, ...)`), so the same command follows single paths and chains.

The path needs a heading interpolation (for example `Paths.line(start, end).linear(start, end)`), otherwise Pedro Pathing throws an exception when the path is followed.

It has four parameters, with the first two being mandatory:

1. Pedro Pathing's Follower (which controls the robot movement)
2. The Path to follow

```java
new FollowPathCommand(follower, path)
```

3. An optional boolean parameter called holdEnd that decides whether the robot should hold its position at the end of the Path (true) or go idle (false). The default value is true if not supplied.

```java
new FollowPathCommand(follower, path, true)
```

4. An optional double parameter called maxPower that limits the robot's speed for this path only, as a fraction (0 to 1) of its maximum achievable speed

```java
new FollowPathCommand(follower, path, true, 0.5)
new FollowPathCommand(follower, path, 0.5) // holdEnd stays true
```

You can use a decorator to set the globalMaxPower for the follower as follows:

```java
new FollowPathCommand(follower, path).setGlobalMaxPower(0.5)
```

Setting the Global Maximum Power limits this path and every later `FollowPathCommand` for the same follower that is not given its own `maxPower`. It takes effect when the command runs (not when it is created) and is remembered for as long as the follower exists, which is normally the OpMode. Setting `maxPower` as a parameter overwrites `globalMaxPower` for that path only, and passing 1.0 to `setGlobalMaxPower()` removes the limit again.

{% hint style="info" %}
Under the hood `maxPower` temporarily overrides Foresight's `maxPathSpeed`, so it only limits the target speed while the robot is coasting along the path: braking, error correction, holding and TeleOp driving are not affected, unlike Pedro Pathing 2.x's `maxPower` which capped motor power. Because of this, `maxPower` and `setGlobalMaxPower()` require the follower to use Pedro Pathing's Foresight algorithm (the default); commands without a `maxPower` work with any algorithm.
{% endhint %}

## How it ends

* `holdEnd` temporarily overrides the follower's `holdEnd` setting while the command runs and restores the previous value when the command ends.
* With `holdEnd` true, the command finishes once the follower is no longer busy: the robot has settled at the end pose within Foresight's [end constraints](https://pedropathing.com/docs/pathing/reference/endconstraints), or the `timeoutConstraint` (100 ms by default) has elapsed there. The follower keeps holding the end pose afterwards.
* With `holdEnd` false, the command finishes as soon as the path ends and the follower goes idle. Note that Pedro Pathing's `follower.isBusy()` stays true after this until the next path or hold.
* If the command is interrupted (for example by another command that needs the follower, or a timeout), the follower holds its current pose (or stops, with `holdEnd` false) instead of continuing along the path.

To see how you can use this command in a [`CommandOpMode`](../../command-base/command-system/robot-and-commandopmode.md#commandopmode), you can look at this [example](https://github.com/FTC-23511/SolversLib/blob/master/examples/src/main/java/org/firstinspires/ftc/teamcode/PedroCommandSample/PedroCommands.java). For usage in a full Autonomous Program, look at this [example](https://github.com/FTC-23511/SolversLib/blob/master/examples/src/main/java/org/firstinspires/ftc/teamcode/PedroCommandSample/PedroAutoSample.java), and for a full TeleOp Program, at this [example](https://github.com/FTC-23511/SolversLib/blob/master/examples/src/main/java/org/firstinspires/ftc/teamcode/PedroCommandSample/PedroTeleOpSample.java).
