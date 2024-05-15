# Live Data

## Live Updates[​](https://docs.mapsindoors.com/live-data-intro#live-updates) <a href="#live-updates" id="live-updates"></a>

A live update represents a message containing one piece of Live Data, such as indicating that a specific room is currently occupied. Each live update includes a _Topic_ and the live properties, organised as a _dictionary of strings_.

**Common use-cases are:**

* Changing the appearance of meeting rooms or workspace desks on a map, or in a list, based on occupancy information. For example, change the icon in order to indicate that a room is occupied.
* Changing the position of a POI representing a vehicle.

Support for Live Data requires that server-side integrations are in place. For example, visualising live occupancy data requires that a calendar or booking system integration is in place. An integration like that is set up in [collaboration with MapsPeople](https://www.mapspeople.com/mapsindoors-integrations/).

### Topic explanation

A Topic represents a specific category or subject area within the Live Data system. It serves as a channel through which Live Updates, carrying information related to that particular category, are communicated. Think of a Topic as a designated space where Live Data pertaining to a certain aspect, such as availability or occupancy, is organised and transmitted. Developers can subscribe to Topics relevant to their application's needs, enabling them to receive real-time updates and respond accordingly.

In our system, we support seven different types of Topics: `occupancy`, `availability`, `position`, `temperature`, `CO2`, `humidity`, and `count`.

When creating a Topic, you have the flexibility to specify the granularity of the data you wish to access. You can choose to listen to changes at the solution level or drill down to individual locations. Utilising symbols like the `+` sign allows for broad selections, while the `#` sign acts as a 'wildcard,' enabling retrieval of all Live Data within the Solution. Topics are constructed using the following pattern:

`[SOLUTION_ID]/[VENUE_ID]/[BUILDING_ID]/[FLOOR_ID]/[ROOM_ID]/[LOCATION_ID]/[TOPIC_TYPE]`

* **Example #1:** Topic for occupancy updates across specific levels within a Solution > Venue > Building > Floor > Room \
  `[SOLUTION_ID]/[VENUE_ID]/[BUILDING_ID]/[FLOOR_ID]/[ROOM_ID]/+/occupancy`
* **Example #2:** Topic for all Live Updates available within a specific Solution > Venue > Building `[SOLUTION_ID]/[VENUE_ID]/[BUILDING_ID]/+/+/+/#`

### Utilise Live Data in Your App for Web[​](https://docs.mapsindoors.com/live-data-intro#enable-live-data-in-your-app-with-the-livedatamanager-for-web) <a href="#enable-live-data-in-your-app-with-the-livedatamanager-for-web" id="enable-live-data-in-your-app-with-the-livedatamanager-for-web"></a>

#### 1. With the `enableLiveData()` and `disableLiveData()` Methods[​](https://docs.mapsindoors.com/live-data-intro#with-the-enablelivedata-and-disablelivedata-methods) <a href="#with-the-enablelivedata-and-disablelivedata-methods" id="with-the-enablelivedata-and-disablelivedata-methods"></a>

By utilising the `enableLiveData()` and `disableLiveData()` methods, you can easily integrate Live Data functionality into your web application using the LiveDataManager.

```javascript
const liveDataManagerInstance = new mapsindoors.LiveDataManager(mapsIndoorsInstance);
liveDataManagerInstance.enableLiveData(mapsindoors.LiveDataManager.LiveDataDomainTypes.AVAILABILITY);
liveDataManagerInstance.enableLiveData(mapsindoors.LiveDataManager.LiveDataDomainTypes.OCCUPANCY);
```

In this example, we enable Live Data for "Availability" and "Occupancy" Domain types. The LiveDataManager internally manages relevant Topic subscriptions based on the map's location. Additionally, a default rendering mechanism adjusts the appearance of relevant Locations on the map accordingly.

You can disable the Live Data again by calling the `disableLiveData()` method:

```javascript
liveDataManagerInstance.disableLiveData(mapsindoors.LiveDataManager.LiveDataDomainTypes.AVAILABILITY);
```

Note that using the `enableLiveData()` method has some limitations, and is thereby not suitable for all use cases.

* Since the LiveDataManager will try to determine the Live Data subscriptions based on where the map is currently situated, it might not detect Live Data updates of the "Position" Domain representing moving objects entering the visible region of the map.
* Since the LiveDataManager does not know which Live Updates are relevant to show, it will need to subscribe to all Live Data in the visible region, which, depending on your amount of Live Data, may or may not lead to performance implications.

#### 2. With the `subscribe()` and `unsubscribe()` Methods[​](https://docs.mapsindoors.com/live-data-intro#with-the-subscribe-and-unsubscribe-methods) <a href="#with-the-subscribe-and-unsubscribe-methods" id="with-the-subscribe-and-unsubscribe-methods"></a>

To enable Live Data in an web application, a subscription to one or more Topics is needed. Once subscribed, the web application can be notified about changes and decide what to do. The web application is in control of what should happen upon receiving live data updates, and the MapsIndoors SDKs provide mechanisms to efficiently make updates to the map representation of Locations. The central class to carry out these tasks is the `LiveDataManager`.

The only Live Data updates that are also directly notified to the SDK internally are Live Data updates of the "Position" Domain Type. By consequence, if you have already set up your map with MapsIndoors, an additional few lines of code can enable moving locations on the map. Here is an example:

```javascript
const liveDataManagerInstance = new mapsindoors.LiveDataManager(mapsIndoorsInstance);
liveDataManagerInstance.subcribe(mySolutionId + '/#');
```

In the example, the Topic is created using the Solution ID and a multilevel wildcard, which will return all Live Data in the Solution.

You can unsubscribe to the Topic by calling the `unsubscribe()` method:

```javascript
liveDataManagerInstance.unsubcribe(positionTopic);
```

### Rendering Live Data Locations for Web[​](https://docs.mapsindoors.com/live-data-intro#rendering-live-data-locations-for-web) <a href="#rendering-live-data-locations-for-web" id="rendering-live-data-locations-for-web"></a>

As mentioned, the LiveDataManager has a default way of rendering Live Data Locations if you use `enableLiveData()`. If you need to show Live Data in another way, you can override the default rendering by providing a function as second parameter, which will act as a callback when receiving Live Updates bundled in a LiveUpdateEvent:

```javascript
liveDataManagerInstance.enableLiveData(mapsindoors.LiveDataManager.LiveDataDomainTypes.AVAILABILITY, (liveUpdateEvent) => {
    for (const [liveUpdateDomainType, locations] of liveUpdateEvent.data) {
        for (const location of locations) {
            // Manipulate the Location and/or Display Rule here as you see fit. Live Updates are contained within the LiveUpdates Map on the Location.
            // As an example, we change the icon of the Location if there is an Occupancy update:

            const occupancyUpdate = location.liveUpdates.get(mapsindoors.LiveDataManager.LiveDataDomainTypes.OCCUPANCY);
            if (occupancyUpdate) {
                const displayRule = mi.getDisplayRule(location, true);
                const displayRuleOverrides = { iconSize: { width: 100, height: 100 } };
                mapsIndoorsInstance.setDisplayRule(location.id, displayRuleOverrides);
            }
        }
    }
});
```

To avoid performance implications, the display rule updates may benefit from being throttled.

Note that since there is no guarantee of which Live Data you receive first, and Locations can have multiple Live Data updates on different Domains, we recommend checking the `lastModifiedTimeStamp` of each Live Data update to select which one to render.

### Handling Live Data Events for Web[​](https://docs.mapsindoors.com/live-data-intro#handling-live-data-events-for-web) <a href="#handling-live-data-events-for-web" id="handling-live-data-events-for-web"></a>

While only a few lines of code can get things moving around on a map, there are of course more handles that are relevant to create a robust and user-friendly real-time map experience.

#### Listening for Live Updates for Web[​](https://docs.mapsindoors.com/live-data-intro#listening-for-live-updates-for-web) <a href="#listening-for-live-updates-for-web" id="listening-for-live-updates-for-web"></a>

To listen for Live Updates on a general level, add an event listener for `live_update_received` on the Live Data Manager. The event payload is a [Live Update](https://app.mapsindoors.com/mapsindoors/js/sdk/latest/docs/LiveUpdate.html)

```javascript
liveDataManagerInstance.addEventListener('live_update_received', (liveUpdate) => {
    if (liveUpdate.domainType === mapsindoors.LiveDataManager.LiveDataDomainTypes.OCCUPANCY) {
        const people = liveUpdate.properties.nrOfPeople;
        ...
    }
});
```

#### Handling State Changes and Errors for Web[​](https://docs.mapsindoors.com/live-data-intro#handling-state-changes-and-errors-for-web) <a href="#handling-state-changes-and-errors-for-web" id="handling-state-changes-and-errors-for-web"></a>

In order to get notified about state changes and errors related to Live Data, a number of listeners can be set on the LiveDataManager:

* `live_update_received` event. The event payload is a [Live Update](https://app.mapsindoors.com/mapsindoors/js/sdk/latest/docs/LiveUpdate.html).
* `live_data_status_changed` event. The event payload is a [Subscription Client State](https://app.mapsindoors.com/mapsindoors/js/sdk/latest/docs/mapsindoors.LiveDataManager.html#.SubscriptionClientStates).
* `live_data_error` event. The event payload contains information about the error.

Live Updates are dependent on network connectivity, so the Live Data Manager will try to recover from common errors like e.g. a network dropout. The Live Data Manager will not try to recover from subscription errors alone, as this could be caused by a non-existing Topic for a given Dataset, thus it does not make sense retrying the failing subscription.
