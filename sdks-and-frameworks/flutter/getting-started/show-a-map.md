# Show a Map

Your environment is now fully configured, and you have the necessary map platform and MapsIndoors API keys. Next you will learn how to load a map with MapsIndoors.

{% hint style="info" %}
Please note that data in MapsIndoors is loaded asynchronously. This results in behavior where data might not have finished loading if you call methods accessing it immediately after initializing MapsIndoors. Best practice is to set up `listeners` or `delegates` to inform of when data is ready. Please be aware of this when developing using the MapsIndoors SDK.
{% endhint %}

\
Lets start by setting up a simple app with a stateful map widget to contain the app.

First add the app to your main and import mapsindoors

```dart
import 'package:mapsindoors_googlemaps/mapsindoors.dart';
import 'package:flutter/material.dart';

void main() {
  runApp(const MapsIndoorsDemoApp());
}

class MapsIndoorsDemoApp extends StatelessWidget {
  const MapsIndoorsDemoApp({super.key});

  @override
  Widget build(BuildContext context) {
    return MaterialApp(
      title: 'Flutter MapsIndoors Demo',
      theme: ThemeData(
        primarySwatch: Colors.blue,
      ),
    );
  }
}
```

Now we can create the stateful widget, we require a mapsindoors API key when constructing the map, to ensure the map is only linked to a single mapsindoors instance.

```dart
/// The widget that will contain the map
class Map extends StatefulWidget {
  const Map({super.key, required this.apiKey});
  final String apiKey;

  @override
  State<Map> createState() => _MapState();
}

class _MapState extends State<Map> {
  @override
  Widget build(BuildContext context) {
    throw UnimplementedError();
  }
}
```

Now we can add the Map to our App

```dart
class MapsIndoorsDemoApp extends StatelessWidget {
  const MapsIndoorsDemoApp({super.key});

  @override
  Widget build(BuildContext context) {
    return MaterialApp(
      title: 'Flutter MapsIndoors Demo',
      theme: ThemeData(
        primarySwatch: Colors.blue,
      ),
      home: const Map(
        apiKey: 'gettingstarted',
      ), 
    );
  }
}
```

#### Initialize MapsIndoors[​](https://docs.mapsindoors.com/getting-started/flutter/map#initialize-mapsindoors) <a href="#initialize-mapsindoors" id="initialize-mapsindoors"></a>

We start by loading `MapsIndoors`. `MapsIndoors` is used to get and store all references to MapsIndoors-specific data. This includes access to all MapsIndoors-specific geodata.

Place the following initialization code in the `initState` method of your apps `State` that displays the Google map, Ensure that the [`loadMapsIndoors`](https://pub.dev/documentation/mapsindoors\_googlemaps/latest/mapsindoors/loadMapsIndoors.html) callback returns before accessing other `MapsIndoors` methods.

```dart
class _MapState extends State<Map> {
  @override
  void initState() {
      super.initState();
      loadMapsIndoors(widget.apiKey).then((error) {
        // if no error occured during loading, then we can start using the SDK
        if (error == null) {
          // do stuff like fetching locations
        }
      });
  }
}
```

If you are not a customer you can use this demo MapsIndoors API key `d876ff0e60bb430b8fabb145`.

#### Initialize MapsIndoorsWidget[​](https://docs.mapsindoors.com/getting-started/flutter/map#initialize-mapsindoorswidget) <a href="#initialize-mapsindoorswidget" id="initialize-mapsindoorswidget"></a>

We now want to add all the data we get by initializing `MapsIndoors` to our map. This is done by initializing [`MapsIndoorsWidget`](https://pub.dev/documentation/mapsindoors\_googlemaps/latest/mapsindoors/MapsIndoorsWidget-class.html) onto the map. `MapsIndoorsWidget` is used as a layer between the map and MapsIndoors.

First add the `MapsIndoorsWidget` to the build hierarchy, in this example it is placed in the body of a `Scaffold`, but it can be placed anywhere, just ensure there is enough space to use the map comfortably.

Then create a `OnMapReadyListener` method to handle the callback from initializing `MapsIndoorsWidget`:

```dart
class _MapState extends State<Map> {
  late MapsIndoorsWidget _mapControl;

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      key: _scaffoldKey,
      resizeToAvoidBottomInset: false,
      appBar: AppBar(title: Text("Getting Started Project")),
      body: Center(
        child: Column(
          mainAxisAlignment: MainAxisAlignment.start,
          children: <Widget>[
            _mapControl = MapsIndoorsWidget(
              readyListener: onMapControlReady,
            ),
          ],
        ),
      ),
    );
  }

  void onMapControlReady(MPError? error) async {
    if (error == null) {
      ...
      _mapControl.goTo(await getDefaultVenue());
    } else {
      // if loading mapcontrol failed inform the user
      ScaffoldMessenger.of(context).showSnackBar(SnackBar(
        content: Text("Map load failed: $error"),
        backgroundColor: Colors.red,
      ));
    }
  }
}
```

Expected result:

<figure><img src="../../../.gitbook/assets/flutter_map.gif" alt=""><figcaption></figcaption></figure>

See the full example of the app here: [main.dart](https://github.com/MapsPeople/getting\_started\_flutter/blob/main/lib/main.dart)
