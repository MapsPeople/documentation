# Creating a Search Experience

Now you have simple app showing a map. In this step, you'll create a search implementation and display the search results in a list.

Add the searchResults state and search function inside the `MapScreen` function.

```tsx
  const [searchResults, setSearchResults] = useState<MPLocation[]|undefined>(undefined);
```

We define a function `search` that performs a text search on the name of locations. It creates a `MPQuery` containing the search string, as well as an empty filter. These are passed to `MapsIndoors.getLocationsAsync`, which asynchronously returns a list of `MPLocations`.

```tsx
  const search = async (text: string | undefined) => {
    if (text === undefined) {
      console.debug('Clearing search');
      setSearchResults(undefined);
      return;
    }

    // Do search
    console.debug(`Searching for "${text}"`);

    const query = MPQuery.create({query: text});
    const filter = MPFilter.create();

    const locations = await MapsIndoors.getLocationsAsync(query, filter)

    setSearchResults(locations);
    (bottomSheet.current as BottomSheet).expand();
  };
```

We also define a clickResult, which will determine what happens when one of the search results are clicked. In this case we will `goTo` on `MapControl` for the location that was clicked. This will pan the map to the location the user clicked, add this inside the `MapScreen` function.

```javascript
  const clickResult = (result: MPLocation) => {
    //Centers the map to the chosen MPLocation
    mapControl?.goTo(result);
  };
```

Now for the changes to the rendered layout.

We have added two new `View`s, one containing the `SearchBox` component that will represent a search bar on the top of the screen, as well as added the `SearchResults` component inside the BottomSheet. Notice that the `onSearch` of the searchBox calls the `search` function that we implemented. This will set the `searchResults` populating our searchResults component and expanding the BottomSheet showing the results of the search.

We also add the `onClose` parameter to the BottomSheet that will clear the previous `searchResult` when the BottomSheet is closed.

```tsx
return (
    <GestureHandlerRootView style={{flex:1, flexGrow:1}}>
      <MapView style={{
        width: width,
        height: height,
        flex: 1,
      }}/>
      <View style={{position: 'absolute', width: '100%', height: '100%', padding: 10, pointerEvents: 'box-none'}}>
        <SearchBox onSearch={search}/>
      </View>
      <BottomSheet ref={bottomSheet} snapPoints={['15%', '60%']} index={-1} enablePanDownToClose={true} onClose={() => {setSearchResults(undefined)}}>
        <SearchResults searchResults={searchResults} clickResult={clickResult}/>
      </BottomSheet>
    </GestureHandlerRootView>
  );
```

The `SearchBox` and `SearchResults` components have already been made, but we need to fill in some data to show our search results from the `MPLocation`. The SearchBox is just a simple input field with two buttons, to call our search function with a string value from the input box. This can be left as is for now.

components/SearchBox.ts

```tsx
import React, {useState} from "react";
import {Button, TextInput, View} from "react-native";
import {Colors} from "react-native/Libraries/NewAppScreen";

export default function SearchBox({onSearch}) {
  const [searchBoxText, setSearchBoxText] = useState<string>("");

  return (
      <View  style={{flexDirection: 'row'}}>
        <TextInput
            enterKeyHint={'search'}
            value={searchBoxText}
            onChangeText={setSearchBoxText}
            onSubmitEditing={(event) => onSearch(event.nativeEvent.text)}
            placeholder={'Search...'} placeholderTextColor={'#AAA'}
            style={{
              height: 48,
              backgroundColor: Colors.white,
              borderColor: Colors.dark,
              borderWidth: 1,
              paddingHorizontal: 5,
              flex: 1,
            }}
        />
        <Button title={'Search'} onPress={() => onSearch(searchBoxText)}/>
        <Button title={'Cancel'} onPress={() => onSearch(undefined)}/>
      </View>
  );
}
```

The `searchResults` component is a `BottomSheetFlatList` that contains a list of items that display the results of the search.

components/SearchResults.ts

```tsx
type SearchResultsProps = {
  searchResults: MPLocation[] | undefined;
  clickResult: (location: MPLocation) => void;
}

export default function SearchResults({searchResults, clickResult}: SearchResultsProps) {

  return (
      <BottomSheetFlatList data={searchResults} keyExtractor={(item, index) => index.toString()}
                renderItem={({item}) => <SearchResultItem item={item} clickResult={clickResult} />}
      />
  )
}
```

First we'll implement the `useEffect` hook to retrieve an iconUrl from the `MPLocation`. Here we get the `DisplayRule` of the location through `MapsIndoors` and use the `IconUrl` to display an image in our view for the location.

We will then add two `Text` elements inside the second view of `SearchResultItem` to display the `name` as well as the `buildingName` of the MPLocation.

components/SearchResultItem.ts

```tsx
type SearchResultItemProps = {
  item: MPLocation;
  clickResult: (location: MPLocation) => void;
}

export default function SearchResultItem({item, clickResult}: SearchResultItemProps) {
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
        </View>
      </TouchableOpacity>
  );
}
```

You should now be able to run the application and search for locations inside the solution, and be able to click them to show where the location is on the map, as well as read the name and the possible building tied to it.
