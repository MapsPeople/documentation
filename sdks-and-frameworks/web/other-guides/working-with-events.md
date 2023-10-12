# Working with Events

### Overview[​](https://docs.mapsindoors.com/working-with-events#overview) <a href="#overview" id="overview"></a>

Let's take a look at the events that MapsIndoors offers and how to utilize them.

> Events are actions or occurrences that happen in the system you are programming, which the system tells you about so you can respond to them in some way if desired. -- [_MDN web docs_](https://developer.mozilla.org/en-US/docs/Learn/JavaScript/Building\_blocks/Events)

For example, if the user clicks on a Location on the map, then you can react to that action by presenting the user with additional info about the Location.

A code example is shown in the JSFiddle below, but will be run through bit by bit in this guide. <mark style="background-color:red;">(Kan ikke se JSFiddle)</mark>



<mark style="background-color:red;">Der nævnes fire typer events. Ville det ikke være godt med et eksempel/en use case til hver om hvordan man ville kunne bruge pågældende event.</mark>&#x20;

#### Ready Event[​](https://docs.mapsindoors.com/working-with-events#ready-event) <a href="#ready-event" id="ready-event"></a>

The `ready` event will be fired when MapsIndoors is done initializing and is ready to interact.

```
mapsIndoors.addListener('ready', (e) => {
 log(`MapsIndoors: Ready`);
});
```

#### Building Changed Event[​](https://docs.mapsindoors.com/working-with-events#building-changed-event-1) <a href="#building-changed-event-1" id="building-changed-event-1"></a>

The `building_changed` event will be fired when the map is moved around and a new Building comes in focus.

This is also related to the Floor Selector which will update its view to show the Floors of the current Building.

The event handler is called with a [building](https://app.mapsindoors.com/mapsindoors/js/sdk/latest/docs/global.html#Building) (<mark style="background-color:red;">jeg forstår ikke det her link</mark>) object representing the building in focus.

```
mapsIndoors.addListener('building_changed', (e) => {
 log(`Building changed: ${e.buildingInfo.name}`);
});
```

#### Floor Changed Event[​](https://docs.mapsindoors.com/working-with-events#floor-changed-event-1) <a href="#floor-changed-event-1" id="floor-changed-event-1"></a>

The `floor_changed` event will be fired when the Floor is changed; either by clicking the Floor Selector or by calling `setFloor()` on the MapsIndoors instance.

The event handler is called with the Floor Index of the current Floor.

```
mapsIndoors.addListener('floor_changed', (e) => {
 log(`Floor changed: ${e}`);
});
```

#### Click Event[​](https://docs.mapsindoors.com/working-with-events#click-event) <a href="#click-event" id="click-event"></a>

The `click` event will fire when the user clicks on a Location on the map.

The event handler is called with a [location](https://app.mapsindoors.com/mapsindoors/js/sdk/latest/docs/global.html#Location) object representing the Location clicked.

```
mapsIndoors.addListener('click', (location) => {
 log(`Clicked: ${location.properties.name}`);
});
```
