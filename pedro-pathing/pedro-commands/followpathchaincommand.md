---
description: import com.seattlesolvers.solverslib.pedroCommand.FollowPathChainCommand;
---

# FollowPathChainCommand

This command allows you to easily follow a PathChain (see [FollowPathCommand](followpathcommand.md) for following a Path) by calling Pedro Pathing's[ Follower.followPath(PathChain pathChain, double maxPower, boolean holdEnd)](https://github.com/Pedro-Pathing/PedroPathing/blob/main/src/main/java/com/pedropathing/follower/Follower.java#L531).

It has two mandatory parameters:

* Pedro Pathing's Follower (which controls the robot movement)
* The PathChain to follow
* An optional boolean parameter called holdEnd that decides whether or not the robot should hold its position at the end of the PathChain (default value is true if not supplied)

```java
new FollowPathChainCommand(follower, pathChain)
```

If you are using an ArrayList of PathChains, you can simply get the desired PathChain that you want:

```java
new FollowPathChainCommand(follower, paths.get(0))
```

To see how you can use both this command and [FollowPathCommand](followpathcommand.md) in a full Autonomous Program, look at this [example](https://github.com/FTC-23511/SolversLib/blob/master/examples/src/main/java/org/firstinspires/ftc/teamcode/FollowPedroSample.java).
