---
title: dependencies {    // implem...
---

{% code title="build.gradle (Module: TeamCode)" %}
```groovy
dependencies {
    // implementation "org.ftclib.ftclib:core:2.1.1" remove FTCLib core
    // FTCLib's vision is no longer supported in SolversLib
    implementation "org.solverslib:core:0.2.3" // core
    implementation "org.pedroPathing:core:0.2.3" // pedroPathing
}
```
{% endcode %}
