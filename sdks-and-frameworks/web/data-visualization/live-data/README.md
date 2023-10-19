# Live Data

<mark style="background-color:red;">WRONG COPIED ARTICLE</mark>

This guide gives an overview of how to work with Live Data in the MapsIndoors Android SDK. As opposed to static data which does not change unless data is synchronized, Live Data can change in real time, and these changes can be instantly reflected on the map and in searches.

Common use cases are:

* Changing the appearance of meeting rooms or workspace desks on a map, or in a list, based on occupancy information. For example, change the icon in order to indicate that a room is occupied.
* Changing the position of a POI representing a vehicle.

Support for Live Data requires that server-side integrations are in place. For example, visualizing live occupancy data requires that a calendar or booking system integration is in place. An integration like that is set up in [collaboration with MapsPeople](https://www.mapspeople.com/mapsindoors-integrations/).

### Live Topics[​](https://docs.mapsindoors.com/live-data-intro#live-topics) <a href="#live-topics" id="live-topics"></a>

All Live Data is ordered in so-called _Topics_. A MapsIndoors Topic is _hierarchical_ in the way it is defined, and its relation to MapsIndoors data is derivable by its 7 components:

1. Dataset
2. Venue
3. Building
4. Floor
5. Room
6. Location
7. Domain Type

As a minimum, all Topics relate to a Data Set (also known as a "Solution" in MapsIndoors), a Domain Type and one (or more) of the other components.

#### Domain Type[​](https://docs.mapsindoors.com/live-data-intro#domain-type) <a href="#domain-type" id="domain-type"></a>

The Domain Type describes what kind of conceptual Domain the Live Data belongs to. Here are some examples of Domain Types:

* Availability - The current availability state of a particular bookable room or workspace
* Occupancy - The current known occupancy of a given capacity, for example in a meeting room
* Position - The current geo-spatial position and related floor

The Domain Type is not bound to be one of the above, but could be very specific to a particular use-case, source of data and technical integration.

#### Topic Criterias[​](https://docs.mapsindoors.com/live-data-intro#topic-criterias) <a href="#topic-criterias" id="topic-criterias"></a>

Knowing that updates are ordered in Topics, it is possible to subscribe to updates using a Topic Criteria. Filtering out live updates can be done on all levels of the Topic Criteria. For example, you might want to subscribe to all position updates but only for a particular Floor in a particular Building. This can be done by setting the correct IDs on the Floor and Building component. Leaving out a component means that we will get all updates, regardless of what relation the updates have at that level. Continuing the example, leaving out the Floor component means that we get all position updates on all Floors, but still only for a particular Building.

### Live Updates[​](https://docs.mapsindoors.com/live-data-intro#live-updates) <a href="#live-updates" id="live-updates"></a>

A live update is the model for a message carrying one piece of Live Data, for example that a particular room is now occupied. It contains the Topic for the live update and the actual live properties as a _dictionary_ of _strings_.



### Enable Live Data in Your App with the LiveDataManager for Web[​](https://docs.mapsindoors.com/live-data-intro#enable-live-data-in-your-app-with-the-livedatamanager-for-web) <a href="#enable-live-data-in-your-app-with-the-livedatamanager-for-web" id="enable-live-data-in-your-app-with-the-livedatamanager-for-web"></a>

#### With the `enableLiveData()` and `disableLiveData()` Methods[​](https://docs.mapsindoors.com/live-data-intro#with-the-enablelivedata-and-disablelivedata-methods) <a href="#with-the-enablelivedata-and-disablelivedata-methods" id="with-the-enablelivedata-and-disablelivedata-methods"></a>

Enabling Live Data through the `LiveDataManager` is an easy way to get Live Data running in your web app.

```javascript
const liveDataManagerInstance = new mapsindoors.LiveDataManager(mapsIndoorsInstance);
liveDataManagerInstance.enableLiveData(mapsindoors.LiveDataManager.LiveDataDomainTypes.AVAILABILITY);
liveDataManagerInstance.enableLiveData(mapsindoors.LiveDataManager.LiveDataDomainTypes.OCCUPANCY);
```

In the example we enable Live Data for the "Availability" and "Occupancy" Domain types. Internal processes will determine which Topics are relevant for subscription based on where the map is situated. A default rendering mechanism will also alter the appearance of the relevant Locations on the map.

You can disable the Live Data again by calling the `disableLiveData` method:

```javascript
liveDataManagerInstance.disableLiveData(mapsindoors.LiveDataManager.LiveDataDomainTypes.AVAILABILITY);
```

Note that using the `enableLiveData` method has some limitations, and is thereby not suitable for all use cases.

* Since the LiveDataManager will try to determine the Live Data subscriptions based on where the map is currently situated, it might not detect Live Data updates of the "Position" Domain representing moving objects entering the visible region of the map.
* Since the LiveDataManager does not know which Live Updates are relevant to show, it will need to subscribe to all Live Data in the visible region, which, depending on your amount of Live Data, may or may not lead to performance implications.

#### With the `subscribe()` and `unsubscribe()` Methods[​](https://docs.mapsindoors.com/live-data-intro#with-the-subscribe-and-unsubscribe-methods) <a href="#with-the-subscribe-and-unsubscribe-methods" id="with-the-subscribe-and-unsubscribe-methods"></a>

To enable Live Data in an web application, a subscription to one or more Topics is needed. Once subscribed, the web application can be notified about changes and decide what to do. The web application is in control of what should happen upon receiving live data updates, and the MapsIndoors SDKs provide mechanisms to efficiently make updates to the map representation of Locations. The central class to carry out these tasks is the `LiveDataManager`.

The only Live Data updates that are also directly notified to the SDK internally are Live Data updates of the "Position" Domain Type. By consequence, if you have already set up your map with MapsIndoors, an additional few lines of code can enable moving locations on the map. Here is an example:

```javascript
const liveDataManagerInstance = new mapsindoors.LiveDataManager(mapsIndoorsInstance);
liveDataManagerInstance.subcribe(mySolutionId + '/#');
```

In the example, the Topic is created using the Solution ID and a multilevel wildcard, which will return all Live Data in the Solution.

You can unsubscribe to the Topic by calling the `unsubscribe` method:

```javascript
liveDataManagerInstance.unsubcribe(positionTopic);
```

#### Rendering Live Data Locations for Web[​](https://docs.mapsindoors.com/live-data-intro#rendering-live-data-locations-for-web) <a href="#rendering-live-data-locations-for-web" id="rendering-live-data-locations-for-web"></a>

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
