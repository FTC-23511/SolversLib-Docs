---
description: package com.seattlesolvers.solverslib.photon
---

# Usage

Photon only has 1 main requirement: you must use USB to connect hubs (USB 3.0 on Control Hub to USB mini b on expansion hub), NOT RS485. This is because Photon only works on USB-connected hubs.

{% hint style="danger" %}
**Warning:** Using USB instead of RS485 to communicate between hubs is not optional. You must use it for stable results, even if you are only running Photon on one hub.parallelized
{% endhint %}

{% hint style="info" %}
If you have a camera and wish to use Photon, it is recommended to use a USB 3.0 Hub to ensure that both the Expansion Hub and the camera is connected to the USB 3.0 port.&#x20;
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

By default, both servos and motors will be parallelized. However, because Photon only works on USB-connected hubs, parallelizing servos will cause issues if you are powering servos using the REV Servo Hub, Servo Power Modules, or other non-USB devices. As such, you should disable `PARALLELIZE_SERVOS` when using non-USB devices to power servos.

```java
PhotonCore.PARALLELIZE_SERVOS = true; // Default set to true
PhotonCore.PARALLELIZE_SERVOS = false; // Set to false if using REV Servo Hub
```

{% hint style="info" %}
Using the goBILDA Servo Power Injector allows for full servo power (ideal for Swerve) while being Photon-compatible. This is because it directly uses the hub, and isn't a separate device.
{% endhint %}

Then, in every single run loop, you must clear the hub caches at the start (or end) of the loop to avoid getting stale values and get new data. This is the same for manual bulk reads as well.

```java
PhotonCore.CONTROL_HUB.clearBulkCache();
PhotonCore.EXPANSION_HUB.clearBulkCache();
```

