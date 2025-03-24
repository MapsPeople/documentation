---
hidden: true
---

# Live Data

This guide gives an overview of how to work with _Live Data_ in the MapsIndoors Android SDK. As opposed to _static data_, which does not change unless data is synchronized, Live Data can change in real time, and these changes can be instantly reflected on the map and in searches.

Common use-cases are:

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



### Enable Live Data in Your App in iOS[​](https://docs.mapsindoors.com/live-data-intro#enable-live-data-in-your-app-in-ios) <a href="#enable-live-data-in-your-app-in-ios" id="enable-live-data-in-your-app-in-ios"></a>

To enable Live Data in an application, a subscription to one or more Topics is needed. Once subscribed, the application can be notified about changes and decide what to do. The application is in control of what should happen upon receiving live data updates, and the MapsIndoors SDKs provide mechanisms to efficiently make updates to the map representation of locations.

The central class to carry out the subscription tasks is the LiveDataManager, but the MPMapControl protocol also has some convenience methods that abstract the subscription layer, trading off some granular control.

#### Enable Live Data through MPMapControl in iOS (v4 SDK)[​](https://docs.mapsindoors.com/live-data-intro#enable-live-data-through-mpmapcontrol-in-ios-v4-sdk) <a href="#enable-live-data-through-mpmapcontrol-in-ios-v4-sdk" id="enable-live-data-through-mpmapcontrol-in-ios-v4-sdk"></a>

Enabling Live Data through MPMapControl can be done as simple as calling MPMapControl.enableLiveData() with a Domain Type.

```swift
self.mapControl = MPMapControl(map: self.map!)

self.mapControl.enableLiveData(domain: MPLiveDomainType.position) { liveUpdate in
    // Handle live data updates
}
```

In the example, we are enabling Live Data for the Domain Type "Position". Internal processes will determine which topics are relevant for subscription based on where the map is situated. A default rendering mechanism will also alter the appearance of the relevant locations on the map. As a consequence, the SDK will set custom display rules for this rendering. Adding your own or resetting display rules while Live Data is enabled with default rendering may break the rendering for the current MPMapControl instance. Hence, you should not use custom display rules unless you are handling the rendering of Live Data on your own.

Note that using the enableLiveData() methods on MPMapControl has some limitations and is therefore not suitable for all use cases.

* Since MPMapControl will try to determine relevant Live Data subscriptions based on where the map is currently situated, it might not detect Live Data updates of the Position domain representing moving objects entering the visible region of the map.
* Since MPMapControl does not know which Live Updates are relevant to show, it will need to subscribe to all Live Data in the visible region, which, depending on your amount of Live Data, may or may not impact app performance.

### Rendering Live Data Locations iOS[​](https://docs.mapsindoors.com/live-data-intro#rendering-live-data-locations-ios) <a href="#rendering-live-data-locations-ios" id="rendering-live-data-locations-ios"></a>

As mentioned, MPMapControl has a default way of rendering Live Data Locations if you call MPMapControl.enableLiveData(). If you need to show Live Data in another way, you can add handlers for this by providing a listener to the enableLiveData method. Here is an example of creating a Display Rule for a Location that has a Live Update.

```swift
self.mapControl.enableLiveData(domain: MPLiveDomainType.occupancy) { liveUpdate in
    if let occupancyUpdate = liveUpdate as? MPOccupancyLiveUpdate {
        // Handle occupancy live data update
        let locationId = occupancyUpdate.topic.locationId
        if let location = MapsIndoors.getLocation(withId: locationId) {
            var img: UIImage

            if occupancyUpdate.numberOfPeople > 0 {
                img = UIImage(named: "closed.png")
            } else {
                img = UIImage(named: "open.png")
            }

            let dr = MPLocationDisplayRule(name: MPLiveDomainType.occupancy, icon: img, andZoomLevelOn: 15)
            self.mapControl?.setDisplayRule(dr, for: location)
        }
    }
}
```

### Handling Live Data Events in iOS[​](https://docs.mapsindoors.com/live-data-intro#handling-live-data-events-in-ios) <a href="#handling-live-data-events-in-ios" id="handling-live-data-events-in-ios"></a>

While only a few lines of code can get things moving around on a map, there are of course more handles that are relevant to create a robust and user-friendly real-time map experience.

#### Listening for Live Updates in iOS[​](https://docs.mapsindoors.com/live-data-intro#listening-for-live-updates-in-ios) <a href="#listening-for-live-updates-in-ios" id="listening-for-live-updates-in-ios"></a>

To get Live Updates on a general level, implement the MPSubscriptionClientDelegate protocol method didReceiveMessage(\_ message: Data, onTopic: String):

```swift
extension MyClass : MPSubscriptionClientDelegate {
    func didReceiveMessage(_ message: Data, onTopic: String) {
        let liveUpdate = location.getLiveUpdate(forDomainType: MPLiveDomainType.occupancy)
        ...
    }
}
```

#### Handling State Changes and Errors in iOS[​](https://docs.mapsindoors.com/live-data-intro#handling-state-changes-and-errors-in-ios) <a href="#handling-state-changes-and-errors-in-ios" id="handling-state-changes-and-errors-in-ios"></a>

In order to get notified about state changes and errors happening in the Subscription Client, MPSubscriptionClientDelegate provides methods as shown in the example below:

```swift
extension MyClass : MPSubscriptionClientDelegate {

    func didUpdateState(_ state: MPSubscriptionState) {
        print("Client state changed to \(state)")
    }

    func didSubscribe(_ topic: MPSubscriptionTopic) {
        print("Started Live Updates for \(topic.topicString)")
    }

    func didUnsubscribe(_ topic: MPSubscriptionTopic) {
        print("Stopped Live Updates for \(topic.topicString)")
    }

    func onSubscriptionError(_ error: Error, topic: MPSubscriptionTopic) {
        print("Could not subscribe Live Updates for \(topic.topicString)")
    }

    func onUnsubscriptionError(_ error: Error, topic: MPSubscriptionTopic) {
        print("Could not unsubscribe Live Updates for \(topic.topicString)")
    }

    func onError(_ error: Error) {
        print("We got an error")
    }
}
```

Live Updates are of course dependent on network connectivity, so the Subscription Client will try to recover from common errors like network dropout. However, the Subscription Client will not try to recover from subscription errors alone, as this could be caused by a non-existing topic for a given Dataset, thus it does not make sense retrying the failing subscription.

To learn more, visit the [Live Data tutorial for iOS](https://docs.mapsindoors.com/live-data-intro/) and the [reference guide](https://app.mapsindoors.com/mapsindoors/reference/ios/v4-doc/documentation/mapsindoors/mpliveupdate).
