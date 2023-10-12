---
description: >-
  The MapsIndoors SDK records anonymous usage statistics and diagnostic events
  per default and sends the logged data to a server at MapsPeople.
---

# Event Logging

Logging may be disabled entirely by calling:

### **Android v4**

{% hint style="info" %}
If you are looking for documentation on Android SDK v3, please [see here](https://docs-legacy.mapsindoors.com/content/legacy/android\_v3/).
{% endhint %}

{% tabs %}
{% tab title="Java" %}
MapsIndoors.disableEventLogging(true);
{% endtab %}

{% tab title="Kotlin" %}
```java
MapsIndoors.disableEventLogging(true)
```


{% endtab %}
{% endtabs %}

### iOS v3

{% hint style="danger" %}
1. Developing on the new Arm-based Apple Silicon (M1) Macs requires building and running on a physical iOS device or using an iOS simulator running iOS 13.7, e.g. iPhone 11. This is a temporary limitation in Google Maps SDK for iOS, and as such also a limitation in MapsIndoors, due to the dependency to Google Maps.
2. Note: Due to [a bug in CocoaPods](https://github.com/CocoaPods/CocoaPods/issues/7155) it is necessary to include the `post_install` hook in your Podfile described in the [PodFile post\_install](https://github.com/MapsIndoors/MapsIndoorsIOS/wiki/Podfile-post\_install) wiki.
{% endhint %}

```swift
MapsIndoors.eventLoggingDisabled = true
```

[\
](https://docs.mapsindoors.com/webex)
