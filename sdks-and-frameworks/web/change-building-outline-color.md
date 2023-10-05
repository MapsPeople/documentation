---
description: Web V4
---

# Change Building Outline Color

To change the building outline color, use the `strokeColor` property of the `BuildingOutlineOptions` interface. This property accepts any color as defined by conventional CSS color values. See [https://developer.mozilla.org/en-US/docs/Web/CSS/color\_value](https://developer.mozilla.org/en-US/docs/Web/CSS/color\_value) for more information on CSS color values.

To do this in practice, on the MapsIndoors instance, call `setBuildingOutlineOptions` to change the appearance of the building outline.

```
mapsIndoors.setBuildingOutlineOptions({strokeColor: '#3071d9'});
```

Additional variables such as `strokeWeight` (for the thickness of the stroke) can also be used here, see the [reference docs](https://app.mapsindoors.com/mapsindoors/js/sdk/latest/docs/BuildingOutlineOptions.html) for the full list.

You can also modify the fill color of a building, not just the outline. This is done using Display Rules, and more can be read about that [here](https://docs.mapsindoors.com/display-rules#polygon).
