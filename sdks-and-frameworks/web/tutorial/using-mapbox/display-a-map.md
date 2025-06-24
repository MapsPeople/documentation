# Display a map

**Goal:** This guide will walk you through initializing the MapsIndoors SDK and displaying your first interactive indoor map using Mapbox GL JS as the map provider.

**SDK Concepts Introduced:**

* Setting the MapsIndoors API Key using `mapsindoors.MapsIndoors.setMapsIndoorsApiKey()`.
* Initializing the Mapbox map view using `mapsindoors.mapView.MapboxV3View`.
* Creating the main `mapsindoors.MapsIndoors` instance.
* Adding a `mapsindoors.FloorSelector` control.
* Using `mapsIndoorsInstance.goTo()` to pan and zoom the map to the selected location.
* Handling map clicks to center the map on a clicked POI (location) using `mapsIndoorsInstance.on('click', callback)`

### Prerequisites

* Completion of the [Set Up Your Environment guide](../set-up-your-environment.md).
* You will need a **Mapbox Access Token**. If you don't have one, you can create one for free on the [Mapbox website](https://www.mapbox.com/signup/).
* You will need a **MapsIndoors API Key**. For this tutorial, you can use the demo API key: `02c329e6777d431a88480a09`.

### The Code

To display the map, you'll need to update your `index.html`, `style.css`, and `script.js` files as follows.

#### Update index.html

Open your `index.html` file. You need to include the Mapbox GL JS CSS and JavaScript libraries from their CDN and ensure there's a `<div>` element to contain the map.

{% code lineNumbers="true" %}
```html
<!-- index.html -->
<!DOCTYPE html>

<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>MapsIndoors</title>
    <link rel="stylesheet" href="style.css">
    <!-- Mapbox GL JS CSS -->
    <link href='https://api.mapbox.com/mapbox-gl-js/v3.10.0/mapbox-gl.css' rel='stylesheet' />
    <!-- MapsIndoors SDK (already included from initial setup) -->
    <script src="https://app.mapsindoors.com/mapsindoors/js/sdk/4.41.0/mapsindoors-4.41.0.js.gz"
            integrity="sha384-3lk3cwVPj5MpUyo5T605mB0PMHLLisIhNrSREQsQHjD9EXkHBjz9ETgopmTbfMDc"
            crossorigin="anonymous"></script>
    <!-- Mapbox GL JS -->
    <script src='https://api.mapbox.com/mapbox-gl-js/v3.10.0/mapbox-gl.js'></script>
</head>
<body>
    <!-- This div will hold your map -->
    <div id="map"></div>
    <script src="script.js"></script>
</body>
</html>
```
{% endcode %}

**Explanation of index.html updates:**

* The `<link href='https://api.mapbox.com/mapbox-gl-js/v3.10.0/mapbox-gl.css' rel='stylesheet' />` tag in the `<head>` includes the necessary CSS for Mapbox GL JS. This styles the map elements provided by Mapbox.
* The `<script src='https://api.mapbox.com/mapbox-gl-js/v3.10.0/mapbox-gl.js'></script>` tag in the `<head>` includes the Mapbox GL JS library. **Note**: We are using version `3.10.0` in this example; you should verify this is the currently recommended version or check the [Mapbox documentation](https://docs.mapbox.com/mapbox-gl-js/guides/) for the latest.
* The MapsIndoors SDK script (`mapsindoors-4.41.0.js.gz`) should already be present from the initial setup guide. You can always check the [MapsIndoors SDK reference documentation](https://app.mapsindoors.com/mapsindoors/js/sdk/latest/docs/index.html) for the latest SDK version.
* An empty `<div>` with the attribute `id="map"` was added inside the `<body>`. This `div` is crucial as it serves as the container where both the Mapbox base map and the MapsIndoors layers will be rendered.

#### Update style.css

Update your `style.css` file to ensure the `#map` element fills the available space. This is necessary for the map to be visible.

{% code lineNumbers="true" %}
```css
/* style.css */

/* Use flexbox for the main layout (from initial setup) */
html, body {
    height: 100%;
    margin: 0;
    padding: 0;
    overflow: hidden; /* Prevent scrollbars if map is full size */
    display: flex;
    flex-direction: column; /* Stack children vertically if needed later */
}

/* Style for the map container */
#map {
  /* Make map fill available space within its flex parent (body) */
  flex-grow: 1;
  width: 100%; /* Make map fill width */
  margin: 0;
  padding: 0;
}
```
{% endcode %}

**Explanation of style.css updates:**

* The styles for the `#map` container are added. These styles define how the map container behaves within the flexbox layout established in the initial setup (`html, body` styles).
* `flex-grow: 1;` is a key flexbox property that allows the map container to expand and fill the available vertical space within its parent (`body`).
* `width: 100%;` ensures the map fills the full horizontal width of its container.
* `margin: 0;` and `padding: 0;` remove any default browser spacing around the map container, ensuring it fits snugly.

#### Add JavaScript to script.js

Open your `script.js` file and add the following JavaScript code. This code will initialize the Mapbox map, then create the MapsIndoors view and instance, and finally add a Floor Selector.

Remember to replace `YOUR_MAPBOX_ACCESS_TOKEN` with your actual Mapbox access token if you are not using the demo one, and `YOUR_MAPSINDOORS_API_KEY` with your MapsIndoors API key if not using the demo key. For testing, the demo MapsIndoors API key `02c329e6777d431a88480a09` and the Austin office venue ID `dfea941bb3694e728df92d3d` are used.

{% code lineNumbers="true" %}
```javascript
// script.js

// Define options for the MapsIndoors Mapbox view
const mapViewOptions = {
    accessToken: 'YOUR_MAPBOX_ACCESS_TOKEN', // Replace with your Mapbox token
    element: document.getElementById('map'),
    center: { lng: -97.74204591828197, lat: 30.36022358949809 }, // Example: MapsPeople Austin Office
    zoom: 17,
    maxZoom: 22,
    mapsIndoorsTransitionLevel: 16,
};

// Set the MapsIndoors API key
mapsindoors.MapsIndoors.setMapsIndoorsApiKey('YOUR_MAPSINDOORS_API_KEY'); // Replace with your MapsIndoors API key

// Create a new instance of the MapsIndoors Mapbox view
const mapViewInstance = new mapsindoors.mapView.MapboxV3View(mapViewOptions);

// Create a new MapsIndoors instance, passing the map view
const mapsIndoorsInstance = new mapsindoors.MapsIndoors({
    mapView: mapViewInstance,
    // Set the venue ID to load the map for a specific venue
    venue: 'YOUR_MAPSINDOORS_VENUE_ID', // Replace with your actual venue ID
});

/** Floor Selector **/

// Create a new HTML div element to host the floor selector
const floorSelectorElement = document.createElement('div');

// Create a new FloorSelector instance, linking it to the HTML element and the main MapsIndoors instance.
new mapsindoors.FloorSelector(floorSelectorElement, mapsIndoorsInstance);

// Get the underlying Mapbox map instance
const mapboxInstance = mapViewInstance.getMap();

// Add the floor selector HTML element to the Mapbox map using Mapbox's addControl method
// We wrap the element in an object implementing the IControl interface expected by addControl
mapboxInstance.addControl({
    onAdd: function () {
        // This function is called when the control is added to the map.
        // It should return the control's DOM element.
        return floorSelectorElement;
    },
    onRemove: function () {
        // This function is called when the control is removed from the map.
        // Clean up any event listeners or resources here.
        floorSelectorElement.parentNode.removeChild(floorSelectorElement);
    },
}, 'top-right'); // Optional: Specify a position ('top-left', 'top-right', 'bottom-left', 'bottom-right')

/** Handle Location Clicks **/

// Handle Location Clicks on Map
function handleLocationClick(location) {
    if (location && location.id) {
        mapsIndoorsInstance.goTo(location); // Center the map on the clicked location
    }
}

// Add an event listener to the MapsIndoors instance for click events on locations
mapsIndoorsInstance.on('click', handleLocationClick);
```
{% endcode %}

**Explanation of script.js updates:**

* `const mapViewOptions = { ... }`: This object defines essential configuration options for the MapsIndoors Mapbox view.
  * `accessToken`: Your Mapbox access token, required by Mapbox GL JS.
  * `element`: The HTML DOM element (our `<div id="map">`) where the map will be rendered.
  * `center`, `zoom`, `maxZoom`: Standard map parameters to set the initial view.
  * `mapsIndoorsTransitionLevel`: The zoom level at which MapsIndoors will start showing indoor details.
  * For more details on all available options, see the [mapsindoors.mapView.MapboxV3View class documentation](https://app.mapsindoors.com/mapsindoors/js/sdk/latest/docs/mapsindoors.mapView.MapboxV3View.html).
* `mapsindoors.MapsIndoors.setMapsIndoorsApiKey('YOUR_MAPSINDOORS_API_KEY');`: This static method sets your MapsIndoors API key globally for the SDK. This key authenticates your requests to MapsIndoors services. See the [`mapsindoors.MapsIndoors` class reference](https://app.mapsindoors.com/mapsindoors/js/sdk/latest/docs/MapsIndoors.html).
* `const mapViewInstance = new mapsindoors.mapView.MapboxV3View(mapViewOptions);`: This line creates an instance of `MapboxV3View`, which is responsible for integrating MapsIndoors data and rendering with a Mapbox GL JS v3 map. For more details on `MapboxV3View`, see its [class reference](https://app.mapsindoors.com/mapsindoors/js/sdk/latest/docs/mapsindoors.mapView.MapboxV3View.html).
* `const mapsIndoorsInstance = new mapsindoors.MapsIndoors({ mapView: mapViewInstance, venue: 'YOUR_MAPSINDOORS_VENUE_ID' });`: This creates the main `MapsIndoors` instance. This object is your primary interface for interacting with MapsIndoors functionalities like displaying locations, getting directions, etc. See the [`mapsindoors.MapsIndoors` class documentation](https://app.mapsindoors.com/mapsindoors/js/sdk/latest/docs/MapsIndoors.html).
* **Floor Selector Integration:**
  * `const floorSelectorElement = document.createElement('div');`: A new HTML `div` element is dynamically created. This element will serve as the container for the Floor Selector UI.
  * `new mapsindoors.FloorSelector(floorSelectorElement, mapsIndoorsInstance);`: This instantiates the `FloorSelector` control. It takes the HTML element to render into and the `mapsIndoorsInstance` to interact with (e.g., to know available floors and change the current floor). For more details on `FloorSelector`, see its [class reference](https://app.mapsindoors.com/mapsindoors/js/sdk/latest/docs/mapsindoors.FloorSelector.html).
  * `const mapboxInstance = mapViewInstance.getMap();`: The `getMap()` method on our `mapViewInstance` returns the underlying native Mapbox `Map` object. This is necessary to use Mapbox-specific functionalities.
  * `mapboxInstance.addControl({ ... }, 'top-right');`: This uses the native Mapbox `addControl` method to add our `floorSelectorElement` to the map UI in the top-right corner. For more details, refer to the [Mapbox GL JS documentation on controls](https://docs.mapbox.com/mapbox-gl-js/api/markers/#icontrol).
* **Click-to-Center Functionality:**
  * `function handleLocationClick(location) { ... }`: This function is called whenever a location (POI) on the map is clicked. It checks that the clicked object is a valid MapsIndoors location and then calls `mapsIndoorsInstance.goTo(location)` to center the map on that location.
  * `mapsIndoorsInstance.on('click', handleLocationClick);`: This line registers the click handler so that clicking any POI on the map will smoothly center the map on that location. This pattern will be reused and expanded in later steps.

### Expected Outcome

After completing these steps and opening your `index.html` file in a web browser, you should see:

* An interactive map centered on the MapsPeople Austin Office.
* A Floor Selector control visible in the top-right corner of the map, allowing you to switch between different floors of the venue.
* Clicking on any POI or location marker on the map will center the map on that location.

### Troubleshooting

* **Map doesn't load / blank page:**
  * Check your browser's developer console (usually F12) for any error messages.
  * Ensure you have replaced `YOUR_MAPBOX_ACCESS_TOKEN` with a valid token in `script.js` (or are using the provided demo token correctly).
  * Verify that `YOUR_MAPSINDOORS_API_KEY` is correctly set (or the demo key is used).
  * Double-check all CDN links in `index.html` are correct and accessible.
* **Floor selector doesn't appear:**
  * Verify the JavaScript code for creating and adding the floor selector has no typos.
  * Check the console for errors related to `FloorSelector` or `addControl`.
* **Clicking a location does not center the map:**
  * Ensure the `handleLocationClick` function is correctly defined and registered as an event listener.
  * Check for any JavaScript errors in the console that might indicate issues with the click handling code.

### Here's a CodeSandbox demonstrating the result you should have by now:

{% embed url="https://codesandbox.io/p/sandbox/github/MapsPeople/mapsindoors-getting-started-for-web/tree/main/sdk/mapbox/1-display-a-map" %}

### Next Steps

You have successfully displayed a MapsIndoors map with Mapbox GL JS and added a floor selector! This is the foundational step for building more complex indoor mapping applications.

Next, you will learn how to add search functionality to your map:

* Step 2: [Create a Search Experience](create-a-search-experience.md)
