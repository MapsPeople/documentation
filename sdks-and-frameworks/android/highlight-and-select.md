---
description: >-
  This documentation refers to the introduced concept of select and highlight
  that was released with SDK 4.3.0
---

# Highlight and Select

### How to change the appearance of different states

The state Display Rules controls how Locations are displayed on the map in different states. For example, you can change the icon scale of a Location when it is hovered over or highlight a search result. The state Display Rules gives access to the same properties as the regular [Display Rules](display-rules-in-practice.md), which can be used to control the appearance of Locations.

The Android SDK supports Select and Highlight Display Rules these can be received and edited through `MapsIndoors` \
\
**Example:**&#x20;

<pre class="language-kotlin"><code class="lang-kotlin"><strong>//Changing the visibility of a polygon for selection
</strong><strong>MapsIndoors.getDisplayRule(MPSolutionDisplayRule.SELECTION)?.let {
</strong>    it.isPolygonVisible = true
}

//Changing the visibility of a label for highlights
MapsIndoors.getDisplayRule(MPSolutionDisplayRule.HIGHLIGHT)?.let {
    it.isLabelVisible = false
}
</code></pre>

### Highlight

Highlight is for changing the appearance for a collection of `MPLocation`'s, for example to highlight where the restrooms are located in an office building.&#x20;

**Highlight all Restrooms:**

```kotlin
//Create a filter to only receive locations with the category Toilet
val filter = MPFilter.Builder().setCategories(Collections.singletonList("Toilet")).build()
//Query locations with the created filter
MapsIndoors.getLocationsAsync(null, filter) { locations, error ->
    if (locations != null) {
        //Highliting all current toilets, with default MPHighlightBehavior. 
        //The MPHighlightBehavior can be used to customize the camera and map behavior when applying it.
        //Like fitting the view to show all highlighted locations, if possible.
        mMapControl?.setHighlight(locations, MPHighlightBehavior.DEFAULT)
    }
}
```

<figure><img src="../../.gitbook/assets/Screenshot_20240109-133021.png" alt="" width="375"><figcaption><p>An example of the highlight solution Display Rule in action</p></figcaption></figure>

**Clear the highlight**

```kotlin
mMapControl?.clearHighlight()
```

### Selection

The selection Display Rule is for changing the appearance of a single Location, for example when the user clicks on it. To select a Location manually, call the [MapControl.selectLocation(location: MPLocation, behavior: MPSelectionBehavior)](https://app.mapsindoors.com/mapsindoors/reference/android/4.2.10/MapsIndoorsSDK/com.mapsindoors.core/-map-control/index.html#504222062%2FFunctions%2F651798082) method.

```kotlin
mMapControl?.selectLocation(location, MPSelectionBehavior.DEFAULT)
```

<figure><img src="../../.gitbook/assets/Screenshot_20240109-140318.png" alt="" width="279"><figcaption><p>An example of the selection solution DisplayRule in action</p></figcaption></figure>

**Clear current selection:**

```kotlin
mMapControl?.deSelectLocation()
```



### Previous selection

Before the release of 4.3.0 the Android SDK already had a visual implementation of selection with a Display Rule. This is the [`MPSolutionDisplayRule.SELECTION_HIGHLIGHT`](https://app.mapsindoors.com/mapsindoors/reference/android/4.2.10/MapsIndoorsSDK/com.mapsindoors.core/-m-p-solution-display-rule/index.html#-486711157%2FClasslikes%2F651798082) that can also be changed like the newly introduced `MPSolutionDisplayRule`. If you want to retain the old selection rendering, it can be toggled through the Solution Config on the SDK. \
\
**Example:**

```
MapsIndoors.load(application, "myapikey") { e: MIError? ->
    //This will work on any subsequent mapcontrol loads or previously loaded mapcontrols.
    MapsIndoors.getSolution()?.config?.isNewSelection = false
}
```

Note that this is based on the Solution, so with any subsequent load of MapsIndoors, it will be necessary to set the value to `false` again, as it defaults to `true`.
