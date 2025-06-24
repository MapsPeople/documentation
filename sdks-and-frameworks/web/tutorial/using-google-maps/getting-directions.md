# Getting Directions

**Goal:** This guide will show you how to add directions functionality to your application. Users will be able to select an origin and destination, get a route between them, and step through the directions on the map. This step builds on the search and details UI from Step 3, introducing a new directions panel and integrating the MapsIndoors DirectionsService and DirectionsRenderer.

**SDK Concepts Introduced:**

* Using the [DirectionsService](https://app.mapsindoors.com/mapsindoors/js/sdk/latest/docs/mapsindoors.services.DirectionsService.html) to calculate routes between locations.
* Using the [DirectionsRenderer](https://app.mapsindoors.com/mapsindoors/js/sdk/latest/docs/mapsindoors.directions.DirectionsRenderer.html) to display and step through routes on the map.
* Using `directionsRenderer.setRoute()` to display a calculated route on the map.
* Using `directionsRenderer.setStepIndex()` to navigate to a specific step in the route.
* Using `directionsRenderer.nextStep()` and `directionsRenderer.previousStep()` for step navigation.
* Using `directionsRenderer.getLegIndex()` and `directionsRenderer.getStepIndex()` to track current position.

### Prerequisites

* Completion of [Step 3: Show the Details](show-the-details.md). Your app should already support searching for locations and viewing details.
* Your MapsIndoors API Key and Google Maps API Key should be correctly set up. We will continue using the demo API key `02c329e6777d431a88480a09` and venue ID `dfea941bb36964e728df92d3d` for this example.

### Update index.html

Open your `index.html` file. Add a new directions panel inside the existing `.panel` container:

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
    <!-- Google Maps JavaScript API -->
    <script src="https://maps.googleapis.com/maps/api/js?key=YOUR_GOOGLE_MAPS_API_KEY"></script>
    <script src="https://app.mapsindoors.com/mapsindoors/js/sdk/4.41.0/mapsindoors-4.41.0.js.gz"
            xintegrity="sha384-3lk3cwVPj5MpUyo5T605mB0PMHLLisIhNrSREQsQHjD9EXkHBjz9ETgopmTbfMDc"
            crossorigin="anonymous"></script>
</head>

<body>
    <div id="map"></div>
    <div class="panel">
        <div id="search-ui" class="flex-column">
            <input type="text" id="search-input" placeholder="Search for a location...">
            <ul id="search-results"></ul>
        </div>
        <div id="details-ui" class="hidden">
            <h3 id="details-name"></h3>
            <p id="details-description"></p>
            <button id="details-directions" class="details-button details-action-button">Get Directions</button>
            <button id="details-close" class="details-button">Close</button>
        </div>
        <div id="directions-ui" class="hidden flex-column">
            <h3>Directions</h3>
            <div class="directions-inputs">
                <input type="text" id="origin-input" placeholder="Choose origin...">
                <ul id="origin-results" class="directions-results-list"></ul>
                <input type="text" id="destination-input" placeholder="Choose destination..." disabled>
            </div>
            <button id="get-directions" class="details-button details-action-button">Show Route</button>
            <div class="directions-step-nav">
                <span id="step-indicator"></span>
                <button id="prev-step" class="details-button">Previous</button>
                <button id="next-step" class="details-button">Next</button>
            </div>
            <button id="directions-close" class="details-button">Close Directions</button>
        </div>
    </div>
    <script src="script.js"></script>
</body>
</html>
```
{% endcode %}

#### Explanation of index.html updates

* The `#details-ui` panel now includes a "Get Directions" button, allowing users to open the directions panel for the selected location.
* The `#directions-ui` panel is added to the `.panel` container. This new panel contains:
  * Input fields for origin and destination.
  * A button to get directions.
  * Step navigation controls (step indicator, previous/next buttons).
  * A close button for the directions panel.
* All panels (`#search-ui`, `#details-ui`, `#directions-ui`) use consistent class and ID naming, and only one is visible at a time, managed by the show/hide functions in the JavaScript.

### Update style.css

Add styles for the new directions UI elements:

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
     transition: background-color 0.3s ease;
}

/* Specific style for the Close button */
#details-close {
    background-color: #ccc; /* Grey */
    color: #333;
}
 #details-close:hover {
     background-color: #bbb;
 }

/* Directions panel specific */
#directions-ui {
    width: 100%;
}
.directions-inputs {
    display: flex;
    flex-direction: column;
    gap: 4px;
    margin-bottom: 8px;
}
.directions-results-list {
    list-style: none;
    padding: 0;
    margin: 0;
    max-height: 120px;
    overflow-y: auto;
}
.directions-results-list li {
    padding: 8px 0;
    cursor: pointer;
    border-bottom: 1px solid #eee;
}
.directions-results-list li:last-child {
    border-bottom: none;
}
.directions-results-list li:hover {
    background-color: #f0f0f0;
}

.directions-step-nav {
    display: grid;
    grid-template-columns: 1fr 1fr;
    gap: 10px;
}

#step-indicator {
    grid-column: span 2;
}
```
{% endcode %}

#### Explanation of style.css updates

* Styles for the new `#directions-ui` panel and its child elements are added:
  * `.directions-inputs` styles the input fields and results list for selecting origin and destination.
  * `.directions-results-list` styles the list of search results for the origin input.
  * `.directions-step-nav` and `#step-indicator` style the step navigation controls.

### Update script.js

Add the following logic for directions and UI state management.

{% code lineNumbers="true" %}
```javascript
// script.js

// Define options for the MapsIndoors Google Maps view
const mapViewOptions = {
    element: document.getElementById('map'),
    // Initial map center (MapsPeople - Austin Office example)
    center: { lng: -97.74204591828197, lat: 30.36022358949809 },
    // Initial zoom level
    zoom: 17,
    // Maximum zoom level
    maxZoom: 22
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
const directionsUIElement = document.getElementById('directions-ui');

function showSearchUI() {
    hideDetailsUI();
    hideDirectionsUI(); // Ensure directions UI is hidden
    searchUIElement.classList.remove('hidden');
    searchInputElement.focus();
}

function showDetailsUI() {
    hideSearchUI();
    hideDirectionsUI(); // Ensure directions UI is hidden
    detailsUIElement.classList.remove('hidden');
}

function hideSearchUI() {
    searchUIElement.classList.add('hidden');
}

function hideDetailsUI() {
    detailsUIElement.classList.add('hidden');
}

function showDirectionsUI() {
    hideSearchUI();
    hideDetailsUI();

    directionsUIElement.classList.remove('hidden');
}

function hideDirectionsUI() {

    directionsUIElement.classList.add('hidden');
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

// Variable to store the location currently shown in details
let currentDetailsLocation = null;

// Show the details of a location
function showDetails(location) {
    // Keep track of the currently selected location
    currentDetailsLocation = location;
    detailsNameElement.textContent = location.properties.name;
    detailsDescriptionElement.textContent = location.properties.description || 'No description available.';
    showDetailsUI();
}

// Initial call to set up the search UI when the page loads
showSearchUI();

/** Directions Functionality **/

// Handles origin/destination selection, route calculation, and step navigation
const originInputElement = document.getElementById('origin-input');
const originResultsElement = document.getElementById('origin-results');
const destinationInputElement = document.getElementById('destination-input');
const getDirectionsButton = document.getElementById('get-directions');
const prevStepButton = document.getElementById('prev-step');
const nextStepButton = document.getElementById('next-step');
const stepIndicator = document.getElementById('step-indicator');
const directionsCloseButton = document.getElementById('directions-close');

let selectedOrigin = null;
let selectedDestination = null;
let currentRoute = null;
let directionsRenderer = null;

// Reference the details-directions button defined in the HTML
const detailsDirectionsButton = document.getElementById('details-directions');

detailsDirectionsButton.addEventListener('click', () => {
    showDirectionsPanel(currentDetailsLocation);
});

detailsCloseButton.addEventListener('click', showSearchUI);

directionsCloseButton.addEventListener('click', () => {
    hideDirectionsUI();
    showDetailsUI();
    if (directionsRenderer) directionsRenderer.setVisible(false);
});

// Show the directions panel and reset state for a new route
function showDirectionsPanel(destinationLocation) {
    selectedOrigin = null;
    selectedDestination = destinationLocation;
    currentRoute = null;
    destinationInputElement.value = destinationLocation.properties.name;
    originInputElement.value = '';
    originResultsElement.innerHTML = '';
    hideSearchUI();
    hideDetailsUI();
    showDirectionsUI();
    stepIndicator.textContent = '';
}

// Search for origin locations as the user types
originInputElement.addEventListener('input', onOriginSearch);
function onOriginSearch() {
    const query = originInputElement.value;
    const currentVenue = mapsIndoorsInstance.getVenue();
    originResultsElement.innerHTML = '';
    if (query.length < 3) return;
    const searchParameters = { q: query, venue: currentVenue ? currentVenue.name : undefined };
    mapsindoors.services.LocationsService.getLocations(searchParameters).then(locations => {
        if (locations.length === 0) {
            const noResultsItem = document.createElement('li');
            noResultsItem.textContent = 'No results found';
            originResultsElement.appendChild(noResultsItem);
            return;
        }
        locations.forEach(location => {
            const listElement = document.createElement('li');
            listElement.textContent = location.properties.name;
            listElement.addEventListener('click', () => {
                selectedOrigin = location;
                originInputElement.value = location.properties.name;
                originResultsElement.innerHTML = '';
            });
            originResultsElement.appendChild(listElement);
        });
    });
}

// Calculate and display the route when both origin and destination are selected
getDirectionsButton.addEventListener('click', async () => {
    if (!selectedOrigin || !selectedDestination) {
        // Optionally, show an alert or update stepIndicator instead
        stepIndicator.textContent = 'Please select both origin and destination.';
        return;
    }
    // Use anchor property for LatLngLiteral (anchor is always a point)
    const origin = {
        lat: selectedOrigin.properties.anchor.coordinates[1],
        lng: selectedOrigin.properties.anchor.coordinates[0],
        floor: selectedOrigin.properties.floor
    };
    const destination = {
        lat: selectedDestination.properties.anchor.coordinates[1],
        lng: selectedDestination.properties.anchor.coordinates[0],
        floor: selectedDestination.properties.floor
    };
    const directionsService = new mapsindoors.services.DirectionsService();
    const route = await directionsService.getRoute({ origin, destination });
    currentRoute = route;
    if (directionsRenderer) {
        directionsRenderer.setVisible(false);
    }

    directionsRenderer = new mapsindoors.directions.DirectionsRenderer({
        mapsIndoors: mapsIndoorsInstance,
        fitBounds: true,
        strokeColor: '#4285f4',
        strokeWeight: 5
    });

    await directionsRenderer.setRoute(route);
    directionsRenderer.setStepIndex(0, 0);
    showCurrentStep();
});

// Update the step indicator and enable/disable navigation buttons
function showCurrentStep() {
    if (currentRoute?.legs?.length < 1) return;
    const currentLegIndex = directionsRenderer.getLegIndex();
    const currentStepIndex = directionsRenderer.getStepIndex();
    const legs = currentRoute.legs;
    const steps = legs[currentLegIndex].steps;
    if (steps.length === 0) {
        stepIndicator.textContent = '';
        return;
    }
    stepIndicator.textContent = `Leg ${currentLegIndex + 1} of ${legs.length}, Step ${currentStepIndex + 1} of ${steps.length}`;
    prevStepButton.disabled = currentLegIndex === 0 && currentStepIndex === 0;
    nextStepButton.disabled = currentLegIndex === legs.length - 1 && currentStepIndex === steps.length - 1;
}

// Step navigation event listeners
prevStepButton.addEventListener('click', () => {
    if (!directionsRenderer) {
        return;
    }
    directionsRenderer.previousStep();
    showCurrentStep();

});
nextStepButton.addEventListener('click', () => {
    if (!directionsRenderer) {
        return;
    }
    directionsRenderer.nextStep();
    showCurrentStep();
});
```
{% endcode %}

**Explanation of script.js updates:**

* **UI State Management:**
  * New constants like `directionsUIElement` are added to reference the new directions panel in the DOM.
  * The existing UI state management functions (`showSearchUI()`, `showDetailsUI()`) are updated to explicitly hide the `directionsUIElement`.
  * New functions `showDirectionsUI()` and `hideDirectionsUI()` are introduced to manage the visibility of the directions panel, ensuring it's mutually exclusive with the search and details panels.
  * The "Get Directions" button (`detailsDirectionsButton`) in the details panel now has an event listener that calls `showDirectionsPanel()`, passing the `currentDetailsLocation` to pre-fill the destination.
  * The `directionsCloseButton` listener is added to hide the directions UI, show the details UI, and make the `directionsRenderer` invisible when directions are closed, providing a clear exit path.
* **Location Click Handling:**
  * The script maintains the unified click handler pattern established in previous steps. The `handleLocationClick` function continues to serve as the central entry point for user interactions with locations, responding to both map POI clicks and search result clicks.
  * This consistent pattern ensures that when a user clicks on a location (either on the map or in search results), the following actions always occur:
    * The map pans to the selected location using `mapsIndoorsInstance.goTo(location)`
    * The floor is set to the location's floor with `mapsIndoorsInstance.setFloor(location.properties.floor)`
    * The location is visually selected on the map using `mapsIndoorsInstance.selectLocation(location)`
    * The location details panel is shown via `showDetails(location)`
  * This unified approach ensures a consistent user experience regardless of how locations are selected.
* **Directions Panel and Origin/Destination Selection:**
  * New DOM element references are established for directions-related UI elements: `originInputElement`, `originResultsElement`, `destinationInputElement`, `getDirectionsButton`, `prevStepButton`, `nextStepButton`, `stepIndicator`, and `directionsCloseButton`.
  * State variables `selectedOrigin`, `selectedDestination`, `currentRoute`, and `directionsRenderer` are declared to manage the directions workflow state.
  * The `showDirectionsPanel(destinationLocation)` function resets the directions state, pre-fills the destination input with the selected location's name, and shows the directions UI.
  * An origin search is implemented with an `input` event listener on `originInputElement`, which calls the `onOriginSearch()` function. This function uses `mapsindoors.services.LocationsService.getLocations()` to search for origin locations, similar to the main search functionality.
* **Route Calculation:**
  * When the user clicks the "Show Route" button (`getDirectionsButton`), the script checks if both `selectedOrigin` and `selectedDestination` are set.
  * It extracts coordinates and floor information from the location objects' `properties.anchor` and `properties.floor`.
  * A new instance of `mapsindoors.services.DirectionsService()` is created.
  * The `getRoute()` method is called with origin and destination parameters to calculate the route.
  * The route is stored in the `currentRoute` variable for later reference.
* **Route Display and Step Navigation:**
  * Any existing `directionsRenderer` is hidden before creating a new one.
  * A new `mapsindoors.directions.DirectionsRenderer` is instantiated with the `mapsIndoorsInstance`, `fitBounds: true` for automatic map adjustment, and styling options.
  * The renderer is configured with the calculated route via `directionsRenderer.setRoute(route)`.
  * The renderer is set to display the first step of the first leg with `directionsRenderer.setStepIndex(0, 0)`.
  * The `showCurrentStep()` function updates the step indicator text and enables/disables the navigation buttons based on the current position in the route.
  * Event listeners for the navigation buttons call `directionsRenderer.previousStep()` and `directionsRenderer.nextStep()` respectively, followed by `showCurrentStep()` to update the UI.
* **Workflow Integration:**
  * The script carefully integrates the directions functionality with the existing search and details features.
  * The details panel now includes a "Get Directions" button that transitions to the directions workflow.
  * The directions panel allows users to return to the details view via the close button.
  * All three UI states (search, details, directions) are mutually exclusive, providing a clear and focused user interface.

These updates allow users to search for a location, view its details, and get step-by-step directions between two locations within your venue, all within a clear and interactive UI.

### Expected Outcome

* Users can search for a location, view its details, and click "Get Directions" to open the directions panel.
* When the directions panel opens from the details view, the destination input is pre-filled with the selected location's name and is disabled.
* The user can type in the origin input to search for and select an origin location.
* After both origin and destination are selected, clicking "Show Route" calculates and displays the route on the map.
* The map updates to show the full route, and the step navigation controls become active.
* Users can use the "Previous" and "Next" buttons to step through the route, with the map highlighting the current step and the `step-indicator` updating accordingly.
* Only one panel (search, details, or directions) is visible at a time.
* Clicking the "Close Directions" button returns to the details panel and hides the route from the map.

### Troubleshooting

* If the route is not shown after clicking "Show Route", ensure both origin and destination are selected and that the locations have valid anchor coordinates.
* If the map does not update, check for errors in the browser console and verify your API keys and venue ID.
* If the UI panels do not switch as expected, ensure the show/hide functions are called correctly and that the correct classes are applied.

{% embed url="https://codesandbox.io/p/sandbox/github/MapsPeople/mapsindoors-getting-started-for-web/tree/main/sdk/google.maps/4-getting-directions" %}
