# Using a Custom Mapbox MapStyle

MapsIndoors as default uses a custom Mapbox MapStyle to access certain features on the Mapbox map.

You can create your own custom style in [Mapbox Studio](https://www.mapbox.com/mapbox-studio) and have MapsIndoors use that instead of the default used by MapsIndoors.

You will need to initialise Mapbox with your custom Mapbox MapStyle and disable the default MapsIndoors is using.

Below are examples of how to do that in the various SDKs.

### Web Javascript SDK

```javascript
const mapView = new mapsindoors.mapView.MapboxV3View({
   ...,
    style: "URI_TO_YOUR_CUSTOM_STYLE"
});
```

### Android SDK

{% hint style="warning" %}
This requires using Mapbox 11, i.e. you will need to use the MapsIndoors dependency `com.mapspeople.mapsindoors:mapbox-v11` in your `build.gradle` file.
{% endhint %}

```kotlin
mMap.loadStyle("URI_TO_YOUR_CUSTOM_STYLE")
val mapConfig = MPMapConfig.Builder(
    requireActivity(),
    mMap,
    mMapView,
    getString(R.string.mapbox_api_key),
    false
).build()
MapControl.create(mapConfig) {mc, error ->
    if (error != null) {
        // Handle error
    } else {
        mMapControl = mc
    }
}
```

### iOS SDK

{% hint style="warning" %}
This requires using Mapbox 11, i.e. you will need to use the Swift Package Manager distribution of the MapsIndoors iOS SDK or specify the `MapsIndoorsMapbox11` pod in your `Podfile`.
{% endhint %}

```swift
self.mapView.mapboxMap.loadStyle("URI_TO_YOUR_CUSTOM_STYLE") { _ in
    Task {
        do {
            try await MPMapsIndoors.shared.load(apiKey: "YOUR_API_KEY")
            let mapConfig = MPMapConfig(mapBoxView: self.mapView, accessToken: "1234")
            mapConfig.useMapsIndoorsStyle(value: false)
            self.mapControl = MPMapsIndoors.createMapControl(mapConfig: mapConfig)
        }
    }
}
```

### Flutter Plugin

```dart
mapController = MapsIndoorsWidget(
    useDefaultMapsIndoorsStyle: false,
    mapStyleUri: "URI_TO_YOUR_CUSTOM_STYLE",
)
```

### React Native Module

```javascript
const createMapControl = async () => {
   //Set useDefaultMapsIndoorsStyle to false when creating the MPMapConfig
   let config = new MPMapConfig({useDefaultMapsIndoorsStyle: false});
   const mc = await MapControl.create(config, NativeEventEmitter);
}

//Set the MapboxMapStyle when creating the view
<MapView
    style={{
        width: Dimensions.get('window').width,
        height: Dimensions.get('window').height
    }}
    mapboxMapStyle={'URI_TO_YOUR_CUSTOM_STYLE'}
/>
```
