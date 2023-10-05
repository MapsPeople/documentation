---
description: iOS v4
---

# Change Building Outline Color

To change the building outline color, along with other display properties, you must get and modify the Building Outline Display Rule.

```
let buildingOutline = MPMapsIndoors.shared.displayRuleFor(displayRuleType: .buildingOutline)
buildingOutline?.polygonStrokeColor = .blue
```
