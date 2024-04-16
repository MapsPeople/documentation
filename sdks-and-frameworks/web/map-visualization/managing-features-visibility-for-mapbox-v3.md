# Managing features visibility for Mapbox v3

#### Overview

The Mapbox v3 Map View Feature Management feature introduces three new methods to enhance the control and visibility of features on your Mapbox map. With these methods, developers can easily manipulate the visibility of specific features, enhancing user experience and providing more control over map customisation.

#### Methods

#### `hideFeatures(features: string[]): void`

This method allows you to hide specific Mapbox features by passing an array of feature identifiers. Once hidden, the features will no longer be visible on the map. This can be useful for scenarios where certain map elements need to be temporarily removed from view.

**Parameters**

* `features` (Array): An array of `MapboxFeature` objects representing the features to be hidden.

**Example:**

```javascript
const featuresToHide = [MapboxFeature.MODEL2D, MapboxFeature.MODEL3D, MapboxFeature.EXTRUSION3D];
mapView.hideFeatures(featuresToHide);
```

#### `MapboxFeatures(): string[]`

This method retrieves all the available Mapbox v3 feature identifiers that can have their visibility set to "none". It returns an array of these feature identifiers, providing developers with an overview of the features that can be hidden.

**Returns**

* `string[]`: An array of strings representing the identifiers of the features that can be hidden.

**Example**

```javascript
const features = mapView.MapboxFeatures();
console.log(features); // [MODEL2D, WALLS2D, MODEL3D, WALLS3D, EXTRUSION3D, EXTRUDEDBUILDINGS]
```

#### `getHiddenFeatures(): string[]`

This method retrieves the currently hidden features on the map. It returns an array of feature identifiers representing the features that are currently not visible.

**Returns**

* `string[]`: An array of strings representing the identifiers of the currently hidden features.

**Example**

```javascript
mapView.hideFeatures([MapboxFeature.MODEL2D, MapboxFeature.WALLS2D])
const hiddenFeatures = mapView.getHiddenFeatures();
console.log(hiddenFeatures); // [MODEL2D, WALLS2D]
```

#### Conclusion

The Mapbox v3 Map View Feature Management feature provides developers with powerful tools to control the visibility of features on their maps. By utilizing the `hideFeatures`, `MapboxFeatures`, and `getHiddenFeatures` methods, developers can enhance user experience and customise map displays according to their application's requirements.
