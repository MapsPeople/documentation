# Displaying a Map

Your environment is now fully configured, and you have the necessary Google Maps and MapsIndoors API keys. Next you will learn how to load a map with MapsIndoors and display it in a React Native app.

### Selecting and Loading a Solution[​](https://docs.mapsindoors.com/getting-started/React%20Native/display-a-map#selecting-and-loading-a-solution) <a href="#selecting-and-loading-a-solution" id="selecting-and-loading-a-solution"></a>

To start off, we set up a simple two screen navigation using the `@react-navigation` package. We will create a FrontScreen component that lets us chose the solution to open, and a MapScreen component that will display the actual map.

App.tsx

```javascript
import {NavigationContainer} from "@react-navigation/native";
import {createNativeStackNavigator} from "@react-navigation/native-stack";
import FrontScreen from "./screens/FrontScreen";
import MapScreen from "./screens/MapScreen";

function App(): React.JSX.Element {
  const Stack = createNativeStackNavigator();

  return (
      <NavigationContainer>
        <Stack.Navigator>
          <Stack.Screen name={'Front'} component={FrontScreen}/>
          <Stack.Screen name={'Map'} component={MapScreen}/>
        </Stack.Navigator>
      </NavigationContainer>
  )
}

export default App;
```

The `FrontScreen` is defined as a functional component `function FrontScreen(/*...*/) {/*...*/}` that returns the `View` to be displayed. The property `navigation` gives us access to the navigator and lets us navigate to other screens.

We use a `useState` hook `apiKeyString` for storing the API key entered by the user and to pass it to the next screen when navigating.

As for what to actually render, we return a react fragment containing a button with a preset API key for a demo solution. And following that, a row containing an input box and a button for using the entered API key. In order to navigate to the MapScreen, we call `navigation.navigate('Map', {apiKey: apiKeyString})}` where `'Map'` is the name we gave the screen in the navigator, and we pass the API key in the parameters, which will be available through `route` on the following screen.

**The `FrontScreen` and app `App` are already implemented for you.**

screens/FrontScreen.tsx

```javascript
import {Button, TextInput, View} from "react-native";
import {Colors} from "react-native/Libraries/NewAppScreen";
import {useState} from "react";


export default function FrontScreen({navigation}) {
  const [apiKeyString, setApiKeyString] = useState(''); // The API key entered into the TextInput

  return (
      <>
        <Button title={'White House demo'} onPress={() => navigation.navigate('Map', {apiKey: 'd876ff0e60bb430b8fabb145'})}/>
        <View style={{flexDirection: 'row'}}>
          <TextInput placeholder={'Enter an API key for your solution'} placeholderTextColor={'#AAA'} onChangeText={setApiKeyString} style={{flex: 1, backgroundColor: Colors.white}}/>
          <Button title={'Go to map'} onPress={() => navigation.navigate('Map', {apiKey: apiKeyString})}/>
        </View>
      </>
  )
}
```

### Showing the map[​](https://docs.mapsindoors.com/getting-started/React%20Native/display-a-map#showing-the-map) <a href="#showing-the-map" id="showing-the-map"></a>

For the `MapScreen` we create a functional component similarly to the above. Note the additional property `route` which contains parameters from the navigation stack, such as the API key we provided from the `FrontScreen`

From the basic example you are now left to implement the `load` function as well as the `useEffect` hook and adding the `MapView` to the returned view.

We store the reference to our `MapControl` in a `useState` hook, so that we can pass it to other components in the future. We define a `load` function, which gets the API key from `route.params`. We use it to load our solution data through `MapsIndoors`, create a `MapControl` that is used to control the displayed map, and navigate to the first venue in the solution by using `MapControl`. Be sure to `await` the asynchronous API calls to know when it has fully loaded.

We use a `useEffect` hook to load when entering the `MapScreen`.

Actually showing the Map is pretty simple, consisting of returning a `MapView` component, styled to set the appropriate size.

{% hint style="warning" %}
Currently there is a limitation that on some platforms the size of the `MapView` must be provided in actual dimensions, as opposed to percentages or `flex`. We can get the size of the screen using `useWindowDimensions()`, and use `flex: 1` to reduce the size to fit.
{% endhint %}

Here is the first implementation of the `MapScreen`, with the logic described above.

```javascript
import MapsIndoors, {
  MapControl,
  MapView,
  MPMapConfig,
} from "@mapsindoors/react-native-maps-indoors-google-maps";
import {NativeEventEmitter, useWindowDimensions} from "react-native";
import React, {useEffect, useState} from "react";


export default function MapScreen({navigation, route}) {

  const [mapControl, setMapControl] = useState<MapControl>()

  const load = async () => {
    try {
      const {apiKey} = route.params;

      console.log('Loading MapsIndoors...');
      await MapsIndoors.load(apiKey);

      console.log('Creating MapControl...');
      const mapControl = await MapControl.create(new MPMapConfig({useDefaultMapsIndoorsStyle: true}), NativeEventEmitter);
      setMapControl(mapControl)

      console.log('Moving to venue...');
      await mapControl.goTo((await MapsIndoors.getVenues()).getAll()[0]);

      console.log('Done!');

    } catch (e) {
      console.error('Error while loading MapsIndoors:', e);
      navigation.pop();
    }
  };

  // Load MapsIndoors when the app mounts, i.e. the MapView should be ready.
  useEffect(() => {
    load().catch((reason) => {console.error('MapsIndoors failed to load:', reason)});
  }, []); // NOTE: the second parameter deps: [] is important, otherwise it will load everytime the MapView changes

  const {width, height} = useWindowDimensions();

  return (
    <GestureHandlerRootView style={{flex:1, flexGrow:1}}>
      <MapView style={{
        width: width,
        height: height,
        flex: 1,
      }}/>
      <BottomSheet ref={bottomSheet} snapPoints={['15%', '60%']} index={-1} enablePanDownToClose={true}>
      </BottomSheet>
    </GestureHandlerRootView>
  );
}
```
