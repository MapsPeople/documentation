# Enabling Live Data

As opposed to _static data_, which does not change unless data is synchronized, Live Data can change in real time, and these changes can be instantly reflected on the map and in searches.

Common use-cases are:

* Changing the appearance of meeting rooms or workspace desks on a map, or in a list, based on occupancy information. For example, change the icon in order to indicate that a room is occupied.
* Changing the position of a POI representing a vehicle.

Support for Live Data requires that server-side integrations are in place. For example, visualizing live occupancy data requires that a calendar or booking system integration is in place. An integration like that is set up in [collaboration with MapsPeople](https://www.mapspeople.com/mapsindoors-integrations/).

The following section relies on the existence of Live Position Data. If you do not have access to a MapsIndoors Dataset that has a Live Data integration, you should use our demo API key: `d876ff0e60bb430b8fabb145` / White house demo.

Enabling Live Data through [`MapControl`](https://app.mapsindoors.com/mapsindoors/reference/react-native/google-maps/1.0.0/classes/MapControl.html) is as simple as calling [`mapControl.enableLiveData()`](https://app.mapsindoors.com/mapsindoors/reference/react-native/google-maps/1.0.0/classes/MapControl.html#enableLiveData).

We will create a new functions inside our `MapScreen` component called `livedata()` to enable Live Data for the Solution.

```
  const livedata = async () => {
    if (!mapControl) {
      console.warn("Must load MapsIndoors before enabling live data");
      return
    }

    await mapControl.enableLiveData('position');
    await mapControl.enableLiveData('availability');
    await mapControl.enableLiveData('occupancy');
  }
```

We can later call this function after loading MapsIndoors to enable it. For demonstration purposes we add a button inside the `MapScreen` that you can click to enable live data.

```
<GestureHandlerRootView style={{flex:1, flexGrow:1}}>
    <MapView style={{
      width: width,
      height: height,
      flex: 1,
    }}/>
    <View style={{position: 'absolute', bottom: 50, start: 10, flexDirection: 'column-reverse'}}>
      <Button title="livedata" onPress={livedata} />
    </View>
    ...
</GestureHandlerRootView>
```

By consequence, enabling live data through [MapControl](https://app.mapsindoors.com/mapsindoors/reference/react-native/google-maps/1.0.0/classes/MapControl.html) is as simple as calling [mapControl.enableLiveData()](https://app.mapsindoors.com/mapsindoors/reference/react-native/google-maps/1.0.0/classes/MapControl.html) will manage the Live Data subscriptions needed for the currently visible map and provide a default rendering of the Live Data updates depending on the Domain Type.

Using the demo API key you should now be able to see a "Staff Person" moving from one end to the other at ground floor in The White House main building.\


Expected result:

![An animation showing the desired behaviour of this tutorial](https://docs.mapsindoors.com/img/getting-started/react\_native\_live\_data.gif)

### Summary[​](https://docs.mapsindoors.com/getting-started/React%20Native/livedata#summary) <a href="#summary" id="summary"></a>

Congratulations! You're at the end of your journey (for now), and you've accomplished a lot! 🎉

* You learned which prerequisites is needed to start building with MapsIndoors.
* You loaded a interactive map with MapsIndoors locations and added a floor selector for navigating between floors.
* You created a search experience to search for specific locations on the map.
* You added functionality for getting directions from one Location to another.
* You learned how to enable different types of Live Data Domains in your app.

This concludes the "Getting Started" tutorial, but there's always more to discover. To get more inspiration on what to build next please visit our [showcase page](https://www.mapspeople.com/showcases) to see how other clients use MapsIndoors! For more documentation, please visit the rest of our Docs site!.
