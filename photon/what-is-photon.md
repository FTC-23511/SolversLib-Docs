---
description: package com.seattlesolvers.solverslib.photon
---

# What is Photon?

Photon parallelizes hardware writes for servos and motors to make your robot's loop times faster. This is a stable version of the [now-archived PhotonCore](https://github.com/Eeshwar-Krishnan/PhotonFTC), that doesn't have probems.

{% hint style="warning" %}
Photon should be a last resort for improving loop times. Other traditional ways to help improve loop times can be found at [https://cookbook.dairy.foundation/improving\_loop\_times/improving\_loop\_times.html](https://cookbook.dairy.foundation/improving_loop_times/improving_loop_times.html).
{% endhint %}

#### How it Works

Normally with the SDK, after a setPower() or other LynxCommand, it waits for it to finish and check that it happened. But with Photon, after a setPower() or other LynxCommand, it immediately moves onto the next line of code/hardware write.

It is most effective with Swerve, because of the high amount of hardware writes frequently happening (4 servos + 4 motors).

#### Installation

See [installation page](../installation.md#build.gradle).

#### Usage

See [usage page](usage.md).

