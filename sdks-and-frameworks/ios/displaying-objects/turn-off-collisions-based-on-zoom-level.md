---
description: iOS v4
---

# Turn Off Collisions Based on Zoom Level

First, you must define your zoom range, where you wish for the behaviour to occur. Then, if not already done for other purposes in your code, you must implement `MPMapControlDelegate` on your view controller, and then use it to listen for changes in `didChangeCameraPosition()`.

```swift
// Define zoom range
let minZoom = 16.0
let maxZoom = 22.0
let zoomRange = (minZoom...maxZoom)

// 1. Adhere to MPMapControlDelegate in your view controller.
class YourClassName: UIViewController, MPMapControlDelegate

// 2. Use MPMapControlDelegate to listen to camera changes (zoom lvel change is part of that) with didChangeCameraPosition()

self.mapControl?.delegate = self // This is needed since the delegate will inform of event updates on the map view; we will use it below.

// The following is invoked on every camera change. You can place zoom checking anywhere in your code that is being updated.
func didChangeCameraPosition() -> Bool {
    // 3. do a check against the current projection level and make changes
    // The following is the actual to be put in method that is invoked everytime there is a zoom level change
    if (zoomRange ~= (self.mapControl?.cameraPosition.zoom)!) {
        MPMapsIndoorsShared.solution.config.collisionHandling = .removeIconAndLabel
    } else {
        MPMapsIndoorsShared.solution.config.collisionHandling = .allowOverLap
    }
}
```
