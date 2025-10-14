---
description: How to import SolversLib into your Android Studio FTC Project
---

# Installation

## 1. Installing from FTCLib

## build.gradle

The first thing you need to change from FTCLib is the dependency in `build.gradle`

{% code title="build.gradle (Module: TeamCode)" %}
```groovy
dependencies {
    // implementation "org.ftclib.ftclib:core:2.1.1" remove FTCLib core
    // FTCLib's vision is no longer supported in SolversLib
    implementation "org.solverslib:core:0.3.2" // add SolversLib core
```
{% endcode %}

Or, if you are using pedroPathing, change to this dependency block

{% code title="build.gradle (Module: TeamCode)" %}
```groovy
dependencies {
    // implementation "org.ftclib.ftclib:core:2.1.1" remove FTCLib core
    // FTCLib's vision is no longer supported in SolversLib
    implementation "org.solverslib:core:0.3.2" // core
    implementation "org.solverslib:pedroPathing:0.3.2" // pedroPathing
}
```
{% endcode %}

{% hint style="warning" %}
**Important:** Different versions of SolversLib correlate with different versions of Pedro Pathing.

[SolversLib 0.3.3](https://app.gitbook.com/s/3GkjSf3vnBih0vqJQqmk/installation) is for Pedro Pathing 2.0.0\
[SolversLib 0.3.2](installation.md) is for Pedro Pathing 1.0.9\
[SolversLib 0.3.1](https://app.gitbook.com/s/4WjwIOqhnAnCe7dwK3v4/installation) is for Pedro Pathing 1.0.8
{% endhint %}

The latest version numbers (as well as a list of all version numbers) are available at:

* Latest `core` version: [https://repo.dairy.foundation/#/releases/org/solverslib/core](https://repo.dairy.foundation/#/releases/org/solverslib/core)
* Latest `pedroPathing` version: [https://repo.dairy.foundation/#/releases/org/solverslib/pedroPathing](https://repo.dairy.foundation/#/releases/org/solverslib/pedroPathing)

{% hint style="danger" %}
**Warning:** If you choose to use the Pedro Pathing module, you still need to [install Pedro Pathing 1.0.9](https://pedropathing.com/) in order to use it.
{% endhint %}

Please note that you should not and cannot have both FTCLib and SolversLib installed at the same time.

**Repositories:**

Then, follow the steps in the [Repositories](installation.md#repositories) section. Then follow the final step below (changing imports).

#### Changing Imports:

Lastly, follow the steps in the [Changing Imports](installation.md#changing-imports-1) section and then Gradle Sync!

## 2. Installing from SolversLib Quickstart

An alternative option is to simply use the SolversLib Quickstart. Similar to the FTCLib Quickstart, SolversLib has a Quickstart with this library fully set up. You can view it at [https://github.com/FTC-23511/SolversLib-Quickstart](https://github.com/FTC-23511/SolversLib-Quickstart). You can either fork or clone this repository as needed to use it.&#x20;

In addition, the Quickstart also has the Pedro Pathing library installed and added along with the SolversLib pedroPathing dependency, meaning that it is hassle-free. If you don't want the Pedro Pathing part, you can simply delete the relevant files.

## 3. Installing from Scratch

## build.common.gradle

First, you need to add the `mavenCentral` library repository to your `build.gradle` file at the project root:

{% code title="build.gradle" %}
```groovy
    repositories {
        mavenCentral()
    }
```
{% endcode %}

Next, `minSdkVersion` to `24` and `multiDexEnabled` to `true`:

{% code title="build.common.gradle" %}
```groovy
defaultConfig {
    applicationId 'com.qualcomm.ftcrobotcontroller'
    minSdkVersion 24
    targetSdkVersion 28
    multiDexEnabled true
```
{% endcode %}

Next, change `JavaVersion` to `8` :

{% code title="build.common.gradle" %}
```groovy
compileOptions {
    sourceCompatibility JavaVersion.VERSION_1_8
    targetCompatibility JavaVersion.VERSION_1_8
}
```
{% endcode %}

## build.gradle

The first thing you need to change from FTCLib is the dependency in `build.gradle`

{% code title="build.gradle (Module: TeamCode)" %}
```groovy
dependencies {
    // implementation "org.ftclib.ftclib:core:2.1.1" remove FTCLib core
    // FTCLib's vision is no longer supported in SolversLib
    implementation "org.solverslib:core:0.3.2" // add SolversLib core
```
{% endcode %}

Or, if you are using pedroPathing, change to this dependency block

{% code title="build.gradle (Module: TeamCode)" %}
```groovy
dependencies {
    // implementation "org.ftclib.ftclib:core:2.1.1" remove FTCLib core
    // FTCLib's vision is no longer supported in SolversLib
    implementation "org.solverslib:core:0.3.2" // core
    implementation "org.solverslib:pedroPathing:0.3.2" // pedroPathing
}
```
{% endcode %}

{% hint style="warning" %}
**Important:** Different versions of SolversLib correlate with different versions of Pedro Pathing.

[SolversLib 0.3.3](https://app.gitbook.com/s/3GkjSf3vnBih0vqJQqmk/installation) is for Pedro Pathing 2.0.0\
[SolversLib 0.3.2](installation.md) is for Pedro Pathing 1.0.9\
[SolversLib 0.3.1](https://app.gitbook.com/s/4WjwIOqhnAnCe7dwK3v4/installation) is for Pedro Pathing 1.0.8
{% endhint %}

The latest version numbers (as well as a list of all version numbers) are available at:

* Latest `core` version: [https://repo.dairy.foundation/#/releases/org/solverslib/core](https://repo.dairy.foundation/#/releases/org/solverslib/core)
* Latest `pedroPathing` version: [https://repo.dairy.foundation/#/releases/org/solverslib/pedroPathing](https://repo.dairy.foundation/#/releases/org/solverslib/pedroPathing)

{% hint style="danger" %}
**Warning:** If you choose to use the Pedro Pathing module, you still need to [install Pedro Pathing 1.0.9](https://pedropathing.com/) in order to use it.
{% endhint %}

Please note that you should not and cannot have both FTCLib and SolversLib installed at the same time.

#### Repositories

Finally, follow the steps in the [Repositories](installation.md#repositories) section.

## Snapshot Versions

SolversLib is graciously hosted on the Dairy Foundation (thanks to Oscar!), and has release versions and snapshots versions.

* Release versions:
  * Are official, verified versions of SolversLibs
  * Less likelier to have problems/bugs
  * Are in the form: `implementation "org.solverslib:core:x.y.z"` (where x, y, and z are version numbers).
* Snapshots versions:
  * Are unofficial, and effectively beta versions with newere features and additions
  * More likelier to have problems/bugs
  * Are in the form: `implementation "org.solverslib:pedroPathing:SNAPSHOT-abc1234"` (7 random letters & numbers).

For most people, it is HIGHLY recommended to use the releases versions. Should you still want to use the snapshots versions instead of releases, you can use the dependencies instead:

* Latest `core` snapshot version: [https://repo.dairy.foundation/#/snapshots/org/solverslib/core](https://repo.dairy.foundation/#/snapshots/org/solverslib/core)
* Latest `pedroPathing` snapshot version: [https://repo.dairy.foundation/#/snapshots/org/solverslib/pedroPathing](https://repo.dairy.foundation/#/snapshots/org/solverslib/pedroPathing)

**Repositories:**

Look at the section below. Make sure to follow the snapshot versions part.

## Repositories

In your repositories block, add the following code. You may have other content here, especially if you have the Pedro Pathing library installed. If you do not have a repositories block, you can add it above your dependencies block.

{% code title="build.gradle (Module: TeamCode)" %}
```groovy
repositories {
    maven {
        url "https://repo.dairy.foundation/releases"
    }
}
```
{% endcode %}

#### Only for Snapshot/Beta Versions

You also need to add the maven for snapshots in your repositories block in addition to the releases one. Again, if you do not have a repositories block, you can add it above your dependencies block.

A Snapshot version (e.g., `SNAPSHOT-7cdcc66`) is a beta version.

{% code title="build.gradle (Module: TeamCode)" %}
```groovy
repositories {
    maven {
        url "https://repo.dairy.foundation/releases"
    }
    maven {
        url "https://repo.dairy.foundation/snapshots"
    }
}
```
{% endcode %}

## Changing Imports (Only if Migrating from FTCLIB)

Because the package names will be different, you can either manually replace all instances of `com.arcrobotics.ftclib` with `com.seattlesolvers.solverslib` , or use a command in a terminal to replace them all at once for you. Please make sure you either open a terminal into your Android Studio project or use the built-in Android Studio terminal to run the commands below.

**FTCLib Imports to SolversLib Imports (MacOS/Linux):**

```bash
find . -type f -name "*.java" -exec sed -i '' 's/com.arcrobotics.ftclib/com.seattlesolvers.solverslib/g' {} +
```

**SolversLib Imports to FTCLib Imports (MacOS/Linux):**

```bash
find . -type f -name "*.java" -exec sed -i '' 's/com.seattlesolvers.solverslib/com.arcrobotics.ftclib/g' {} +
```

**FTCLib Imports to SolversLib Imports  (Windows):**

```powershell
Get-ChildItem -Recurse -Filter *.java | ForEach-Object { 
    (Get-Content $_.FullName) -replace 'com.arcrobotics.ftclib', 'com.seattlesolvers.solverslib' | 
    Set-Content $_.FullName 
}
```

**SolversLib Imports to FTCLib Imports (Windows):**

```powershell
Get-ChildItem -Recurse -Filter *.java | ForEach-Object { 
    (Get-Content $_.FullName) -replace 'com.seattlesolvers.solverslib', 'com.arcrobotics.ftclib' | 
    Set-Content $_.FullName 
}
```

### Sync Gradle and Finished!

![Click that button and if successful, you can now use SolversLib](.gitbook/assets/gradle-sync.png)
