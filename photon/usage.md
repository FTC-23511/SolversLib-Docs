---
description: package com.seattlesolvers.solverslib.photon
---

# Usage

Photon only has 1 main requirement: you must use USB to connect hubs (USB 3.0 on Control Hub to USB mini b on expansion hub), NOT RS485. As Photon only works on USB-connected hubs (REV Servo Hub and SPM doesn't connect over USB), so using the [goBILDA Servo Power Injector](https://www.gobilda.com/6v-servo-power-injector-6-channel-8-15v-input/) allows for full servo power (ideal for Swerve) while being Photon-compatible.&#x20;

{% hint style="danger" %}
**Warning:** Using USB instead of RS485 to communicate between hubs is not optional. You must use it for stable results, even if you are only running Photon on one hub.
{% endhint %}

To use in code with manual bulk caching:

```java
// Setup in your Robot class if you have one, or in init at start of opMode
// Don't do manual or auto bulk caching elsewhere - do it here.
PhotonCore.CONTROL_HUB.setBulkCachingMode(LynxModule.BulkCachingMode.MANUAL);
PhotonCore.EXPANSION_HUB.setBulkCachingMode(LynxModule.BulkCachingMode.MANUAL);
PhotonCore.experimental.setMaximumParallelCommands(8); // Can be adjusted based on user preference - but raising this number further can cause issues
PhotonCore.enable();
```

Then, in every single run loop, you must clear the hub caches at the start (or end) of the loop to avoid getting stale values and get new data.

```java
PhotonCore.CONTROL_HUB.clearBulkCache();
PhotonCore.EXPANSION_HUB.clearBulkCache();
```

