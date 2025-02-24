---
description: import com.seattlesolvers.solverslib.pedroCommand.old.FollowPathChainCommand;
---

# FollowPathChainCommand

This command allows you to easily follow a PathChain **only**.&#x20;

**It is now deprecated because** [**FollowPathCommand**](../pedro-commands/followpathcommand.md) **handles both Paths and PathChains.**

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
