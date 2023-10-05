# Enable Live Data

As opposed to _static data_, which does not change unless data is synchronized, Live Data can change in real time, and these changes can be instantly reflected on the map and in searches.

Common use-cases are:

* Changing the appearance of meeting rooms or workspace desks on a map, or in a list, based on occupancy information. For example, change the icon in order to indicate that a room is occupied.
* Changing the position of a POI representing a vehicle.

Support for Live Data requires that server-side integrations are in place. For example, visualizing live occupancy data requires that a calendar or booking system integration is in place. An integration like that is set up in [collaboration with MapsPeople](https://www.mapspeople.com/mapsindoors-integrations/).

The following section relies on the existence of Live Position Data. If you do not have access to a MapsIndoors Dataset that have a Live Data integration, you should use our demo API key: `d876ff0e60bb430b8fabb145`.

Enabling Live Data through [`MapControl`](https://app.mapsindoors.com/mapsindoors/reference/android/v4/MapsIndoorsSDK/com.mapsindoors.core/-map-control/index.html?query=class%20MapControl) is as simple as [calling `mapControl.enableLiveData()`](https://app.mapsindoors.com/mapsindoors/reference/android/v4/MapsIndoorsSDK/com.mapsindoors.core/-map-control/enable-live-data.html?query=open%20fun%20enableLiveData\(domainType:%20String\)) with a [Domain Type](https://app.mapsindoors.com/mapsindoors/reference/android/v3/index.html).

We will create a new method on our `MapsActivity` called `enableLiveData()` to enable Live Data for the Solution.



{% tabs %}
{% tab title="Java" %}
[MapsActivity.java](https://github.com/MapsPeople/MapsIndoors-Android-Examples/blob/main/Google\_Maps/mapsindoorsgettingstartedjava/src/main/java/com/mapspeople/mapsindoorsgettingstartedjava/MapsActivity.java#L254-L262)

```
void enableLiveData() {
    //Enabling Live Data for the three known Live Data Domains enabled for this Solution.
    mMapControl.enableLiveData(LiveDataDomainTypes.AVAILABILITY_DOMAIN);
    mMapControl.enableLiveData(LiveDataDomainTypes.OCCUPANCY_DOMAIN);
    mMapControl.enableLiveData(LiveDataDomainTypes.POSITION_DOMAIN);
}
```

By consequence, [`MapControl`](https://app.mapsindoors.com/mapsindoors/reference/android/v4/MapsIndoorsSDK/com.mapsindoors.core/-map-control/index.html?query=class%20MapControl) will manage the Live Data subscriptions needed for the currently visible map and provide a default rendering of the Live Data updates depending on the Domain Type.

In the context of your view controller showing a map, add the call after creating your [`MapControl`](https://app.mapsindoors.com/mapsindoors/reference/android/v4/MapsIndoorsSDK/com.mapsindoors.core/-map-control/index.html?query=class%20MapControl) object used in the `Activity` in the `initMapControl()` method created earlier.



[MapsActivity.java](https://github.com/MapsPeople/MapsIndoors-Android-Examples/blob/main/Google\_Maps/mapsindoorsgettingstartedjava/src/main/java/com/mapspeople/mapsindoorsgettingstartedjava/MapsActivity.java#L145-L167)

```
void initMapControl(View view) {
    //Creates a new instance of MapControl
    MapControl.create(mapConfig, (mapControl, miError) -> {
        mMapControl = mapControl;
        //Enable Live Data on the map
        enableLiveData();
    }
    ...
}
```
{% endtab %}

{% tab title="Second Tab" %}
[MapsActivity.kt](https://github.com/MapsPeople/MapsIndoors-Android-Examples/blob/main/Google\_Maps/mapsindoorsgettingstartedkotlin/src/main/java/com/mapspeople/mapsindoorsgettingstartedkotlin/MapsActivity.kt#L223-L228)

```
private fun enableLiveData() {
    //Enabling Live Data for the three known Live Data Domains enabled for this Solution.
    mMapControl.enableLiveData(LiveDataDomainTypes.AVAILABILITY_DOMAIN)
    mMapControl.enableLiveData(LiveDataDomainTypes.OCCUPANCY_DOMAIN)
    mMapControl.enableLiveData(LiveDataDomainTypes.POSITION_DOMAIN)
}
```

By consequence, [`MapControl`](https://app.mapsindoors.com/mapsindoors/reference/android/v4/MapsIndoorsSDK/com.mapsindoors.core/-map-control/index.html?query=class%20MapControl) will manage the Live Data subscriptions needed for the currently visible map and provide a default rendering of the Live Data updates depending on the Domain Type.

In the context of your view controller showing a map, add the call after creating your [`MapControl`](https://app.mapsindoors.com/mapsindoors/reference/android/v4/MapsIndoorsSDK/com.mapsindoors.core/-map-control/index.html?query=class%20MapControl) object used in the `Activity` in the `initMapControl()` method created earlier.



[MapsActivity.kt](https://github.com/MapsPeople/MapsIndoors-Android-Examples/blob/main/Google\_Maps/mapsindoorsgettingstartedkotlin/src/main/java/com/mapspeople/mapsindoorsgettingstartedkotlin/MapsActivity.kt#L117-L135)

```
private fun initMapControl(view: View) {
    ...
    //Creates a new instance of MapControl
    MapControl.create(config) { mapControl, miError ->
        if (miError == null) {
            mMapControl = mapControl!!
            //Enable live data on the map
            enableLiveData()
            ...
        }
    }
    ...
}
```
{% endtab %}
{% endtabs %}

























Using the demo API key you should now be able to see a "Staff Person" moving from one end to the other at ground floor in The White House main building.

Expected result:

![An animation showing the desired behaviour of this tutorial](https://docs.mapsindoors.com/img/getting-started/android\_live\_data\_gif.gif)

Learn more about controlling and rendering Live Data in MapsIndoors in the [introduction to Live Data](https://docs.mapsindoors.com/live-data-intro/).

### Summary[​](https://docs.mapsindoors.com/getting-started/android/v4/livedata#summary) <a href="#summary" id="summary"></a>

Congratulations! You're at the end of your journey (for now), and you've accomplished a lot! 🎉

* You learned which prerequisites is needed to start building with MapsIndoors.
* You loaded a interactive map with MapsIndoors locations and added a floor selector for navigating between floors.
* You created a search experience to search for specific locations on the map.
* You added functionality for getting directions from one Location to another.
* You learned how to enable different types of Live Data Domains in your app.

This concludes the "Getting Started" tutorial, but there's always more to discover. To get more inspiration on what to build next please visit our [showcase page](https://www.mapspeople.com/showcases) to see how other clients use MapsIndoors! For more documentation, please visit the rest of our Docs site!.
