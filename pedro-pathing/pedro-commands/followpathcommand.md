---
description: import com.seattlesolvers.solverslib.pedroCommand.FollowPathCommand;
---

# FollowPathCommand

This command allows you to easily follow a Path or PathChain.

If a Path is supplied, it will simply convert it to a PatchChain first, and then follow that.

It has four parameters, with the first two being mandatory:

1. <mark style="color:$success;">Pedro Pathing's Follower (which controls the robot movement)</mark>&#x20;
2. <mark style="color:$success;">The Path or PathChain to follow</mark>

```java
new FollowPathCommand(follower, pathChain)
```

3. <mark style="color:$warning;">An optional boolean parameter called holdEnd that decides whether or not the robot should hold its position at the end of the Path (default value is true if not supplied)</mark>

```java
new FollowPathCommand(follower, pathChain, true)
```

4. <mark style="color:$warning;">An optional double parameter called maxPower that sets the maximum power the robot will run at for the path</mark>

```java
new FollowPathCommand(follower, pathChain, true, 0.5)
```

You can use a decorater to set the globalMaxPower for the follower as follows:

```java
new FollowPathCommand(follower, pathChain).setGlobalMaxPower(0.5)
```

Setting the Global Maximum Power sets the maximum power `globalMaxPower` for all future paths (unless rewritten again). However, setting the `maxPower` as a parameter in `FollowPathCommand` overwrites `globaMaxPower` for that path only.   &#x20;

To see how you can use both this command in a full Autonomous Program, look at this [example](https://github.com/FTC-23511/SolversLib/blob/public-beta/examples/src/main/java/org/firstinspires/ftc/teamcode/PedroCommandSample/PedroAutoSample.java), and for a full TeleOp Program, at this [example](https://github.com/FTC-23511/SolversLib/blob/public-beta/examples/src/main/java/org/firstinspires/ftc/teamcode/PedroCommandSample/PedroTeleOpSample.java).
