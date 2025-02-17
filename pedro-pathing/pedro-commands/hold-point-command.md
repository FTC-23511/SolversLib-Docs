---
description: package com.seattlesolvers.solverslib.pedropathing
---

# HoldPointCommand

This command calls Pedro Pathing's [Follower.holdPoint(Pose)](https://github.com/Pedro-Pathing/PedroPathing/blob/main/src/main/java/com/pedropathing/follower/Follower.java#L473), which allows you to easily hold a new Point (the Pose parameter is converted into a Point under the hood).

It has three mandatory parameters:

* Pedro Pathing's Follower (which controls the robot movement)
* The Pose to hold
* A boolean parameter called isFieldCentric that decides whether the move should be field centric or robot centric (based off the follower's position at the time of scheduling the command)

```java
HoldPointCommand(Follower follower, Pose pose, boolean isFieldCentric)
```

The following robot centric movements for Pedro Pathing's default coordinate system should be true assuming that the robot is facing forwards to the long side on the submersible's blue alliance side:

* [Pose.getX()](https://github.com/Pedro-Pathing/PedroPathing/blob/main/src/main/java/com/pedropathing/localization/Pose.java#L112): -Y is forwards, +Y is backwards
* [Pose.getY()](https://github.com/Pedro-Pathing/PedroPathing/blob/main/src/main/java/com/pedropathing/localization/Pose.java#L121): +X is left, -X is right
* [Pose.getHeading()](https://github.com/Pedro-Pathing/PedroPathing/blob/main/src/main/java/com/pedropathing/localization/Pose.java#L130): Heading is in radians, +heading turns left and -heading turns right
