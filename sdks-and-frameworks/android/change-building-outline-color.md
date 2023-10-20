---
description: Android v4
---

# Change Building Outline Color

To change the building outline color, along with other display properties, you must get and modify the Building Outline Display Rule.

Note that the DisplayRule will be null if MapsIndoors is not loaded.

```java
MapsIndoors.getDisplayRule(MPSolutionDisplayRule.BUILDING_OUTLINE).setPolygonStrokeColor(Color.BLUE);
```

The parameter `strokeColor` takes the color in ARGB format (with an alpha-channel value), the syntax being `AARRGGBB`.

Note only the values listed below are respected:

1. `polygonZoomTo` - Sets the maximum Zoom Level at which the Building Outline is visible.
    * Call `setPolygonZoomTo` to change the value on the Display Rule
1. `polygonZoomFrom` - Sets the minimum Zoom Level at which the Building Outline is visible.
    * Call `setPolygonZoomFrom` to change the value on the Display Rule
1. `polygonStrokeColor` - Controls the stroke color of the Building Outline.
    * Call `setPolygonStrokeColor` to change the value on the Display Rule
1. `polygonStrokeWidth` - Controls the stroke width of the Building Outline.
    * Call `setPolygonStrokeWidth` to change the value on the Display Rule
1. `polygonStrokeOpacity` - Controls the stroke opacity of the Building Outline.
    * Call `setPolygonStrokeOpacity` to change the value on the Display Rule
1. `polygonVisibility` - Controls whether the Building Outline is visible on the map.
    * Call `setPolygonVisible` to change the value on the Display Rule
