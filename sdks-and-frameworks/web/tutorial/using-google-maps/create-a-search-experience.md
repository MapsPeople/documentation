# Create a Search Experience

**Goal:** This guide will show you how to add a search input field to your map application, allowing users to find locations within your venue. Search results will be displayed in a list and highlighted on the map.

**SDK Concepts Introduced:**

* Using `mapsindoors.services.LocationsService.getLocations()` to fetch locations based on a query.
* Interacting with the map based on search results:
  * Highlighting multiple locations: `mapsIndoorsInstance.highlight()`.
  * Setting the floor: `mapsIndoorsInstance.setFloor()`.
  * Selecting a specific location: `mapsIndoorsInstance.selectLocation()`.
* Clearing previous highlights and selections: `mapsIndoorsInstance.highlight()` (with no arguments) and `mapsIndoorsInstance.deselectLocation()`.
* Getting current venue information: `mapsIndoorsInstance.getVenue()`.

### Prerequisites

* Completion of [Step 1: Display a Map](display-a-map.md).
* Your MapsIndoors API Key and Google Maps JavaScript API Key should be correctly set up as per Step 1. We will continue using the demo API key `02c329e6777d431a88480a09` and venue ID `dfea941bb3694e728df92d3d` for this example.

### The Code

This guide details the modifications to HTML, CSS, and JavaScript needed to add a search input field, display search results, and dynamically interact with the map based on user searches.

#### Update index.html

Open your `index.html` file. The primary structural change to your HTML is the introduction of a dedicated search panel. This panel will house the input field where users type their search queries and an unordered list where the corresponding search results will appear.

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
    <script src="https://maps.googleapis.com/maps/api/js?libraries=geometry&key=YOUR_GOOGLE_MAPS_API_KEY"></script>
    <script src="https://app.mapsindoors.com/mapsindoors/js/sdk/4.41.0/mapsindoors-4.41.0.js.gz"
            integrity="sha384-3lk3cwVPj5MpUyo5T605mB0PMHLLisIhNrSREQsQHjD9EXkHBjz9ETgopmTbfMDc"
            crossorigin="anonymous"></script>
</head>

<body>
    <div id="map"></div>

    <!-- New search panel for user interaction -->
    <div class="panel">
        <div id="search-ui" class="flex-column">
            <!-- Input field for users to type search queries -->
            <input type="text" id="search-input" placeholder="Search for a location...">
            <!-- Unordered list to display search results dynamically -->
            <ul id="search-results"></ul>
        </div>
    </div>

    <script src="script.js"></script>
</body>

</html>
```
{% endcode %}

**Explanation of index.html updates:**

* A new `div` element with the class `panel` is added. This `div` serves as the main container for all search-related UI elements.
* Inside the `panel`, another `div` with the id `search-ui` and the class `flex-column` is introduced to arrange the search input and results list vertically.
* An `<input>` element with `id="search-input"` is the text field for user search queries.
* An `<ul>` (unordered list) element with `id="search-results"` will be dynamically populated with locations matching the user's query.

### Update style.css

Modify your `style.css` file to incorporate styles for the newly added search panel and its contents. These styles are crucial for ensuring the search interface is user-friendly, visually appealing, and correctly positioned over the map without obstructing it entirely.

{% code lineNumbers="true" %}
```css
/* style.css */

/* Use flexbox for the main layout */
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
  /* Make map fill available space */
  flex-grow: 1;
  width: 100%; /* Make map fill width */
  margin: 0;
  padding: 0;
}


/* Style for the information panel container */
.panel {
    position: absolute; /* Positions the panel relative to the nearest positioned ancestor (or body if none). This allows it to float over the map. */
    top: 10px; /* Adds a 10px margin from the top edge of its containing element. */
    left: 10px; /* Adds a 10px margin from the left edge of its containing element, placing it in the top-left corner. */
    z-index: 10; /* Ensures the panel appears on top of other elements, like the map itself, which might have lower z-index values. */
    background-color: white; /* Sets a solid white background for the panel, making text readable. */
    padding: 10px; /* Adds 10px of space inside the panel, around its content. */
    border-radius: 5px; /* Rounds the corners of the panel for a softer look. */
    box-shadow: 0 2px 5px rgba(0,0,0,0.2); /* Adds a subtle shadow effect, giving the panel a sense of depth. */
    max-height: 80%; /* Limits max-height to prevent overflow with details */
    overflow-y: auto; /* Adds a vertical scrollbar if the content inside the panel exceeds its max-height. */
    border: 1px solid #ccc; /* Adds a light gray border around the panel for better visual separation. */
    width: 300px; /* Sets a fixed width for the panel. */
}

/* Class to apply flex display and column direction */
.flex-column {
    display: flex; /* Enables flexbox layout for this element. */
    flex-direction: column; /* Arranges child elements in a vertical column. */
    gap: 10px; /* Adds a 10px gap between child elements within the flex container. */
}

/* Class to hide elements */
.hidden {
    display: none; /* Makes elements with this class invisible and removes them from the layout. */
}

/* Style for the search input field */
#search-input {
    padding: 8px; /* Adds 8px of internal padding to the input field for better text spacing. */
    border: 1px solid #ccc; /* Adds a light gray border around the input field. */
    border-radius: 4px; /* Slightly rounds the corners of the input field. */
    font-size: 1rem; /* Sets the font size within the input field to a standard readable size. */
}

/* Style for the search results list */
#search-results {
    list-style: none; /* Removes the default bullet points from the unordered list. */
    padding: 0; /* Removes default padding from the list. */
    margin: 0; /* Removes default margins from the list. */
}

/* Style for individual search result items */
#search-results li {
    padding: 8px 0; /* Adds vertical padding to each list item for spacing. */
    cursor: pointer; /* Changes the mouse cursor to a pointer on hover, indicating the item is clickable. */
    border-bottom: 1px solid #eee; /* Adds a light gray line below each list item, acting as a separator. */
}

/* Style for the last search result item (no bottom border) */
#search-results li:last-child {
    border-bottom: none; /* Removes the bottom border from the last item in the list for a cleaner look. */
}

/* Hover effect for search result items */
#search-results li:hover {
    background-color: #f0f0f0; /* Changes the background color of a list item when hovered, providing visual feedback. */
}
```
{% endcode %}

**Explanation of style.css updates:**

* **`.panel`**: Styles the search panel to float over the map in the top-left corner, with a white background, padding, rounded corners, and a shadow for a modern look. `max-height` and `overflow-y: auto` ensure it's scrollable if results are numerous.
* **`.flex-column`**: A utility class using Flexbox to stack child elements (search input, results list) vertically with a small gap.
* **`.hidden`**: A utility class to hide elements (`display: none;`), used initially for the search results list.
* **`#search-input`**: Styles the search input field with padding, border, and font size for readability.
* **`#search-results`**: Styles the unordered list for search results, removing default list styling.
* **`#search-results li`**: Styles individual list items with padding, a bottom border for separation, and a pointer cursor to indicate clickability.
* **`#search-results li:last-child`**: Removes the bottom border from the last list item.
* **`#search-results li:hover`**: Provides a hover effect for list items for better user feedback.

### Update script.js

The `script.js` file sees the most significant changes as it houses the logic for the search functionality.

{% code lineNumbers="true" %}
```javascript
// script.js

// Define options for the MapsIndoors Google Maps view
const mapViewOptions = {
    element: document.getElementById('map'),
    center: { lng: -97.74204591828197, lat: 30.36022358949809 },
    zoom: 17,
    maxZoom: 22,
};

// Set the MapsIndoors API key
mapsindoors.MapsIndoors.setMapsIndoorsApiKey('YOUR_MAPSINDOORS_API_KEY'); // Replace with your MapsIndoors API key

// Create a new instance of the MapsIndoors Google Maps view
const mapViewInstance = new mapsindoors.mapView.GoogleMapsView(mapViewOptions);

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

// Get the underlying Google Maps instance
const googleMapInstance = mapViewInstance.getMap();

// Add the floor selector HTML element to the Google Maps controls.
googleMapInstance.controls[google.maps.ControlPosition.TOP_RIGHT].push(floorSelectorElement);

/** Handle Location Clicks **/

// Function to handle clicks on MapsIndoors locations
function handleLocationClick(location) {
    if (location && location.id) {
        // Move the map to the selected location
        mapsIndoorsInstance.goTo(location);
        // Ensure that the map shows the correct floor
        mapsIndoorsInstance.setFloor(location.properties.floor);
        // Select the location on the map
        mapsIndoorsInstance.selectLocation(location);
    }
}

// Add an event listener to the MapsIndoors instance for click events on locations
mapsIndoorsInstance.on('click', handleLocationClick);

/** Search Functionality **/

// Get references to the search input and results list elements
const searchInputElement = document.getElementById('search-input');
const searchResultsElement = document.getElementById('search-results');

// Initially hide the search results list
searchResultsElement.classList.add('hidden');

// Add an event listener to the search input for 'input' events
// This calls the onSearch function every time the user types in the input field
searchInputElement.addEventListener('input', onSearch);

// Function to perform the search and update the results list and map highlighting
function onSearch() {
    // Get the current value from the search input
    const query = searchInputElement.value;
    // Get the current venue from the MapsIndoors instance
    const currentVenue = mapsIndoorsInstance.getVenue();

    // Clear map highlighting
    mapsIndoorsInstance.highlight();
    // Deselect any selected location
    mapsIndoorsInstance.deselectLocation();

    // Check if the query is too short (less than 3 characters) or empty
    if (query.length < 3) {
        // Hide the results list if the query is too short or empty
        searchResultsElement.classList.add('hidden');
        return; // Stop here
    }

    // Define search parameters with the current input value
    // Include the current venue name in the search parameters
    const searchParameters = { q: query, venue: currentVenue ? currentVenue.name : undefined };

    // Call the MapsIndoors LocationsService to get locations based on the search query
    mapsindoors.services.LocationsService.getLocations(searchParameters).then(locations => {
        // Clear previous search results
        searchResultsElement.innerHTML = null;

        // If no locations are found, display a "No results found" message
        if (locations.length === 0) {
            const noResultsItem = document.createElement('li');
            noResultsItem.textContent = 'No results found';
            searchResultsElement.appendChild(noResultsItem);
            // Ensure the results list is visible to show the "No results found" message
            searchResultsElement.classList.remove('hidden');
            return; // Stop here if no results
        }

        // Append new search results to the list
        locations.forEach(location => {
            const listElement = document.createElement('li');
            // Display the location name
            listElement.innerHTML = location.properties.name;
            // Store the location ID on the list item for easy access
            listElement.dataset.locationId = location.id;

            // Add a click event listener to each list item
            listElement.addEventListener('click', function () {
                // Call the handleLocationClick function when a location in the search results is clicked.
                handleLocationClick(location);
            });

            searchResultsElement.appendChild(listElement);
        });

        // Show the results list now that it has content
        searchResultsElement.classList.remove('hidden');

        // Filter map to only display search results by highlighting them
        mapsIndoorsInstance.highlight(locations.map(location => location.id));
    })
        .catch(error => {
            console.error("Error fetching locations:", error);
            const errorItem = document.createElement('li');
            errorItem.textContent = 'Error performing search.';
            searchResultsElement.appendChild(errorItem);
            searchResultsElement.classList.remove('hidden');
        });
}
```
{% endcode %}

**Explanation of script.js updates:**

* **DOM Element References**: `searchInputElement` and `searchResultsElement` get references to the HTML input field and the unordered list for displaying results, respectively.
* **Initial State**: The `searchResultsElement` is hidden by default using the `.hidden` CSS class.
* **Event Listener**: An `input` event listener is attached to `searchInputElement`. This calls the `onSearch` function each time the user types into the search field.
* **`onSearch()` Function**: This is the core of the search logic:
  * It retrieves the current `query` from the input field and gets the `currentVenue` using `mapsIndoorsInstance.getVenue()`. For more details on venue information, see the [`getVenue()` reference](https://app.mapsindoors.com/mapsindoors/js/sdk/latest/docs/MapsIndoors.html#getVenue).
  * It clears any existing highlights from the map using `mapsIndoorsInstance.highlight()` (called without arguments). See its [API documentation](https://app.mapsindoors.com/mapsindoors/js/sdk/latest/docs/MapsIndoors.html#highlight) for more on clearing highlights.
  * It deselects any currently selected location using `mapsIndoorsInstance.deselectLocation()`. Refer to its [API documentation](https://app.mapsindoors.com/mapsindoors/js/sdk/latest/docs/MapsIndoors.html#deselectLocation) for deselection behavior.
  * If the `query` length is less than 3 characters, it hides the `searchResultsElement` and exits to prevent overly broad or empty searches.
  * It prepares `searchParameters` with the `q` (query) and scopes the search to the `currentVenue.name`. For a comprehensive list of search options, check out the [LocationsService.getLocations() documentation](https://app.mapsindoors.com/mapsindoors/js/sdk/latest/docs/mapsindoors.services.LocationsService.html#.getLocations)
  * It calls `mapsindoors.services.LocationsService.getLocations(searchParameters)` to fetch locations. This asynchronous method returns a Promise.
  * **`.then(locations => { ... })`**: This block handles the successful response from the LocationsService. `locations` is an array of objects, where each object conforms to the [Location interface](https://app.mapsindoors.com/mapsindoors/js/sdk/latest/docs/mapsindoors.Location.html).
    * It clears previous search results by setting `searchResultsElement.innerHTML = null`.
    * If no `locations` are found, it displays a "No results found" message in the list.
    * Otherwise, it iterates through each `location` object:
      * Creates an `<li>` element.
      * Sets its `innerHTML` to `location.properties.name`. The `properties` object on an object conforming to the `Location` interface contains various details about the location. For more information, see the [Location documentation](https://app.mapsindoors.com/mapsindoors/js/sdk/latest/docs/mapsindoors.Location.html).
      * Stores `location.id` in `listElement.dataset.locationId` for potential future use.
      * Adds a `click` event listener to the list item. When clicked:
        * `mapsIndoorsInstance.goTo(location)`: Pans and zooms the map to the clicked location. For more details on `goTo()`, see its [reference](https://app.mapsindoors.com/mapsindoors/js/sdk/latest/docs/MapsIndoors.html#goTo).
        * `mapsIndoorsInstance.setFloor(location.properties.floor)`: Changes the map to the location's floor. To understand floor management, check the [`setFloor()` documentation](https://app.mapsindoors.com/mapsindoors/js/sdk/latest/docs/MapsIndoors.html#setFloor).
        * `mapsIndoorsInstance.selectLocation(location)`: Selects and highlights this specific location on the map. For further information, refer to the [`selectLocation()` API documentation](https://app.mapsindoors.com/mapsindoors/js/sdk/latest/docs/MapsIndoors.html#selectLocation).
      * Appends the new list item to `searchResultsElement`.
      * Collects all `location.id`s into `locationIdsToHighlight`.
    * Makes the `searchResultsElement` visible by removing the `.hidden` class.
    * Calls `mapsIndoorsInstance.highlight(locationIdsToHighlight)` to highlight all found locations on the map simultaneously. The `highlight()` method accepts an array of location IDs. See its [API documentation](https://app.mapsindoors.com/mapsindoors/js/sdk/latest/docs/MapsIndoors.html#highlight) for details on batch highlighting.
  * **`.catch(error => { ... })`**: Handles potential errors during the search request, logging them to the console and displaying an error message in the list.
* The `handleLocationClick` function is now used for both map clicks and search result clicks, ensuring consistent behavior and code reuse.
* When a user clicks a search result or a POI on the map, the map will center on that location, switch to the correct floor, and select the location.
* This pattern will be reused and expanded in later steps.

### Expected Outcome

After implementing these changes:

* A search panel will be visible in the top-left corner of your map.
* Typing 3 or more characters into the search input will trigger a search.
* Matching locations will appear as a clickable list below the search input.
* All matching locations will be highlighted on the map.
* Clicking a location in the list will:
  * Pan and zoom the map to that location.
  * Switch to the correct floor if necessary.
  * Select and distinctively highlight that specific location on the map.

### Troubleshooting

* **Search not working / No results:**
  * Check the browser's developer console (F12) for errors.
  * Ensure your MapsIndoors API Key (`02c329e6777d431a88480a09` for demo) is correct and the MapsIndoors SDK is loaded.
  * Verify `YOUR_GOOGLE_MAPS_API_KEY` is correct.
  * Confirm the `venue` ID (`dfea941bb3694e728df92d3d` for demo) is valid and the venue has searchable locations.
  * Make sure the `onSearch` function is being called (e.g., add a `console.log` at the beginning of `onSearch`).
* **Results list doesn't appear or looks wrong:**
  * Check CSS for the `.panel`, `#search-results`, and `li` elements. Ensure the `.hidden` class is being correctly added/removed.
* **Map interactions (goTo, highlight, selectLocation) not working:**
  * Verify `mapsIndoorsInstance` is correctly initialized.
  * Ensure the `location` objects passed to these methods are valid objects conforming to the `Location` interface.
  * Check for console errors when clicking a search result.

{% embed url="https://codesandbox.io/p/sandbox/github/MapsPeople/mapsindoors-getting-started-for-web/tree/main/sdk/google.maps/2-create-a-search-experience" %}

### Next Steps

You've now successfully added a powerful search feature to your indoor map! Users can easily find their way to specific points of interest.

Next, learn how to display detailed information about a selected location:

* Step 3: [Show the Details](show-the-details.md)
