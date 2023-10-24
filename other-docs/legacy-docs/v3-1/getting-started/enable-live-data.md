# Live Data

### Enable Live Data[​](https://docs.mapsindoors.com/getting-started/ios/live-data#enable-live-data) <a href="#enable-live-data" id="enable-live-data"></a>

As opposed to _static data_, which does not change unless data is synchronized, Live Data can change in real time, and these changes can be instantly reflected on the map and in searches.

Common use-cases are:

* Changing the appearance of meeting rooms or workspace desks on a map, or in a list, based on occupancy information. For example, change the icon in order to indicate that a room is occupied.
* Changing the position of a point of interest representing a vehicle.

Support for Live Data will require specific server-side integrations. For instance, visualizing live occupancy data requires a calendar or booking system integration is in place. An integration like that is set up in [collaboration with MapsPeople](https://www.mapspeople.com/mapsindoors-integrations/).

The following section rely on the existence of Live Position Data. If you do not have access to a MapsIndoors Dataset that have a Live Data integration, we suggest that you use can our demo API key: `d876ff0e60bb430b8fabb145`.

For this demonstration we wish to show an employee moving across The White House.

Enabling Live Data through `MapControl` is achieved by simply calling `mapControl.enableLiveData()` with a Domain Type, somewhere after the initialization of `MapControl`. In this case, since we wish to show the positional changes to an employee live, we will use the domain type, position.

```
mapControl?.enableLiveData(MPLiveDomainType.position)
```

Assuming the demo key is utilising you should now see a "Staff Person" moving from one end to the other at the ground floor of The White House main building.

### Expected Result[​](https://docs.mapsindoors.com/getting-started/ios/live-data#expected-result) <a href="#expected-result" id="expected-result"></a>

![An animation showing the desired behaviour of this tutorial](https://docs.mapsindoors.com/img/getting-started/ios\_live-data.gif)

Learn more about about controlling and rendering Live Data in MapsIndoors in the [introducion to Live Data](https://docs.mapsindoors.com/live-data-intro/)

### Summary[​](https://docs.mapsindoors.com/getting-started/ios/live-data#summary) <a href="#summary" id="summary"></a>

Congratulations! You're at the end of your journey (for now), and you've accomplished a lot! 🎉

* You learned which prerequisites is needed to start building with MapsIndoors.
* You loaded a interactive map with MapsIndoors locations and added a floor selector for navigating between floors.
* You created a search experience to search for specific locations on the map.
* You added functionality for getting directions from one Location to another.
* You learned how to enable different types of Live Data Domains in your app.

This concludes the "Getting Started" tutorial, but there's always more to discover. To get more inspiration on what to build next please visit our [showcase page](https://www.mapspeople.com/showcases) to see how other clients use MapsIndoors! For more documentation, please visit the rest of our Docs site!.
