# Show the Details

**Goal:** This guide demonstrates how to display detailed information about a location when a user selects it from the search results or clicks a POI on the map. The details will include the location's name and description, and the map will navigate to and highlight the selected location.

**SDK Classes and Methods Introduced:**

* Retrieving and displaying specific location properties (e.g., `location.properties.name`, `location.properties.description`) within a dedicated details panel.
* Applying `mapsIndoorsInstance.deselectLocation()` to manage map state when closing the details view.

**Implementation Patterns:**

* Expanding the unified click handler to show location details
* Implementing UI state management between search and details views
* Creating a responsive details panel UI
* Handling transitions between different UI states

### Prerequisites

* Completion of [Step 2: Create a Search Experience](create-a-search-experience.md).
* Your MapsIndoors API Key and Mapbox Access Token should be correctly set up. We will continue using the demo API key `02c329e6777d431a88480a09` and venue ID `dfea941bb3694e728df92d3d` for this example.

### The Code

This step involves modifications to `index.html` to add elements for displaying details, `style.css` to style these new elements, and `script.js` to handle the logic of fetching and displaying location details and interacting with the map.

#### Unified Click Handler Pattern

In Step 1, we introduced a reusable click handler (`handleLocationClick`) for POIs on the map. In Step 2, we reused this handler for search result clicks. In this step, we expand the handler to show a details panel with more information about the selected location, and manage the UI state.

#### Update index.html

Open your `index.html` file. We will add a new `div` within the existing `.panel` to hold the location details. This `div` will be hidden by default and shown when a location is selected.

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
    <link href='https://api.mapbox.com/mapbox-gl-js/v3.10.0/mapbox-gl.css' rel='stylesheet' />
    <script src="https://app.mapsindoors.com/mapsindoors/js/sdk/4.41.0/mapsindoors-4.41.0.js.gz"
            integrity="sha384-tFHttWqE6qOoX8etJurRBBXpH6puWNTgC8Ilq477ltu4EcpHk9ZwFPJDIli9wAS7"
            crossorigin="anonymous"></script>
    <script src='https://api.mapbox.com/mapbox-gl-js/v3.10.0/mapbox-gl.js'></script>
</head>

<body>
    <div id="map"></div>

    <div class="panel">
        <!-- Search UI elements from Step 2 -->
        <div id="search-ui" class="flex-column"> <!-- Wrap search elements -->
            <input type="text" id="search-input" placeholder="Search for a location...">
            <ul id="search-results"></ul>
        </div>

        <!-- New Details UI - initially hidden -->
        <div id="details-ui" class="hidden flex-column">
            <h3 id="details-name"></h3>
            <p id="details-description"></p>
            <button id="details-close" class="details-button">Close</button>
        </div>
    </div>

    <script src="script.js"></script>
</body>

</html>
```
{% endcode %}

**Explanation of index.html updates:**

* The existing search input and results list are wrapped in a `div` with `id="search-ui"` and class `flex-column`. This helps in managing the visibility of the entire search section.
* A new `div` with `id="details-ui"` is added within the `.panel`. This container will hold the location's name, description, and a close button.
  * It has the class `hidden` to be invisible by default.
  * It also has `flex-column` for layout consistency.
* Inside `#details-ui`:
  * `<h3 id="details-name"></h3>`: For displaying the location name.
  * `<p id="details-description"></p>`: For displaying the location description.
  * `<button id="details-close" class="details-button">Close</button>`: A button to close the details view and return to the search results.

#### Update style.css

Modify your `style.css` file to add styles for the new location details UI elements.

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
    position: absolute; /* Position over the map */
    top: 10px; /* Distance from the top */
    left: 10px; /* Distance from the left */
    z-index: 10; /* Ensure it's above the map and other elements */
    background-color: white; /* White background for readability */
    padding: 10px;
    border-radius: 5px;
    box-shadow: 0 2px 5px rgba(0,0,0,0.2); /* Add a subtle shadow */
    max-height: 80%; /* Limits max-height to prevent overflow with details */
    overflow-y: auto; /* Add scroll if content exceeds max-height */
    border: 1px solid #ccc; /* Add border for clarity */
    width: 300px;
}

/* Class to apply flex display and column direction */
.flex-column {
    display: flex;
    flex-direction: column;
    gap: 10px; /* Space between elements */
}

/* Class to hide elements */
.hidden {
    display: none;
}

/* Style for the search input field */
#search-input {
    padding: 8px;
    border: 1px solid #ccc;
    border-radius: 4px;
    font-size: 1rem;
}

/* Style for the search results list */
#search-results {
    list-style: none; /* Remove default list bullets */
    padding: 0;
    margin: 0;
}

/* Style for individual search result items */
#search-results li {
    padding: 8px 0;
    cursor: pointer; /* Indicate clickable items */
    border-bottom: 1px solid #eee; /* Separator line */
}

/* Style for the last search result item (no bottom border) */
#search-results li:last-child {
    border-bottom: none;
}

/* Hover effect for search result items */
#search-results li:hover {
    background-color: #f0f0f0; /* Highlight on hover */
}

/* --- New Styles for Location Details UI elements --- */

/* Styles for the new UI wrappers within #search-container */
#search-ui,
#details-ui {
    width: 100%; /* Ensure they fill the container's width */
    /* display: flex and flex-direction are controlled by .flex-column-container class via JS */
}

/* Style for the location name in details */
#details-name {
    margin-top: 0;
    margin-bottom: 10px;
    font-size: 1.2rem;
    border-bottom: 1px solid #eee;
    padding-bottom: 5px;
}

/* Style for the location description in details */
#details-description {
    margin-bottom: 15px;
    font-size: 0.9rem;
    color: #555;
}

/* Style for general buttons within details */
.details-button {
     padding: 8px;
     border: none;
     border-radius: 4px;
     font-size: 0.9rem;
     cursor: pointer;
     margin-bottom: 8px; /* Space between buttons */
     transition: background-color 0.3s ease;
}

 .details-button:last-child {
     margin-bottom: 0;
 }

/* Specific style for the Close button */
#details-close {
    background-color: #ccc; /* Grey */
    color: #333;
}
#details-close:hover {
 background-color: #bbb;
}
```
{% endcode %}

**Explanation of style.css updates:**

* `#search-ui`, `#details-ui`: Basic styling to ensure they take full width. The `flex-column` class will handle their internal layout.
* `#details-name`: Styles for the location name heading (font size, margin, border).
* `#details-description`: Styles for the description text (font size, color, `white-space: pre-wrap` to respect newlines from the data, `max-height` and `overflow-y` for scrollability).
* `.details-button`: General styling for buttons in the details view (padding, border-radius, full width).
* `#details-close`: Specific styling for the "Close" button (background color, text color, hover effect).

#### Update script.js

The `script.js` file will be updated to handle showing/hiding the details panel, populating it with location data, and interacting with the map.

{% code lineNumbers="true" %}
```javascript
// script.js

// Define options for the MapsIndoors Mapbox view
const mapViewOptions = {
    accessToken: 'YOUR_MAPBOX_ACCESS_TOKEN', // Replace with your Mapbox token
    element: document.getElementById('map'),
    // Initial map center (MapsPeople - Austin Office example)
    center: { lng: -97.74204591828197, lat: 30.36022358949809 },
    // Initial zoom level
    zoom: 17,
    // Maximum zoom level
    maxZoom: 22,
    // The zoom level at which MapsIndoors transitions
    mapsIndoorsTransitionLevel: 16
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

// Function to handle clicks on MapsIndoors locations
function handleLocationClick(location) {
    if (location && location.id) {
        // Move the map to the selected location
        mapsIndoorsInstance.goTo(location);
        // Ensure that the map shows the correct floor
        mapsIndoorsInstance.setFloor(location.properties.floor);
        // Select the location on the map
        mapsIndoorsInstance.selectLocation(location);

        // Show the details UI for the clicked location
        showDetails(location);
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

/** UI state management **/

const searchUIElement = document.getElementById('search-ui');
const detailsUIElement = document.getElementById('details-ui');

function showSearchUI() {
    hideDetailsUI();
    searchUIElement.classList.remove('hidden');
    searchInputElement.focus();
}

function showDetailsUI() {
    hideSearchUI();
    detailsUIElement.classList.remove('hidden');
}

function hideSearchUI() {
    searchUIElement.classList.add('hidden');
}

function hideDetailsUI() {
    detailsUIElement.classList.add('hidden');
}

/** Location Details **/

// Get references to the static details view elements
const detailsNameElement = document.getElementById('details-name');
const detailsDescriptionElement = document.getElementById('details-description');
const detailsCloseButton = document.getElementById('details-close');

detailsCloseButton.addEventListener('click', () => {
    mapsIndoorsInstance.deselectLocation(); // Deselect any selected location
    showSearchUI();
});

// Show the details of a location
function showDetails(location) {
    detailsNameElement.textContent = location.properties.name;
    detailsDescriptionElement.textContent = location.properties.description || 'No description available.';
    showDetailsUI();
}

// Initial call to set up the search UI when the page loads
showSearchUI();
```
{% endcode %}

**Explanation of script.js updates:**

* **Unified Click Handler Implementation**:
  * Building upon our approach in previous steps, we've implemented a comprehensive click handler system.
  * The `handleLocationClick` function manages map interactions (selecting a location, zooming, and changing floors).
  * This function then calls the `showDetails` function to update the UI presentation.
  * This separation of concerns makes the code more maintainable and easier to understand.
* **UI State Management Functions**:
  * `showSearchUI()`: Shows the search interface and hides the details panel.
  * `showDetailsUI()`: Shows the details interface and hides the search panel.
  * `hideSearchUI()` and `hideDetailsUI()`: Helper functions to manage UI visibility.
  * `showDetails(location)`: Updates the details UI with the location's name and description.
  * The close button in the details panel has an event listener that returns to the search UI.
* **Map Interaction Methods**:
  * `mapsIndoorsInstance.goTo(location)`: Pans and zooms the map to the selected location.
  * `mapsIndoorsInstance.setFloor(location.properties.floor)`: Switches to the floor where the location exists.
  * `mapsIndoorsInstance.selectLocation(location)`: Visually highlights the selected location on the map.
* **Location Properties Access**:
  * We access `location.properties.name` and `location.properties.description` to display detailed information.
  * The fallback text "No description available" is shown when description is missing.
* **Search Result Click Handler**:
  * Search result clicks now trigger the same handler as map POI clicks, providing a consistent experience.
  * This unified approach ensures the same behavior whether a user interacts with the map or search results.
  * The separation between `handleLocationClick` (for map operations) and `showDetails` (for UI updates) creates a cleaner architecture that's easier to maintain and extend.
* **Initial Setup**:
  * `showSearchUI()` is called at the end to ensure the application starts with the search interface visible.

### Expected Outcome

After implementing these changes:

* When you search for locations and click on a result in the list:
  * The search input and results list will be hidden.
  * A details panel will appear showing the selected location's name and description.
  * The map will pan and zoom to the selected location.
  * The selected location will be highlighted on the map.
  * The map will switch to the correct floor of the selected location.
* Clicking the "Close" button in the details panel will hide the details and show the search input and results list again.
* When you click a POI on the map:
  * The details panel will appear showing the location's name and description.
  * The map will pan and zoom to the selected location.
  * The selected location will be highlighted on the map.
  * The map will switch to the correct floor of the selected location.

### Troubleshooting

* **Details panel doesn't show or shows incorrect data:**
  * Check browser console for errors.
  * Verify IDs in `index.html` match those used in `script.js` for `getElementById`.
  * Ensure `location.properties.name` and `location.properties.description` exist for the selected location or that default text is handled.
  * Confirm CSS for `.hidden`, `#search-ui`, and `#details-ui` is correctly applied and toggled.
* **Map doesn't navigate or select location:**
  * Ensure `mapsIndoorsInstance` is correctly initialized.
  * Verify the `location` object passed to `handleLocationClick` is a valid object conforming to the `Location` interface.
  * Check for console errors when `selectLocation`, `goTo`, or `setFloor` are called.
* **"Close" button doesn't work:**
  * Ensure the event listener is correctly attached to `detailsCloseButton` and that `showSearchUI` is called.

{% embed url="https://codesandbox.io/p/sandbox/github/MapsPeople/mapsindoors-getting-started-for-web/tree/main/sdk/mapbox/3-show-the-details" %}

### Next Steps

You've now enhanced your application to display detailed information about locations using a unified click handler. Next, you'll learn how to get and display directions between locations:

* Step 4: [Getting Directions](getting-directions.md)
