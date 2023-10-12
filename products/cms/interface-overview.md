# Interface Overview

## Map <a href="#map" id="map"></a>

The Map section is the main navigation of the MapsIndoors CMS and includes the Filter bar at the top, a Toolbar at the bottom, a Floor selector on the right, and your Map in the center.

<figure><img src="../../.gitbook/assets/cms (3).jpg" alt=""><figcaption></figcaption></figure>

1. Use the "Combo Box" to select your active solution.
2. Enters the "Map" view (shown on the image). This is the page you see when logging into the CMS.
3. Enters the "Solution Details" menu containing the submenus mentioned earlier.
4. Open the Standard Web App using the information entered in the CMS. (Only available if you have set an Alias)
5. Enter the "Settings" menu page containing subpages.
6. Opens a drop-down menu containing options such as "Docs," "Log Out," and a link to enabling two-factor authentication.
7. Select the active Venue. The filter bar can then narrow down the data you see on the Map and Lists for this Venue.
8. Select one or more Buildings.
9. Select one or more Floors. This is active when a single Building is selected.
10. Filters on whether Rooms, POIs, and Areas should be shown on the Map and in the List.
11. A filter to narrow down Locations to display specific Location Types.
12. A filter to narrow down Locations to display specific Categories.
13. A filter to narrow down Locations to display with specific App User Roles applied.
14. Click to search for Locations using a Location’s name, Location ID, Alias, or External ID.
15. Selecting one or more Locations in the List view activates the bulk edit button. This allows you to bulk update Location Attributes, such as Location Type, Searchability, Categories, Aliases, and more.
16. Expands a list containing all your Locations. Filters are applied to Location data shown on the Map and the List view.
17. A Floor selector to select the displayed Floor on the Map.
18. The main toolbar to modify your Solution.

## Combo Box[​](https://docs.mapsindoors.com/cms-interface-overview#combo-box) <a href="#combo-box" id="combo-box"></a>

<figure><img src="../../.gitbook/assets/CleanShot 2023-07-05 at 15.06.45@2x.png" alt=""><figcaption></figcaption></figure>

The combo box provides the following features to the user:

* Filtering of solutions if multiple solutions are available.
* Searching through solutions by typing text in the input field.
* Content can be copied and pasted from/into the input field.

## Toolbar[​](https://docs.mapsindoors.com/cms-interface-overview#toolbar) <a href="#toolbar" id="toolbar"></a>

<figure><img src="https://docs.mapsindoors.com/img/cms/Map_Toolbar.png" alt=""><figcaption></figcaption></figure>

From left to right, the functionalities in the Toolbar are as follows (you can also hover over the icons in the CMS to see their names):

* **Add POI** - Creates a Point of Interest by clicking on the Map. Afterward, it opens an editor where Location details can be adjusted.
* **Add Area** - Creates an Area by clicking on the Map to create corners of a polygon. Afterward, it opens an editor where Area details can be adjusted.
* **Show Network** - A toggle button to show or hide the Route Network your Directions feature will use.
* **Add Barrier Route Element** - Creates a Route Element on your map. The Barrier Route Element can apply, e.g., restrictions, delay, or define one-way direction to specific parts of the Network. For example, if you do not want your directions to guide your visitors down a specific hallway, but there isn't a Door present, this Route Element can be used.
* **Reload Route Network** - Reloads the Route Network. Route Element changes' effect on the Network becomes visible after reloading the Network.
* **Zoom Level** - Adjust the Zoom Level. Values range from 15 (zoom out) to 22 (zoom in).

## Doors & Barriers[​](https://docs.mapsindoors.com/cms-interface-overview#doors--barriers) <a href="#doors--barriers" id="doors--barriers"></a>

Doors and Barriers are Route Elements that allow you to manipulate certain portions of the Route Network. They appear if you click the **Show Network** button mentioned above.

**Doors** are a Route Element that indicates the presence of a door - or other entryway - in a building.

![A screenshot of menu for adjusting doors](https://docs.mapsindoors.com/img/cms/door\_menu.png)

* **Type** - Lets you define what type of Door this is. Each type has different defaults.
  * **Door** - A Door between two Rooms inside the same Building.
  * **ElevatorDoor** - A Door for an elevator comes with a built-in Delay.
  * **ExternalDoor** - A type of Door that links to the outside Route Network, only connects to one Room.
  * **InterBuildingDoor** - A Door that connects two Rooms in two different Buildings.
  * **Opening** - An entryway between two Rooms inside the same Building, but with no physical door.
  * **Hatchway** - A specific kind of Door used for smaller rooms in some specific situations.
* **Restrictions** - Set optional limits on who can use this Door.
  * **Open for all** - Open for all Users.
  * **Open for specific App User Roles** - Define certain App User Roles that can use this Door.
  * **Closed for all** - No Users can use this door.
* **Delay (seconds)** - Set a Delay in seconds for routes that pass through this door. Used in estimated arrival time calculations.
* **Radius (meters)** - Set the Radius in meters for this Door, to ensure it touches the Route Network.
* **Floor Index** - Use this to move a Door between two Floors.
* **One-way direction (bearing)** - The exit angle of a one-way Door, in degrees, like a compass bearing. In the illustration below, the entered value would be 45, as the user would exit at a 45-degree bearing.

![An illustration that demonstrates how to interpret bearing](https://docs.mapsindoors.com/img/cms/one\_way\_bearing\_compass.png)

**Barriers** are similar to Doors in that they are used to modify the Route Network. Many of the settings and restrictions you can set are the same between the two kinds.

![A screenshot of menu for adjusting barriers](https://docs.mapsindoors.com/img/cms/barrier\_menu.png)

* **Restrictions** - Set optional limits on who can bypass this Barrier.
  * **Open for all** - Open for all Users.
  * **Open for specific App User Roles** - Define certain App User Roles that bypass this Barrier.
  * **Closed for all** - No Users can bypass this Barrier.
* **Delay (seconds)** - Set a Delay in seconds for routes that pass through this Barrier. Used in estimated arrival time calculations.
* **Radius (meters)** - Set the Radius in meters for this Barrier to ensure it touches the Route Network.
* **Floor Index** - Use this to move a Barrier between two Floors.
* **One-way direction (bearing)** - The exit angle of a one-way Barrier, in degrees, like a compass bearing. In the illustration above, the entered value would be 45, as the user would exit at a 45-degree bearing.

## Solution Details[​](https://docs.mapsindoors.com/cms-interface-overview#solution-details) <a href="#solution-details" id="solution-details"></a>

Under Solution Details, you can find several subpages, which are described below:

### Types[​](https://docs.mapsindoors.com/cms-interface-overview#types) <a href="#types" id="types"></a>

<figure><img src="https://docs.mapsindoors.com/img/cms/Solution_Details_Types.png" alt=""><figcaption></figcaption></figure>

This page organizes the "Types" you sort your locations into.

1. Create a new Type.
2. Click to select a Type for easy selection of multiple types at once.
3. "Edit Type" - Gives you the option to change the name of the Type or to modify the App User Role restrictions.
4. "Edit Display Rules" - The ability to modify the Display Rules for a given Type.
5. The name of the Type.
6. The Icon assigned to the Type.
7. Displays what information the Type's Label contains when displayed on your Map.
8. The number of Locations with the given Type applied.
9. "Edit Template" - Edit the template for a given Type.

### Categories[​](https://docs.mapsindoors.com/cms-interface-overview#categories) <a href="#categories" id="categories"></a>

<figure><img src="https://docs.mapsindoors.com/img/cms/Solution_Details_Categories.png" alt=""><figcaption></figcaption></figure>

Categories are similar to Types, but whereas Locations can only be of one Type, they can be of multiple Categories. Categories are used for browsing important Locations or amenities in your application. - For example, a canteen might be in a Category of both "Food" and "Leisure", but still only be of the Type "Canteen".

1. Create a new Category.
2. "Edit Category" - Edit properties of your Category.
3. The name of the Category.
4. The Key belongs to the Category.

### Type Visibility[​](https://docs.mapsindoors.com/cms-interface-overview#type-visibility) <a href="#type-visibility" id="type-visibility"></a>

<figure><img src="https://docs.mapsindoors.com/img/cms/Solution_Details_Type_Visibility.png" alt=""><figcaption></figcaption></figure>

Type Visibility is the term used to determine at which Zoom Levels both the Types' Icons and Labels are visible.

1. A save button. Click this to save your changes.
2. Visibility toggle - Click to toggle on/off the Type visibility. It controls whether the Type Icons and Labels are shown on the Map.
3. Set the minimum and maximum Zoom Level at which a given Type should be visible.

### **Buildings**[**​**](https://docs.mapsindoors.com/cms-interface-overview#buildings)

![A screenshot of the page overview of Buildings](https://docs.mapsindoors.com/img/cms/Solution\_Details\_Buildings.png)

This page lists the Buildings in the selected Venue in your Solution.

1. "Edit Building." Lets you edit the properties of the Building, such as name, ID, and Floors.
2. The Name of the Building.
3. The Administrative ID of the Building.
4. The Address of the Building.

### Venues[​](https://docs.mapsindoors.com/cms-interface-overview#venues) <a href="#venues" id="venues"></a>

<figure><img src="https://docs.mapsindoors.com/img/cms/Solution_Details_Venues.png" alt=""><figcaption></figcaption></figure>

A page featuring a list of Venues in your Solution. MapsIndoors provide these, and to add more, contact your representative.

1. "Edit Venue" - You can, e.g., edit Venue Name and Venue External ID in the Venue details editor.
2. The Name of your Venues.
3. The Venue ID of your Venue.

### App Settings[​](https://docs.mapsindoors.com/cms-interface-overview#app-settings) <a href="#app-settings" id="app-settings"></a>

This page contains various subpages with more advanced settings about your app.

### **App Configuration**[**​**](https://docs.mapsindoors.com/cms-interface-overview#app-configuration)

This page contains various settings, such as names for your API keys, App User Roles and App Categories.

<figure><img src="https://docs.mapsindoors.com/img/cms/Solution_Details_App_Title.png" alt=""><figcaption></figcaption></figure>

Here you can change the title of your app.

1. Save the changes you've made.
2. Enter the name you wish to use.

<figure><img src="https://docs.mapsindoors.com/img/cms/Solution_Details_Alias.PNG" alt=""><figcaption></figcaption></figure>

The API Keys used to make your MapsIndoors Solution consist of random combinations of letters and numbers. Here, you can assign them an Alias to make remembering easier.

ALIAS

Do not set an Alias if you want to make it more difficult to find and load your MapsIndoors data. In that case, you should only load the data with an API key.

<figure><img src="https://docs.mapsindoors.com/img/cms/Solution_Details_App_User_Roles.PNG" alt=""><figcaption></figcaption></figure>

You can also modify your App User Roles from within these pages.

1. "Edit Role" - Edit settings about one specific App User Role.
2. The names of your App User Roles.
3. "Add App User Role" - Create a new App User Role.

<figure><img src="https://docs.mapsindoors.com/img/cms/Solution_Details_App_Categories.png" alt=""><figcaption></figcaption></figure>

Here you can select which Categories can be used for browsing the app.

1. Move your app Categories up and down in order.
2. The name of the Category.
3. Toggle whether or not the Category in question is visible in the app.
4. The Icon selected for the Category.
5. Select an Icon to be used for the Category.

### **API Keys**[**​**](https://docs.mapsindoors.com/cms-interface-overview#api-keys)

Here you manage the active API keys generated for your Solution. You need an API key to load your MapsIndoors data in your apps.

You can create as many API keys as you want, and it is good practice to use one for each place you need to load data from MapsIndoors (each mobile platform, web app etc.).

You can easily delete an API key if it is unused or has been compromised.

<figure><img src="https://docs.mapsindoors.com/img/cms/Solution_Details_API_Keys_V2.png" alt=""><figcaption></figcaption></figure>

1. The name that you want to use to identify the new API key.
2. Generate an API key.
3. The name of an active API key.
4. Save any changes you make.
5. Toggle between active and inactive API keys.
6. Your API key is located here in text form.
7. Delete the API key.

### **Booking Provider**[**​**](https://docs.mapsindoors.com/cms-interface-overview#booking-provider)

This submenu gives you the option of integrating a Booking system into your Solution. The exact menus presented here depend on which provider you opt for.

### **Position Provider**[**​**](https://docs.mapsindoors.com/cms-interface-overview#position-provider)

MapsIndoors also allows you to integrate a Positioning system into your Solution. The exact menus presented here, like the Booking system, depend on which provider you opt for.

### **Webex**[**​**](https://docs.mapsindoors.com/cms-interface-overview#webex)

As the options above, the possibilities presented for WebEx integration depend on the exact manner of integration.
