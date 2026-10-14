---
description: Zoom-responsive Icon scaling with the iOS SDK v4
---

# Icon Zoom Scaling

With Icon zoom scaling, Location Icons grow as the user zooms in, between two zoom levels set for the Solution. How much each Icon grows is the **Zoom scaling** value of its Display Rule. To learn how it works and how to set it up, read the CMS page [Icon Zoom Scaling](../../../products/cms/display-rules/icon-zoom-scaling.md) first.

{% hint style="info" %}
Icon zoom scaling works with Mapbox only. Google Maps ignores it.
{% endhint %}

## Requirements

* iOS SDK **4.23.0** or later.
* A Solution with Icon zoom scaling turned on in the CMS.

## No code changes needed

The SDK reads the zoom range and the Zoom scaling values from the Solution's CMS configuration and scales the Icons itself. Updating the SDK is all that's needed.

## Changing Zoom scaling at runtime

Zoom scaling is the `iconZoomFactor` property of an `MPDisplayRule`. You can change it on the Main Display Rule, on a Location Type's Display Rule, or on a single Location's Display Rule:

```swift
// Let every Icon grow to twice its normal size.
MPMapsIndoors.shared.displayRuleFor(displayRuleType: .main)?.iconZoomFactor = 2

// Leave a Location Type out: its Icons keep their normal size.
MPMapsIndoors.shared.displayRuleFor(type: "Workstation")?.iconZoomFactor = 1

// Apply the change straight away.
mapControl.refresh()
```

`iconZoomFactor` must be greater than 0 and at most 4. Other values are ignored and logged, and the previous value is kept.

## Changing the zoom range at runtime

The zoom range comes from the CMS, but you can override it on `MPSolutionConfig`. Both values are MapsIndoors zoom levels:

```swift
let config = MPMapsIndoors.shared.solution?.config

// Grow Icons between zoom level 18 and 22.
config?.iconScaleZoomFrom = 18
config?.iconScaleZoomTo = 22

// Read the range in use. nil means Icon zoom scaling is off.
let band = config?.iconScaleZoomBand   // band.zoomFrom, band.zoomTo

// Turn Icon zoom scaling off.
config?.iconScaleZoomFrom = nil
config?.iconScaleZoomTo = nil

mapControl.refresh()
```

The range must satisfy 1 ≤ from < to ≤ 24. An invalid range turns Icon zoom scaling off; it is never corrected into a different range. Changes to the Solution config don't redraw the map by themselves, so call `refresh()` on your `MPMapControl` to apply them straight away.

## Markers drawn by your app

The SDK only scales what it draws. If your app draws its own callouts, labels or markers at a fixed offset from a MapsIndoors Icon, they won't move as the Icon grows and may overlap it. Contact MapsPeople support if you need help adjusting them.
