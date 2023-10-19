# Enable Live Data

As opposed to _static data_, which does not change unless data is synchronized, Live Data can change in real time, and these changes can be instantly reflected on the map and in searches.

Common use-cases are:

* Changing the appearance of meeting rooms or workspace desks on a map, or in a list, based on occupancy information. For example, change the icon in order to indicate that a room is occupied.
* Changing the position of a POI representing a vehicle.

Support for Live Data requires that server-side integrations are in place. For example, visualizing live occupancy data requires that a calendar or booking system integration is in place. An integration like that is set up in [collaboration with MapsPeople](https://www.mapspeople.com/mapsindoors-integrations/).

The following section relies on the existence of Live Position Data. If you do not have access to a MapsIndoors Dataset that have a Live Data integration, you should use our demo API key: `d876ff0e60bb430b8fabb145`.

Enabling Live Data through [`MapsIndoorsWidget`](https://pub.dev/documentation/mapsindoors\_googlemaps/latest/mapsindoors/MapsIndoorsWidget-class.html) is as simple as [calling `mapsindoorsWidget.enableLiveData`](https://pub.dev/documentation/mapsindoors\_googlemaps/latest/mapsindoors/MapsIndoorsWidget/enableLiveData.html) with a [Domain Type](https://pub.dev/documentation/mapsindoors\_googlemaps/latest/mapsindoors/LiveDataDomainTypes.html).

We will create a new method on our `MapState` called `enableLiveData` to enable Live Data for the Solution.

```dart
void enableLiveData() {
  _mapControl
    ..enableLiveData(LiveDataDomainTypes.availability.name)
    ..enableLiveData(LiveDataDomainTypes.occupancy.name)
    ..enableLiveData(LiveDataDomainTypes.position.name);
}
```

By consequence, `MapsIndoorsWidget` will manage the Live Data subscriptions needed for the currently visible map and provide a default rendering of the Live Data updates depending on the Domain Type.

In the context of your view controller showing a map, add the call after your `MapsIndoorsWidget` object is ready. Use the `onMapControlReady` method created earlier in `MapState`.\


{% hint style="info" %}
Due to a known issue with the latest iOS SDK, you will need to change floor before the livedata is shown
{% endhint %}

```dart
void onMapControlReady(MPError? error) async {
  if (error == null) {
    // with mapcontrol loaded, we can enable livedata
    enableLiveData();
     ...
  } else {
    ...
  }
}
```

Using the demo API key you should now be able to see a "Staff Person" moving from one end to the other at ground floor in The White House main building.

Expected result:

![An animation showing the desired behaviour of this tutorial](https://docs.mapsindoors.com/img/getting-started/flutter\_livedata.gif)

Learn more about controlling and rendering Live Data in MapsIndoors in the [introduction to Live Data](https://docs.mapsindoors.com/live-data-intro/).

### Summary[​](https://docs.mapsindoors.com/getting-started/flutter/livedata#summary) <a href="#summary" id="summary"></a>

Congratulations! You're at the end of your journey (for now), and you've accomplished a lot! 🎉

* You learned which prerequisites is needed to start building with MapsIndoors.
* You loaded a interactive map with MapsIndoors locations and added a floor selector for navigating between floors.
* You created a search experience to search for specific locations on the map.
* You added functionality for getting directions from one Location to another.
* You learned how to enable different types of Live Data Domains in your app.

This concludes the "Getting Started" tutorial, but there's always more to discover. To get more inspiration on what to build next please visit our [showcase page](https://www.mapspeople.com/showcases) to see how other clients use MapsIndoors! For more documentation, please visit the rest of our Docs site!.

[\
](https://docs.mapsindoors.com/getting-started/flutter/directions)
