---
description: import com.seattlesolvers.solverslib.pedroCommand.FollowPathCommand;
---

# FollowPathCommand

This command allows you to easily follow a Path (see [FollowPathChainCommand](followpathchaincommand.md) for following a PathChain) by calling Pedro Pathing's [Follower.followPath(Path path)](https://github.com/Pedro-Pathing/PedroPathing/blob/main/src/main/java/com/pedropathing/follower/Follower.java#L499).

It has two mandatory parameters:

* Pedro Pathing's Follower (which controls the robot movement)
* The Path to follow
* An optional boolean parameter called holdEnd that decides whether or not the robot should hold its position at the end of the Path (default value is true if not supplied)

```java
new FollowPathCommand(follower, path)
```

If you are using an ArrayList of Paths, you can simply get the desired PathChain that you want:

```java
new FollowPathCommand(follower, path.get(0))
```

To see how you can use both this command and [FollowPathChainCommand](followpathchaincommand.md) in a full Autonomous Program, look at this [example](https://github.com/FTC-23511/SolversLib/blob/master/examples/src/main/java/org/firstinspires/ftc/teamcode/FollowPedroSample.java).
