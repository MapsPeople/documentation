---
description: Zoom-responsive Icon scaling with the Web SDK v4
---

# Icon Zoom Scaling

With Icon zoom scaling, Location Icons grow as the user zooms in, between two zoom levels set for the Solution. How much each Icon grows is the **Zoom scaling** value of its Display Rule. To learn how it works and how to set it up, read the CMS page [Icon Zoom Scaling](../../../products/cms/display-rules/icon-zoom-scaling.md) first.

{% hint style="info" %}
Icon zoom scaling works with the Mapbox map view (`MapboxV3View`) only. Google Maps ignores it.
{% endhint %}

## Requirements

* Web SDK **4.62.0** or later.
* A Solution with Icon zoom scaling turned on in the CMS.

## No code changes needed

The SDK reads the zoom range and the Zoom scaling values from the Solution's CMS configuration and scales the Icons itself. Updating the SDK is all that's needed.

## Changing Zoom scaling at runtime

Zoom scaling is the `iconZoomFactor` property of a Display Rule. You can override it for specific Locations, like any other Display Rule property:

```javascript
// Keep these Locations at their normal size at every zoom level.
mapsIndoors.setDisplayRule(['locationId1', 'locationId2'], { iconZoomFactor: 1 });

// Let a Location grow to 1.5 times its normal size.
mapsIndoors.setDisplayRule('locationId3', { iconZoomFactor: 1.5 });

// Go back to the value from the CMS.
mapsIndoors.setDisplayRule('locationId3', null);
```

`iconZoomFactor` must be greater than 0 and at most 4. Other values are ignored.

## Reading the zoom range

The zoom range comes from the CMS. You can read it from the Solution config:

```javascript
const { iconScaleZoomFrom, iconScaleZoomTo } = mapsIndoors.getSolutionConfig();
```

Both are MapsIndoors zoom levels. When neither is set, Icon zoom scaling is off for the Solution.

## Dynamic icon scaling module

If your Solution uses the `dynamicIconScaling` module, Icon zoom scaling takes over when a valid zoom range is set, and the module's own Icon collision handling is turned off. Without a zoom range, the module works as before.

## Markers drawn by your app

The SDK only scales what it draws. If your app draws its own callouts, labels or markers at a fixed offset from a MapsIndoors Icon, they won't move as the Icon grows and may overlap it. Contact MapsPeople support if you need help adjusting them.
