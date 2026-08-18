---
description: package com.seattlesolvers.solverslib.pedroCommand
---

# Pedro Commands

One of SolversLib's modern features is easy integration with [Pedro Pathing](https://pedropathing.com/), a popular path following library for Autonomous. To use this, make sure you have both the [pedroPathing module installed](../../installation.md#build.gradle-teamcode) as well as the [Pedro Pathing library installed](https://pedropathing.com/docs/pathing). This dependency is completely seperate from core, meaning that it is not installed by default, and should only be used if you are using Pedro Pathing.

SolversLib includes commands for using Pedro Pathing's Follower class, allowing you to fully use command base in your Autonomous OpModes.

These commands are for following Pedro Pathing's Path and PathChain classes, not SolversLib's Path class.&#x20;
