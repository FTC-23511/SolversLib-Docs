---
description: import com.seattlesolvers.solverslib.pedroCommand.FollowPathCommand;
---

# FollowPathCommand

This command allows you to easily follow a Path or PathChain [Follower.followPath(PathChain pathChain)](https://github.com/Pedro-Pathing/PedroPathing/blob/main/src/main/java/com/pedropathing/follower/Follower.java#L519).&#x20;

If a Path is supplied, it will simply convert it to a PatchChain first, and then follow that.

It has two mandatory parameters:

* Pedro Pathing's Follower (which controls the robot movement)
* The Path or PathChain to follow
* An optional boolean parameter called holdEnd that decides whether or not the robot should hold its position at the end of the Path (default value is true if not supplied)

```java
new FollowPathCommand(follower, pathChain)
```

If you are using an ArrayList of PathChains/Paths, you can simply get the desired PathChain/Path that you want:

```java
new FollowPathCommand(follower, path.get(0))
```

To see how you can use both this command in a full Autonomous Program, look at this [example](https://github.com/FTC-23511/SolversLib/blob/master/examples/src/main/java/org/firstinspires/ftc/teamcode/FollowPedroSample.java).
