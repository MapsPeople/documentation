# Getting Directions

Now we have a simple map where you can search for locations. When finishing this step you'll be able to create a directions between two points and change the transportation mode.

### Get Directions Between Two Locations[​](https://docs.mapsindoors.com/getting-started/flutter/directions#get-directions-between-two-locations) <a href="#get-directions-between-two-locations" id="get-directions-between-two-locations"></a>

After having created our list of search results, we have a good starting point for creating directions between two Locations. Since our search only supports searching for a single location, we will hardcode a Location's coordinate into our app, and use that as the basis for our _Origin_. Then we'll create a route, show the navigation details, and display the route on the map.

We have already created a point in the basic example, called `_userPosition` to use as a starting point for directions on `MapState`. The position given with the example is for the main entrance to the White House, replace it with some location that fits your usecase.

```dart
final _userPosition = MPPoint.withCoordinates(
    longitude: -77.03740973527613,
    latitude: 38.897389429704695,
    floorIndex: 0);
```

We can extend the search functionality we made earlier by implementing the [`onLocationSelected`](https://pub.dev/documentation/mapsindoors\_googlemaps/latest/mapsindoors/OnLocationSelectedListener.html) listener on the [`MapsIndoorsWidget`](https://pub.dev/documentation/mapsindoors\_googlemaps/latest/mapsindoors/MapsIndoorsWidget-class.html), and showing the selected location's details on the bottom sheet:

```dart
void onMapControlReady(MPError? error) async {
  if (error == null) {
    ...
    // Add a listener for location selection events, we do not want to stop the SDK from moving the camera, so we do not comsume the event
    _mapControl
      ..setOnLocationSelectedListener(onLocationSelected, false)
      ..goTo(await getDefaultVenue());
  } else {
    ...
  }
}
```

and implementing the `onLocationSelected` listener:

```dart
void onLocationSelected(MPLocation? location) {
  // if no location is selected, close the sheet
  if (location == null) {
    _controller?.close();
    _controller = null;
    return;
  }
  // show location details
  _controller = _scaffoldKey.currentState?.showBottomSheet((context) {
    return Container(
      color: Colors.white,
      padding: const EdgeInsets.only(bottom: 50.0, left: 100, right: 100),
      child: Column(
        mainAxisSize: MainAxisSize.min,
        children: [
          const SizedBox(
            height: 30,
          ),
          Text(location.name),
          const SizedBox(
            height: 30,
          ),
          Text("Description: ${location.description}"),
          const SizedBox(
            height: 30,
          ),
          Text("Building: ${location.buildingName} - ${location.floorName}"),
          const SizedBox(
            height: 30,
          ),
          ...
        ],
      ),
    );
  });
  _controller?.closed.then((value) => _mapControl.selectLocation(null));
}
```

Now we will create a class that takes a origin and destination and can handle the controls of a route. This is a rather complex class that creates a route, lets us traverse it without errors and displays the steps of the route in the bottom sheet. A minimal example would only require the constructor, the leg selected listener and the UI.

First lets set up the constructor and services, we want to create the `RouteHandler` with an origin, destination and the state of our `Scaffold` (to create a bottom sheet later). We will also set up our [`MPDirectionsService`](https://pub.dev/documentation/mapsindoors\_googlemaps/latest/mapsindoors/MPDirectionsService-class.html) and [`MPDirectionsRenderer`](https://pub.dev/documentation/mapsindoors\_googlemaps/latest/mapsindoors/MPDirectionsRenderer-class.html) and generate a route immediately. finally we show the route on the Renderer.

```dart
class RouteHandler {
  RouteHandler(
      {required MPPoint origin,
      required MPPoint destination,
      required ScaffoldState scaffold}) {
    _service.getRoute(origin: origin, destination: destination).then((route) {
      _route = route;
      _renderer.setRoute(route);
      // Start some UI handling here later
    });
  }
  final _service = MPDirectionsService();
  final _renderer = MPDirectionsRenderer();
  late final MPRoute _route;
}
```

Now we want some controllers for the state of our route, we want a method to safely update the leg index, we want to listen to when the leg index changes and we want to be able to remove the route again. First we add a [`onLegSelectedListener`](https://pub.dev/documentation/mapsindoors\_googlemaps/latest/mapsindoors/OnLegSelectedListener.html) to the class and set it on the renderer. Then we add a getter/setter for the current leg index, where we ensure that we cannot get an index that is out of bounds for the current route. and finally we add a `removeRoute` method that clears the route from the map.

```dart
class RouteHandler {
  RouteHandler(
      {required MPPoint origin,
      required MPPoint destination,
      required ScaffoldState scaffold}) {
    _service.getRoute(origin: origin, destination: destination).then((route) {
      ...
      _renderer.setOnLegSelectedListener(onLegSelected);
      // Start some UI handling here later
    });
  }

  ...

  // backing field for the current route leg index
  int _currentIndex = 0;

  int get currentIndex {
    return _currentIndex;
  }

  // clamp the index to be in the correct range
  set currentIndex(int index) {
    _currentIndex = index.clamp(0, _route.legs!.length - 1);
  }

  // updates the state of the routehandler if the route is updated externally, eg. by tapping the next marker on the route
  void onLegSelected(int legIndex) {
    _controller?.setState!(() => currentIndex = legIndex);
  }

  // external handle to clear the route
  void removeRoute() {
    _renderer.clear();
  }
}
```

Finally we want some UI interaction, so we add a `ShowRoute` method that creates a `BottomSheet` which contains some navigation icons, to move between legs of the route, and a text field to display the steps of the current leg. We also add a method to expand the route steps in a way that we can show the leg in a single string.

Then we call `showRoute` from our constructor. Additionally we close the `BottomSheet` when removing the map.

```dart
class RouteHandler {
  RouteHandler(
      {required MPPoint origin,
      required MPPoint destination,
      required ScaffoldState scaffold}) {
    _service.setTravelMode(MPDirectionsService.travelModeDriving);
    _service.getRoute(origin: origin, destination: destination).then((route) {
      ...
      showRoute(scaffold);
    });
  }
  PersistentBottomSheetController? _controller;

  ...

  // opens the route on a bottom sheet
  void showRoute(ScaffoldState scaffold) {
    _controller = scaffold.showBottomSheet((context) {
      return Container(
        color: Colors.white,
        padding: const EdgeInsets.only(top: 50.0, bottom: 50.0),
        child: Row(
          mainAxisAlignment: MainAxisAlignment.center,
          mainAxisSize: MainAxisSize.min,
          children: [
            // goes a step back on the route
            IconButton(
              onPressed: () async {
                currentIndex--;
                await _renderer.selectLegIndex(currentIndex);
              },
              icon: const Icon(Icons.keyboard_arrow_left),
              iconSize: 50,
            ),
            // displays the route instructions
            Expanded(
              child: Text(
                expandRouteSteps(_route.legs![currentIndex].steps!),
                softWrap: true,
                textAlign: TextAlign.center,
              ),
            ),
            // goes a step forward on the route
            IconButton(
              onPressed: () async {
                currentIndex++;
                await _renderer.selectLegIndex(currentIndex);
              },
              icon: const Icon(Icons.keyboard_arrow_right),
              iconSize: 50,
            ),
          ],
        ),
      );
    });
    // if the bottom sheet is closed, then clear the route
    _controller?.closed.then((val) {
      _renderer.clear();
    });
  }

  // external handle to clear the route
  void removeRoute() {
    _renderer.clear();
    _controller?.close();
  }

  // expands the step instructions into a single string for the entire leg
  String expandRouteSteps(List<MPRouteStep> steps) {
    String sum = "${steps[0].maneuver}";
    for (final step in steps.skip(1)) {
      sum += ", ${step.maneuver}";
    }
    return sum;
  }
}
```

//MADHTODO With this we now have a class that will handle creating a Route and displaying it on the map for us. You can see the entire implementation of RouteHandler here: [main.dart](https://docs.mapsindoors.com/getting-started/flutter/example.com)

We can now add a button on our `onLocationSelected` bottomsheet to generate a route from `_userPosition` to the selected location:

```dart
final _scaffoldKey = GlobalKey<ScaffoldState>();
RouteHandler? _routeHandler;
PersistentBottomSheetController? _controller;

void onLocationSelected(MPLocation? location) {
  ...
  // if an active route is displayed, remove it from view
  _routeHandler?.removeRoute();
  // show location details
  _controller = _scaffoldKey.currentState?.showBottomSheet((context) {
    return Container(
      ...
      child: Column(
        mainAxisSize: MainAxisSize.min,
        children: [
          ...
          // when clicked will create a route from the user position to the location
          ElevatedButton(
            onPressed: () => _routeHandler = RouteHandler(
                origin: _userPosition,
                destination: location.point,
                scaffold: _scaffoldKey.currentState!),
            child: const Row(
              children: [
                Icon(Icons.keyboard_arrow_left_rounded),
                SizedBox(
                  width: 5,
                ),
                Text("directions")
              ],
            ),
          ),
        ],
      ),
    );
  });
  _controller?.closed.then((value) => _mapControl.selectLocation(null));
}
```

We can now show and navigate a route from our `_userPosition` to a selected location.

### Change Transportation Mode[​](https://docs.mapsindoors.com/getting-started/flutter/directions#change-transportation-mode) <a href="#change-transportation-mode" id="change-transportation-mode"></a>

In MapsIndoors, the transportation mode is referred to as **travel mode**. There are four travel modes, **walking**, **bicycling**, **driving** and **transit** (public transportation). The travel modes generally applies for outdoor navigation. Indoor navigation calculations are based on **walking** travel mode.

To swap Travel Modes you set the Travel Mode before making a query for the route:

```dart
RouteHandler(
    {required MPPoint origin,
    required MPPoint destination,
    required ScaffoldState scaffold}) {
  _service.setTravelMode(MPDirectionsService.travelModeDriving);
  _service.getRoute(origin: origin, destination: destination).then((route) {
    _route = route;
    _renderer.setRoute(route);
    _renderer.setOnLegSelectedListener(onLegSelected);
    showRoute(scaffold);
  });
}
```

Expected result:

![An animation showing the desired behaviour of this tutorial](https://docs.mapsindoors.com/img/getting-started/flutter\_directions.gif)

//MADHTODO The accompanying UI and implementation of this directions experience can be found in the getting started app sample. [Getting Started App sample](https://github.com/MapsPeople/getting\_started\_flutter)

[\
](https://docs.mapsindoors.com/getting-started/flutter/search)
