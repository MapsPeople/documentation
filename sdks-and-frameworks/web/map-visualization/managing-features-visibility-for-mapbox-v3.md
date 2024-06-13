# Managing feature visibility for Mapbox

## Overview

From Mapbox v3 (Web) and v11 (Mobile), it's now possible to control what features (layers) should be shown or hidden at runtime. We are introducing three new methods to enhance the control and visibility of features on your Mapbox map. With these methods, you can easily manipulate the visibility of specific features, enhancing the user experience and providing more control over map customization. As an example, you could easily change between a 2D and a 3D map at runtime, without reloading the map data.

## Methods

### `hideFeatures(features: string[]): void`

This method allows you to hide specific Mapbox features by passing an array of feature identifiers. Once hidden, the features will no longer be visible on the map. This can be useful for scenarios where certain map elements need to be temporarily removed from view.

**Parameters**

* `features` (Array): An array of `FeatureType` objects representing the features to be hidden.

**Switching between 2D and 3D features example:**

```javascript
// Creating two arrays: one which hides 3D features,
// and the other one that hides 2D features.
const features3DToHide = [
    FeatureType.WALLS3D, 
    FeatureType.MODEL3D, 
    FeatureType.EXTRUSION3D,
    FeatureType.EXTRUDEDBUILDINGS
];

const features2DToHide = [
    FeatureType.MODEL2D,
    FeatureType.WALLS2D
];

// Then calling each function either on button click or toggle.
function hide3DFeatures() {
    mapView.hideFeatures(features3DToHide);
}

function hide2DFeatures() {
    mapView.hideFeatures(features2DToHide);
}

// Calling hideFeatures with an empty array as a parameter will result in
// showing all the features.
function showFeatures() {
    mapView.hideFeatures([])
}
```

Example when calling `hide3DFeatures()` function:

<figure><img src="../../../.gitbook/assets/Screenshot 2024-06-05 at 12.48.32.png" alt=""><figcaption><p>Only 2D features visible</p></figcaption></figure>

Example when calling `hide2DFeatures()` function:

<figure><img src="../../../.gitbook/assets/Screenshot 2024-06-05 at 12.52.32.png" alt=""><figcaption><p>Only 3D features visible</p></figcaption></figure>

### `getHiddenFeatures(): string[]`

This method retrieves the currently hidden features on the map. It returns an array of feature identifiers representing the features that are currently not visible.

**Returns**

* `string[]`: An array of strings representing the identifiers of the currently hidden features.

**Example**

```javascript
mapView.hideFeatures([FeatureType.MODEL2D, FeatureType.WALLS2D])
const hiddenFeatures = mapView.getHiddenFeatures();
console.log(hiddenFeatures); // [MODEL2D, WALLS2D]
```

### `FeatureType(): string[]`

This method retrieves all the available feature identifiers that can have their visibility set to "none". It returns an array of these feature identifiers, providing developers with an overview of the features that can be hidden.

**Returns**

* `string[]`: An array of strings representing the identifiers of the features that can be hidden.

**Example**

```javascript
const features = mapView.FeatureType();
console.log(features); // [MODEL2D, WALLS2D, MODEL3D, WALLS3D, EXTRUSION3D, EXTRUDEDBUILDINGS]
```



**NOTE**: `MapboxFeatures()` method is deprecated. It still works as expected but will be removed within next major release.

## Conclusion

You now have powerful tools to control the visibility of features on your maps. By utilizing the `hideFeatures`, `FeatureType`, and `getHiddenFeatures` methods, you can enhance user experience and customize map displays according to your needs.



See example of the implementation [here](../../../products/fast-track-maptemplate/2d-3d-visibility-switch.md).
