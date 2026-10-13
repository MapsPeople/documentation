---
description: Zoom-responsive Icon scaling with the Android SDK v4
---

# Icon Zoom Scaling

With Icon zoom scaling, Location Icons grow as the user zooms in, between two zoom levels set for the Solution. How much each Icon grows is the **Zoom scaling** value of its Display Rule. To learn how it works and how to set it up, read the CMS page [Icon Zoom Scaling](../../../products/cms/display-rules/icon-zoom-scaling.md) first.

{% hint style="info" %}
Icon zoom scaling works with Mapbox only. Google Maps ignores it.
{% endhint %}

## Requirements

* Android SDK **4.23.0** or later.
* A Solution with Icon zoom scaling turned on in the CMS.

## No code changes needed

The SDK reads the zoom range and the Zoom scaling values from the Solution's CMS configuration and scales the Icons itself. Updating the SDK is all that's needed.

## Changing Zoom scaling at runtime

Zoom scaling is the `iconZoomFactor` property of a Display Rule. You can change it on the Main Display Rule, on a Location Type's Display Rule, or on a single Location's Display Rule.

{% tabs %}
{% tab title="Java" %}
```java
// Let every Icon grow to twice its normal size.
MPDisplayRule mainRule = MapsIndoors.getMainDisplayRule();
if (mainRule != null) {
    mainRule.setIconZoomFactor(2.0);
}

// Leave a Location Type out: its Icons keep their normal size.
MPDisplayRule workstations = MapsIndoors.getDisplayRule("Workstation");
if (workstations != null) {
    workstations.setIconZoomFactor(1.0);
}

// Apply the change straight away.
mapControl.refresh();
```
{% endtab %}

{% tab title="Kotlin" %}
```kotlin
// Let every Icon grow to twice its normal size.
MapsIndoors.getMainDisplayRule()?.iconZoomFactor = 2.0

// Leave a Location Type out: its Icons keep their normal size.
MapsIndoors.getDisplayRule("Workstation")?.iconZoomFactor = 1.0

// Apply the change straight away.
mapControl.refresh()
```
{% endtab %}
{% endtabs %}

`iconZoomFactor` must be greater than 0 and at most 4.

## Changing the zoom range at runtime

The zoom range comes from the CMS, but you can override it on the Solution config. Both values are MapsIndoors zoom levels:

{% tabs %}
{% tab title="Java" %}
```java
MPSolutionConfig config = MapsIndoors.getSolution().getConfig();

// Grow Icons between zoom level 18 and 22.
config.setIconScaleZoomBand(18.0, 22.0);

// Read the range in use. null means Icon zoom scaling is off.
IconScaleZoomBand band = config.getIconScaleZoomBand();

// Turn Icon zoom scaling off.
config.setIconScaleZoomBand(null, null);

mapControl.refresh();
```
{% endtab %}

{% tab title="Kotlin" %}
```kotlin
val config = MapsIndoors.getSolution()?.config

// Grow Icons between zoom level 18 and 22.
config?.setIconScaleZoomBand(18.0, 22.0)

// Read the range in use. null means Icon zoom scaling is off.
val band = config?.iconScaleZoomBand

// Turn Icon zoom scaling off.
config?.setIconScaleZoomBand(null, null)

mapControl.refresh()
```
{% endtab %}
{% endtabs %}

The range must satisfy 1 ≤ from < to ≤ 24. An invalid range turns Icon zoom scaling off; it is never corrected into a different range. Changes to the Solution config don't redraw the map by themselves, so call `refresh()` on your `MapControl` to apply them straight away.

## Markers drawn by your app

The SDK only scales what it draws. If your app draws its own callouts, labels or markers at a fixed offset from a MapsIndoors Icon, they won't move as the Icon grows and may overlap it. Contact MapsPeople support if you need help adjusting them.
