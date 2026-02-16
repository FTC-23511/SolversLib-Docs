---
description: How to import SolversLib into your Android Studio FTC Project
---

# Installation

## Option 1: Manually Install

This works with or without having [FTCLib](https://ftclib.org/) already installed. SolversLib allows you to easily migrate from FTCLib to SolversLib while keeping all of your code, or install normally.

### build.gradle

The first thing you need to change from FTCLib is the dependency in `build.gradle`

{% code title="build.gradle (Module: TeamCode)" %}
```groovy
dependencies {
    // implementation "org.ftclib.ftclib:core:2.1.1" remove FTCLib core
    implementation "org.solverslib:core:0.3.4" // core
```
{% endcode %}

Or, if you are using [Pedro Pathing](/broken/pages/r7t7Ipj9bB5rG4YSi6pu), change to this dependency block

{% code title="build.gradle (Module: TeamCode)" %}
```groovy
dependencies {
    // implementation "org.ftclib.ftclib:core:2.1.1" remove FTCLib core
    implementation "org.solverslib:core:0.3.4" // core
    implementation "org.solverslib:pedroPathing:0.3.4" // pedroPathing
}
```
{% endcode %}

And if you are using [Photon](/broken/pages/p4vt3VlMfnz4BWiYKZVT), change to this dependency block

{% code title="build.gradle (Module: TeamCode)" %}
```groovy
dependencies {
    // implementation "org.ftclib.ftclib:core:2.1.1" remove FTCLib core
    implementation "org.solverslib:core:0.3.4" // core
    implementation "org.solverslib:pedroPathing:0.3.4" // pedroPathing
    implementation "org.solverslib:photon:0.0.1" // photon
}
```
{% endcode %}

{% hint style="danger" %}
**Important:** Different versions of SolversLib correlate with different versions of Pedro Pathing.

[SolversLib 0.3.3](installation.md)+ is for Pedro Pathing 2.0.0 and higher\
[SolversLib 0.3.2](https://app.gitbook.com/s/qHp1wuxUd3u5XDcqQNPs/installation) is for Pedro Pathing 1.0.9\
[SolversLib 0.3.1](https://app.gitbook.com/s/4WjwIOqhnAnCe7dwK3v4/installation) is for Pedro Pathing 1.0.8
{% endhint %}

The latest version numbers (as well as a list of all version numbers) are available at:

* Latest `core` version: [https://repo.dairy.foundation/#/releases/org/solverslib/core](https://repo.dairy.foundation/#/releases/org/solverslib/core)
* Latest `pedroPathing` version: [https://repo.dairy.foundation/#/releases/org/solverslib/pedroPathing](https://repo.dairy.foundation/#/releases/org/solverslib/pedroPathing)
* Latest `photon` version: [https://repo.dairy.foundation/#/releases/org/solverslib/photon](https://repo.dairy.foundation/#/releases/org/solverslib/photon)

{% hint style="danger" %}
**Warning:** If you choose to use the Pedro Pathing module, you still need to [install Pedro Pathing 2.0.0](https://pedropathing.com/)+ in order to use it.
{% endhint %}

Please note that you should not and cannot have both FTCLib and SolversLib installed at the same time.

### Repositories (required)

Finally in your repositories block, add the following code. You may have other content here, especially if you have the Pedro Pathing library installed. If you do not have a repositories block, you can add it above your dependencies block.

{% code title="build.gradle (Module: TeamCode)" %}
```groovy
repositories {
    maven {
        url "https://repo.dairy.foundation/releases"
    }
}
```
{% endcode %}

### Changing Imports (Only if Migrating from FTCLib)

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

## Option 2: Installing from SolversLib Quickstart

An alternative option is to simply use the SolversLib Quickstart. Similar to the FTCLib Quickstart, SolversLib has a Quickstart with this library fully set up. You can view it at [https://github.com/FTC-23511/SolversLib-Quickstart](https://github.com/FTC-23511/SolversLib-Quickstart). You can either fork or clone this repository as needed to use it.&#x20;

In addition, the Quickstart also has the Pedro Pathing library installed and added along with the SolversLib `pedroPathing`\` dependency, meaning that it is hassle-free. If you don't want the Pedro Pathing part, you can simply delete the relevant files and dependencies.

### Sync Gradle and Finished!

![Click that button and if successful, you can now use SolversLib](.gitbook/assets/gradle-sync.png)
