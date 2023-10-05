# Getting Directions

We are now able to search for locations accross our solution, so we can now use these search results to get a route between locations.

Firstly we'll add some buttons to the search results to allow a user to select an origin and destination for the route.

Inside the `SearchResultItem`add `setFromLocation` and `setToLocation` to the `SearchResultItemProps`

Add the `setFromLocation` and `setToLocation` to the constructor of `SearchResultItem` as well.

Finally inside the view of `SearchResultItem` add a view with two `TouchableOpacity` elements that when pressed calls the `setFromLocation` and `setToLocation`. Add a to and from `Text` element inside them to indicate what function they are using.

The implementation of `setFromLocation` and `setToLocation` will come later.

```
type SearchResultItemProps = {
  item: MPLocation;
  clickResult: (location: MPLocation) => void;
  setFromLocation: (loc: MPLocation) => void;
  setToLocation: (location: MPLocation) => void;
}

export default function SearchResultItem({item, clickResult, setFromLocation, setToLocation}: SearchResultItemProps) {
  const fallbackImageUrl = "data:image/gif;base64,R0lGODlhAQABAIAAAAUEBAAAACwAAAAAAQABAAACAkQBADs="

  const [itemURL, setItemURL] = useState(fallbackImageUrl)

  useEffect(() => {
    const _ = async () => {
      const url = await (await MapsIndoors.getDisplayRuleByLocation(item))?.getIconUrl()
      setItemURL(url ?? fallbackImageUrl);
    };
    _().then();
  }, [])

  return (
      <TouchableOpacity
          style={{paddingVertical: 5, width: '100%'}}
          onPress={(event) => clickResult(item)}>
        <View style={{backgroundColor: Colors.lighter, padding: 5, flexDirection: 'row'}}>
          <Image source={{uri: itemURL}} style={{width: 42, height:42}} />
          <View style={{flex: 1, paddingHorizontal: 10}}>
            <Text style={{fontSize: 20, color: Colors.dark}} numberOfLines={1}>{item.name}</Text>
            <Text style={{fontSize: 10, color: Colors.dark}} numberOfLines={1}>{item.buildingName}</Text>
          </View>
          <View style={{flexDirection: 'column', height: '100%'}}>
            <TouchableOpacity onPress={() => setFromLocation(item)} style={{flex: 1, backgroundColor: Colors.light, borderColor: Colors.lighter, borderWidth: 1, padding: 2}}>
                <Text style={{textAlign: 'center', color: Colors.dark}}>From</Text>
            </TouchableOpacity>
            <TouchableOpacity onPress={() => setToLocation(item)} style={{flex: 1, backgroundColor: Colors.light, borderColor: Colors.lighter, borderWidth: 1, padding: 2}}>
                <Text style={{textAlign: 'center', color: Colors.dark}}>To</Text>
            </TouchableOpacity>
          </View>
        </View>
      </TouchableOpacity>
  );
}
```

We will now add a `NavigationHeader` to the bottom sheet. The navigation header will be used to show the locations selected for getting directions. It's a simple view of two text fields that includes the selected locations name and venue as well as a button to get a route.

Open the `NavigationHeader.tsx` file. Start by expanding the `NavigationHeaderProps` with a to and `fromLocation` as well as adding a function `getRoute`. Inside the third view add two `Text` elements using the `BoldText` to highlight to and from, and use the location as well as venue name to describe the chosen to or from location. Lastly add the `getRoute` function to the `Query` buttons `onPress`.

```
const BoldText = (props: React.PropsWithChildren) => <Text style={{fontWeight: 'bold'}}>{props.children}</Text>

type NavigationHeaderProps = {
  searchResults: MPLocation[] | undefined;
  fromLocation: MPLocation | undefined;
  toLocation: MPLocation | undefined;
  getRoute: () => void;
}

export default function NavigationHeader({searchResults, fromLocation, toLocation, getRoute}: NavigationHeaderProps) {
  return (
    <View style={{flexDirection: 'column'}}>
      <View style={{
          flexDirection: 'row',
          backgroundColor: '#FFA8',
          marginVertical: 10,
          borderColor: Colors.dark,
          borderWidth: 1,
          display: fromLocation || toLocation || searchResults ? 'flex' : 'none'
        }}>
        <View style={{flex: 1, paddingHorizontal: 5}}>
          <Text style={{color: Colors.dark}}>
            <BoldText>From</BoldText> {fromLocation?.name} ({fromLocation?.venueName})
          </Text>
          <Text style={{color: Colors.dark}}>
            <BoldText>To</BoldText> {toLocation?.name} ({toLocation?.venueName})
          </Text>
        </View>
        <Button title={'Query'} onPress={() => {
          getRoute();
        }}/>
      </View>
    </View>
  )
}
```

We now want to implement the `setFromLocation` and `setToLocation` inside our `MapScreen` as well as adding the navigation header to the bottom sheet.

First add the function to the props and constructor of `SearchResults` as well as forward it to the `SearchResultItem` inside the `BottomSheetFlatList`.

```
type SearchResultsProps = {
  searchResults: MPLocation[] | undefined;
  setFromLocation: (loc: MPLocation) => void;
  setToLocation: (location: MPLocation) => void;
  clickResult: (location: MPLocation) => void;
}

export default function SearchResults({searchResults, setFromLocation, setToLocation, clickResult}: SearchResultsProps) {

  return (
      <BottomSheetFlatList data={searchResults} keyExtractor={(item, index) => index.toString()}
                renderItem={({item}) => <SearchResultItem item={item} clickResult={clickResult} setFromLocation={setFromLocation} setToLocation={setToLocation} />}
      />
  )
}
```

Then we add the `NavigationHeader` inside our BottomSheet of the `MapScreen` component as well as the `fromLocation` and `toLocation` hooks and supply them to the `SearchResults` component inside the BottomSheet.

```
export default function MapScreen({navigation, route}) {

  const [fromLocation, setFromLocation] = useState<MPLocation>();
  const [toLocation, setToLocation] = useState<MPLocation>();

  const {width, height} = useWindowDimensions();

  return (
      <GestureHandlerRootView style={{flex:1, flexGrow:1}}>
        <MapView style={{
          width: width,
          height: height,
          flex: 1,
        }}/>
        <View style={{position: 'absolute', width: '100%', height: '100%', padding: 10, pointerEvents: 'box-none'/*ignore touch events but children can be clicked*/}}>
          <SearchBox onSearch={search}/>
        </View>
        <BottomSheet ref={bottomSheet} snapPoints={['15%', '60%']} index={-1} enablePanDownToClose={true} onChange={() => {}} onClose={() => {setSearchResults(undefined)}} /*onChange={}*/>
          <NavigationHeader searchResults={searchResults}
            fromLocation={fromLocation}
            toLocation={toLocation}
            getRoute={getRoute}/>
            <SearchResults searchResults={searchResults} setFromLocation={setFromLocation} setToLocation={setToLocation} clickResult={clickResult}/>
        </BottomSheet>
      </GestureHandlerRootView>
  );
}
```

Now that our `MapScreen` is aware of the to and from locations. We will create a function inside the `MapScreen` that queries a route between the two locations. First we will add three `useState` hooks that stores information to display information about the route as well as rendering it on the map.

Inside the `getRoute` function we will first make sure that both the to and `fromLocation` is defined. Then we will create an `MPDirectionsService` this is used to query for a route, as well as configuring the route that we want to query. Here we set our `travelMode` to walking and then call `getRoute` on the `MPDirectionsService` to get a route result between the two locations. We the create an `MPDirectionsRenderer` note the supplied NativeEventEmitter this is used internally to send events between native and React Native. We set the route we received from the `MPDirectionsService` on the `MPDirectionsRenderer` which will cause the route to be rendered onto the map. We will also add a listener on the `MPDirectionsRenderer` that listens for leg changes, so that we can react to this with our views later on.

```
  const [mproute, setMPRoute] = useState<MPRoute>();
  const [directionsRenderer, setDirectionsRenderer] = useState<MPDirectionsRenderer|undefined>(undefined);
  const [routeLeg, setRouteLeg] = useState<number>();

  const getRoute = async () => {
    if (!fromLocation || !toLocation) {
      return
    }

    // Query route
    console.debug('Querying Route');
    
    //Creating the directions service, if it has not been created before.
    const directionsService = await MPDirectionsService.create();
    //Setting the travel mode to walking, to ensure instructions are for walking.
    await directionsService.setTravelMode('walking');

    let from = fromLocation.position
    let to = toLocation.position;
    console.debug({from}, {to})

    //Querying the route through the directionsService after 
    const route = await directionsService.getRoute(from, to);
    console.debug({route});
    setMPRoute(route);

    //Creating the directions renderer
    const directionsRenderer = new MPDirectionsRenderer(NativeEventEmitter);
    //Setting the route on the directions renderer, causing it to be rendered onto the map.
    await directionsRenderer.setRoute(route);

    setDirectionsRenderer(directionsRenderer);

    //Listen for leg changes
    directionsRenderer.setOnLegSelectedListener((leg) => {
      setRouteLeg(leg);
    });
  }
```

Now that we have the `MPRoute` shown on the map we want to describe the individual legs of the generated route. So we will create ui that we can replace the search results in the bottom sheet with. We will create a simple view, `RouteInstructionLeg` that describes the step of the selected leg as well as two buttons to navigate back and forth between the legs of the route.

Add the `MPRouteLeg` `useState` hook to emit when the leg changes of the route. Add the `RouteInstructionLeg` inside the empty view component of `RouteInstructions`. Finally implement a `useEffect` hook that listens for the goToPage number changing, that will change the leg of the route, causing the `RouteInstructionLeg` to change the described leg.

```jsx
export default function RouteInstructions({route, goToPage, onPrevious, onNext}: {route: MPRoute|undefined, goToPage: number|undefined, onPrevious: (()=>void)|undefined, onNext: (()=>void)|undefined}) {

  const [leg, setLeg] = useState<MPRouteLeg|undefined>(route?.legs![0]);

  useEffect(() => {
    if (goToPage !== undefined) {
      setLeg(route?.legs![goToPage]);
    }
  }, [goToPage])

  return <View style={{padding: 5, flexDirection: 'column', height: '60%'}}>
    <Text style={{color: Colors.dark}}>legs: {route?.legs?.length}</Text>
    <View style={{flexDirection: 'column', flex: 1}}>
      <RouteInstructionLeg leg={leg}/>
    </View>
    <View>
      <Button title="next" onPress={onNext}/>
      <Button title="previous" onPress={onPrevious}/>
    </View>
  </View>
}
```

The `RouteInstructions` are accompanied with a short step instruction for the route. That uses the `htmlInstructions` of the steps to describe each step, as well as the distance and duration of the selected Leg

Add two `Text` elements inside the view one as a header with "Leg instructions" and one that describes the accumulated duration and distance of the leg. Add a `BottomSheetFlatList` that takes the steps of the leg that shows a `Text` element per step using the `htmlInstructions` of the steps to describe each of them.

```jsx
export default function RouteInstructionLeg({leg}: {leg: MPRouteLeg|undefined}) {
  return <View style={{ backgroundColor: Colors.light, padding: 5, height: '100%'}}>
    <Text style={{color: Colors.dark}}>Leg instructions</Text>
    <Text style={{color: Colors.dark}}>{`dist:${leg?.distance?.value} dur:${leg?.duration?.value}`}</Text>
    <BottomSheetFlatList data={leg?.steps} renderItem={ ({item: step}) =>
      <Text style={{color: Colors.dark}}>{step.htmlInstructions}</Text>
    }/>
  </View>
}
```

We now need to implement the `onNext` and `onPrevious` methods as well as integrate these views inside the bottom sheet of our `MapScreen` We'll start by creating a condition inside the BottomSheet view, that renders a view depending on if the `MPRoute` is not undefined. The MPRoute is the one we set inside the `getRoute` function. The goToPage listens to the index of the current route, by listening to the onLegSelected.

Inside the `BottomSheet` of the `MapScreen` Add the condition if `mproute` is defined below the `NavigationHeader`. When true we will display the `RouteInstructions` and when false, the `SearchResults` are shown.

acAdd the `RouteInstructions` with the route from our condition, use the `routeLeg` `useState` for goToPage, and add calls to the `directionsRenderer` for the previous and next leg. Move the `SearchResults` from previous inside the condition, to be shown when the `mproute` is not set. We have also implemented a function inside the `onClose` of our `BottomSheet` called clear. We will implement the functionality below.

```jsx
<BottomSheet ref={bottomSheet} snapPoints={['15%', '60%']} index={-1} enablePanDownToClose={true} onChange={() => {}} onClose={clear}>
  <NavigationHeader searchResults={searchResults}
    fromLocation={fromLocation}
    toLocation={toLocation}
    getRoute={getRoute}/>
    { mproute ?
      <RouteInstructions route={mproute} 
      goToPage={routeLeg} 
      onPrevious={() => directionsRenderer?.previousLeg()} 
      onNext={() => directionsRenderer?.nextLeg()}/>
      :
      <SearchResults searchResults={searchResults}
        setFromLocation={setFromLocation}
        setToLocation={setToLocation}
        clickResult={clickResult}/>
    }
</BottomSheet>
```

Lastly to be able to close our bottomsheet and create new searches we will setup a function inside the `SearchBox` that removes the search results and the MPRoute. Also clearing the currently rendered route and closing the bottomsheet.

Add the function `clear` inside our `MapScreen` component, that sets our route, searchResults, to and fromLocation to undefined. Add a `useEffect` hook that checks when the `mproute` and `searchResults` are undefined, that clears any rendered route as well as closing our `BottomSheet`. Inside the `SearchBox` on the `MapScreen` component, add an `onCancel` parameter, that invokes the `clear` function we just added.

```javascript
const clear = () => {
    setMPRoute(undefined);
    setToLocation(undefined);
    setSearchResults(undefined);
    setFromLocation(undefined);
}

useEffect(() => {
    if (!mproute && !searchResults) {
        bottomSheet.current?.close();
        directionsRenderer?.clear();
    }
}, [mproute, searchResults])

<View style={{position: 'absolute', width: '100%', height: '100%', padding: 10, pointerEvents: 'box-none'}}>
    <SearchBox onSearch={search} onCancel={clear}/>
</View>
```

Add the `onCancel` function to the `SearchBox` and call that from the cancel button already implemented.

```javascript
export default function SearchBox({onSearch, onCancel}) {
  return (
    ...
    <Button title={'Search'} onPress={() => onSearch(searchBoxText)}/>
    <Button title={'Cancel'} onPress={() => onCancel()}/>
    ...
  )
}
```
