---
description: package com.seattlesolvers.solverslib.pedroCommand
---

# Pedro Commands

One of SolversLib's modern features is easy integration with [Pedro Pathing](https://pedropathing.com/), a popular path following library for Autonomous. To use this, make sure you have both the [pedroPathing module installed](../../installation.md#build.gradle-teamcode) as well as the [Pedro Pathing library installed](https://pedropathing.com/docs/pathing/installation). This dependency is completely separate from core, meaning that it is not installed by default, and should only be used if you are using Pedro Pathing.

SolversLib includes commands for using Pedro Pathing's Follower class, allowing you to fully use command base in your Autonomous OpModes.

{% hint style="warning" %}
SolversLib 0.3.6 and higher works with Pedro Pathing 3.0.0 and higher (`com.pedropathing:revhub`). For Pedro Pathing 2.x use SolversLib 0.3.3 to 0.3.5, see the [Installation](../../installation.md) page.
{% endhint %}

These commands are for following Pedro Pathing's `Path` class (`com.pedropathing.paths.Path`), not SolversLib's Path class. Pedro Pathing 3 removed `PathChain`: a chain of paths is also a `Path`, built with `Paths.path(path1, path2, ...)`.

All of the commands need `follower.update()` to be called every loop (for example in your `CommandOpMode`'s `run()` method), just like when you use Pedro Pathing without commands.

| Command                                     | Calls                                                     | Finishes when                                                                                              |
| ------------------------------------------- | --------------------------------------------------------- | ---------------------------------------------------------------------------------------------------------- |
| [`FollowPathCommand`](followpathcommand.md) | `follower.follow(path)`                                   | the path is done and the robot has settled at the end pose (or the follower goes idle, with holdEnd false) |
| [`HoldPointCommand`](hold-point-command.md) | `follower.hold(pose)`                                     | the follower is no longer busy                                                                             |
| [`TurnCommand`](hold-point-command-1.md)    | `follower.hold(pose)` with the heading turned by an angle | the heading is within the tolerance                                                                        |
| [`TurnToCommand`](hold-point-command-2.md)  | `follower.hold(pose)` with a new field heading            | the heading is within the tolerance                                                                        |
