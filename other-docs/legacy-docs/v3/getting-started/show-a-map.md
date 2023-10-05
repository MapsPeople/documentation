# Show a Map

Your environment is now fully configured, and you have the necessary Google Maps and MapsIndoors API keys. Next you will learn how to load a Google Maps map with MapsIndoors.

### Show a Map with MapsIndoors[​](https://docs.mapsindoors.com/getting-started/android/v3/map#show-a-map-with-mapsindoors) <a href="#show-a-map-with-mapsindoors" id="show-a-map-with-mapsindoors"></a>

ASYNC

Please note that data in MapsIndoors is loaded asynchronously. This results in behavior where data might not have finished loading if you call methods accessing it immediately after initializing MapsIndoors. Best practice is to set up `listeners` or `delegates` to inform of when data is ready. Please be aware of this when developing using the MapsIndoors SDK.

#### Initialize MapsIndoors[​](https://docs.mapsindoors.com/getting-started/android/v3/map#initialize-mapsindoors) <a href="#initialize-mapsindoors" id="initialize-mapsindoors"></a>

We start by initializing `MapsIndoors`. `MapsIndoors` is used to get and store all references to MapsIndoors-specific data. This includes access to all MapsIndoors-specific geodata.

Place the following initialization code in the `onCreate` method in the `MapsActivity` that displays the Google map. You should also assign the `mapFragment` view to a local variable, as we will use this later to initialize `MapControl` inside the `onCreate`, after it has been created:

* Java
* Kotlin

[MapsActivity.java](https://github.com/MapsPeople/MapsIndoors-Getting-Started-Android/blob/master/app/src/main/java/com/example/mapsindoorsgettingstarted/MapsActivity.java#L60-L65)

```
protected void onCreate(Bundle savedInstanceState) {
    ...
    mMapView = mapFragment.getView();
    MapsIndoors.initialize(getApplicationContext(), "YOUR_MAPSINDOORS_API_KEY");
    MapsIndoors.setGoogleAPIKey(“YOUR_GOOGLE_API_KEY”);
    ...
}
```

If you are not a customer you can use this demo MapsIndoors API key `d876ff0e60bb430b8fabb145`.

#### Initialize MapsControl[​](https://docs.mapsindoors.com/getting-started/android/v3/map#initialize-mapscontrol) <a href="#initialize-mapscontrol" id="initialize-mapscontrol"></a>

We now want to add all the data we get by initializing `MapsIndoors` to our Google map. This is done by initializing `MapControl` onto the Google map. `MapControl` is used as a layer between Google Maps and MapsIndoors.

Here we use Google Maps logic to apply geodata onto the map. This also means we append logic onto many Google Maps listeners, which means that using Google Maps listeners directly might break intended behavior of the MapsIndoors experience. We recommend to check our reference docs, and see if you can add a specific `Listener` through the `MapControl` and always use those when possible.

Start by creating an `initMapControl` method which is used to initiate the `MapControl` and assign it to our Google map:

* Java
* Kotlin

[MapsActivity.java](https://github.com/MapsPeople/MapsIndoors-Getting-Started-Android/blob/master/app/src/main/java/com/example/mapsindoorsgettingstarted/MapsActivity.java#L135-L168)

```
void initMapControl(View view) {
    //Creates a new instance of MapControl
    mMapControl = new MapControl(this);
    //Sets the Google map object and the map view to the MapControl
    mMapControl.setGoogleMap(mMap, view);
    //Initiates the MapControl
    mMapControl.init(miError -> {
        if (miError == null) {
            //No errors so getting the first venue (in the white house solution the only one)
            Venue venue = MapsIndoors.getVenues().getCurrentVenue();
            runOnUiThread( ()-> {
                if (venue != null) {
                    //Animates the camera to fit the new venue
                    mMap.animateCamera(CameraUpdateFactory.newLatLngBounds(venue.getLatLngBoundingBox(), 19));
                }
            });
        }
    });
}
```

In your `onMapReady` callback function, assign the `mMap` variable with the `GoogleMap` you get from the callback and call the `initMapControl` method with the `mMapView` you assigned in the `onCreate` to set up a Google map with MapsIndoors Venues, Buildings and Locations:

* Java
* Kotlin

[MapsActivity.java](https://github.com/MapsIndoors/MapsIndoors-Getting-Started-Android/blob/master/app/src/main/java/com/example/mapsindoorsgettingstarted/MapsActivity.java#L135-L168)

```
@Override
public void onMapReady(GoogleMap googleMap) {
    mMap = googleMap;

    if (mMapView != null) {
        initMapControl(mMapView);
    }
}
```

Expected result:

![An animation showing the desired behaviour of this tutorial](https://docs.mapsindoors.com/img/getting-started/android\_map\_gif.gif)

See the full example of MapsActivity here [MapsActivity.java](https://github.com/MapsPeople/MapsIndoors-Getting-Started-Android/blob/master/app/src/main/java/com/example/mapsindoorsgettingstarted/MapsActivity.java) or [MapsActivity.kt](https://github.com/MapsPeople/MapsIndoors-Getting-Started-Android-Kotlin/blob/main/app/src/main/java/com/example/mapsindoorsgettingstartedkotlin/MapsActivity.kt)
