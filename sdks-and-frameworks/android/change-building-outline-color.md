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

Note that only the polygon stroke color, width, opacity, visible, zoomFrom and zoomTo values are respected.