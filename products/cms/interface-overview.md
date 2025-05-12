# Interface Overview

## Map <a href="#map" id="map"></a>

The Map section is the main navigation of the MapsIndoors CMS and includes the Filter bar at the top, a Toolbar at the bottom, a Floor selector on the right, and your Map in the center.

<figure><img src="../../.gitbook/assets/cms (6).jpg" alt=""><figcaption></figcaption></figure>

1. Use the "Solution Switcher" to select your active Solution.
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
17. A Floor selector to set the Floor for the Map.
18. The main toolbar to modify your data.

## Solution Switcher​ <a href="#combo-box" id="combo-box"></a>

<figure><img src="../../.gitbook/assets/CleanShot 2023-07-05 at 15.06.45@2x.png" alt=""><figcaption></figcaption></figure>

The Solution switcher allows you to search for the Solutions you have access to.



## Toolbar

From left to right, the functionalities in the Toolbar are as follows (you can also hover over the icons in the CMS to see their names):

* **Add POI** - Creates a Point of Interest by clicking on the Map. Afterwards, it opens an editor where Location details can be adjusted.
* **Add Area** - Creates an Area by clicking on the Map to create corners of a polygon. Afterwards, it opens an editor where Area details can be adjusted.
* **Show Network** - A toggle button to show or hide the Route Network used by the Wayfinding feature.
* **Add Barrier Route Element** - Creates a Route Element on your map. The Barrier Route Element can apply e.g. restrictions, a delay, or define a one-way direction to specific parts of the Network. For example, if you do not want your directions to guide your visitors down a specific hallway, but there isn't a Door present, this Route Element can be used.
* **Reload Route Network** - Reloads the Route Network. Route Element changes' effect on the Network becomes visible after reloading the Network.
* **Zoom Level** - Adjust the Zoom Level. Values range from 15 (zoom out) to 999 (also known as "max zoom").

## Doors & Barriers​ <a href="#doors--barriers" id="doors--barriers"></a>

Doors and Barriers are Route Elements that allow you to manipulate certain portions of the Route Network. They appear if you click the **Show Network** button mentioned above.

**Doors** are a Route Element that indicates the presence of a door - or other entryway - in a building.

* **Type** - Lets you define what type of Door this is. Each type has different defaults.
  * **Door** - A Door between two Rooms inside the same Building.
  * **ElevatorDoor** - A Door for an elevator comes with a built-in Delay.
  * **ExternalDoor** - A type of Door that links to the outside Route Network. These only connects to one Room.
  * **InterBuildingDoor** - A Door that connects two Rooms in two different Buildings.
  * **Opening** - An entryway between two Rooms inside the same Building, but with no physical door.
  * **Hatchway** - A specific kind of Door used for smaller rooms in some specific situations.
* **Restrictions** - Set optional limits on who can use this Door.
  * **Open for all** - Open for all Users.
  * **Open for specific App User Roles** - Define certain App User Roles that can use this Door.
  * **Closed for all** - No Users can use this door.
* **Delay (seconds)** - Set a Delay in seconds for routes that pass through this door. Used in estimated arrival time calculations.
* **Radius (meters)** - Set the Radius in meters for this Door, to ensure it touches the Route Network.
* **Floor Index** - Use this to move a Door from one Floor to another.
* **One-way direction (bearing)** - The _exit angle_ of a one-way Door, in degrees, like a compass bearing. In the illustration below, the entered value would be 45, as the user would exit at a 45-degree bearing.

By default doors are snapping, meaning they are following the lines of current solution's geometry:

<figure><img src="../../.gitbook/assets/snapping-doors (1).png" alt=""><figcaption></figcaption></figure>

While holding Shift keyboard button and moving mouse around, doors will:

* snap to the middle point of the closest wall of the closest polygon to the mouse pointer,
* snap to the corners of the polygon that mouse is the closest to.

Different doors creation modes:

* **Single** - Creates single door on the map. Can be accessed by Add Door Route Element button in the toolbar:

<figure><img src="../../.gitbook/assets/single-door-icon (1).png" alt=""><figcaption></figcaption></figure>

* **Multiple with fixed width** - Creates multiple doors on the map with fixed width. Can be accessed by keyboard shortcut: Ctrl+Shift+D for **Windows**, Shift+Control+D for **MacOS**.

<figure><img src="../../.gitbook/assets/multiple-fixed-width-doors-example (1).png" alt=""><figcaption></figcaption></figure>

* **Multiple with custom width** - Creates multiple doors on the map with custom width. Can be accessed by keyboard shortcut: Ctrl+Shift+F for **Windows**, Shift+Control+F for **MacOS**.

<figure><img src="../../.gitbook/assets/multiple-custom-width-doors-example (1).png" alt=""><figcaption></figcaption></figure>

When accessing Multiple doors creation mode, there is an option to choose door type for all the doors that will be created. You can also undo latest door creation.

**NOTE:** This is the only property that you can change while creating doors. The rest of the properties have to be edited for each door individually.

<figure><img src="../../.gitbook/assets/multiple-doors-toolbar (1).png" alt=""><figcaption></figcaption></figure>

**Barriers** are similar to Doors in that they are used to modify the Route Network. Many of the settings and restrictions you can set are the same between the two kinds.

* **Restrictions** - Set optional limits on who can bypass this Barrier.
  * **Open for all** - Open for all Users.
  * **Open for specific App User Roles** - Define certain App User Roles that can bypass this Barrier.
  * **Closed for all** - No users can bypass this Barrier.
* **Delay (seconds)** - Set a Delay in seconds for routes that pass through this Barrier.
* **Radius (meters)** - Set the Radius in meters for this Barrier to ensure it touches the Route Network.
* **Floor Index** - Use this to move a Barrier to another Floor.
* **One-way direction (bearing)** - The _exit angle_ of a one-way Barrier, in degrees, like a compass bearing. In the illustration above, the entered value would be 45, as the user would exit at a 45-degree bearing.



## Solution Details <a href="#solution-details" id="solution-details"></a>

Under Solution Details, you can find several subpages, which are described below.

### Types

This page organises the "Types" you can add to your Locations.

1. Create a new Type.
2. Select multiple Types for easy deletion.
3. "Edit Type" - Gives you the option to change the name of the Type, edit if given type is selectable, not selectable or if value should be inherited from Solution Settings, modify the App User Role restrictions, Sync to other Solutions or Add Custom Properties.
4. "Edit Display Rules" - The ability to modify the Display Rules for a given Type.
5. The Icon assigned to the Type.
6. The Display Name of the Type.
7. The Administrative ID of the Type.
8. The number of Locations with the given Type applied.
9. Whether the Type's "General Visibility" is set to Visible or Not Visible.

### Categories <a href="#categories" id="categories"></a>

Categories are similar to Types, but whereas Locations can only be of one Type, they can have multiple Categories. Categories are used for browsing important Locations or amenities in your application. For example, a canteen might be in a Category of both "Food" and "Leisure", but still only be of the Type "Canteen".

1. Create a new Category.
2. "Edit Category" to edit the properties of your Category.
3. The name of the Category.
4. The Category's Key.

This page lists the Buildings in the selected Venue in your Solution.

1. "Edit Building." Lets you edit the properties of the Building:
   * Building Name (translatable)
   * Address
   * Whether it's Searchable
   * Its External ID
   * Details about its Floors:
     * Their Floor Name
     * Searchable status
   * Default Floor
   * Building Photo
     * Choose from the Media Library or set a URL
   * [Custom Properties](../../sdks-and-frameworks/web/other-guides/custom-properties.md)
2. The Name of the Building.
3. The Administrative ID of the Building.
4. The Address of the Building.

### Venues <a href="#venues" id="venues"></a>

The list of Venues in your Solution.

1. "Edit Venue"
   * The Venue Name, including translations.
   * Venue Photo. Set via the [Media Library](media-library/) or URL.
   * Whether it's Searchable
   * Its External ID
   * The Default Floor for the Venue. Set by a Floor index. The list contains all Floor Indexes found across all Buildings in the Venue.
   * [Custom Properties](../../sdks-and-frameworks/web/other-guides/custom-properties.md).
2. The Name of your Venues.
3. The Venue ID

### Solution Settings

This page contains various subpages with more advanced styling settings. Read more about Solution Setting [here](solution-settings.md).

### App Settings​ <a href="#app-settings" id="app-settings"></a>

This page contains various subpages with more advanced settings about your app.

#### **App Configuration​**

This page contains various settings, such as names for your API keys, App User Roles and App Categories.

The "App Title" is used to set a name for your app in your application.

The "Alias" is a shorter name you can use for loading your application in your app. Think of it as a "named API key", so instead of a random combinations of letters and numbers, it's an easy to remember name instead.

Do not set an Alias if you want to make it more difficult to find and load your MapsIndoors data. In that case, you should only load the data with an API key.

<figure><img src="../../.gitbook/assets/image (15) (1).png" alt=""><figcaption></figcaption></figure>

"App User Roles" are used for controlling access to Location data, and for Wayfinding for specific users of your app.

1. Click the "Edit Role" icon to edit its name, including translations.
2. The Display Name.
3. Click "Add App User Role" to add to the list.

In the "App Categories" section, you can edit more details about your Categories.

1. Move your app Categories up and down in order. If your app uses the App Config, you can change the order in which the Categories are displayed in your app.
2. The name of the Category.
3. Toggle whether or not the Category in question is visible in the app.
4. The Icon selected for the Category.
5. Select an Icon to be used for the Category. Opens the [Media Library](media-library/).

### **API Keys​**

Here you manage the active API keys generated for your Solution. You need an API key to load your MapsIndoors data in your apps.

You can create as many API keys as you want, and it is good practice to use one for each place you need to load data from MapsIndoors (each mobile platform, web app etc.).

You can easily delete an API key if it is unused, or has been compromised.

1. The name that you want to use to identify the new API key.
2. Generate an API key.
3. The name of an active API key.
4. Save any changes you make.
5. Toggle between active and inactive API keys.
6. Your API key is located here in text form.
7. Delete the API key.

### Booking Provider

This submenu gives you the option of integrating a Booking system into your Solution. The exact menus presented here depend on which provider you opt for.

### Position Provider

MapsIndoors also allows you to integrate a Positioning system into your Solution. The exact menus presented here, like the Booking system, depend on which provider you opt for.

### Webex

As the options above, the possibilities presented for WebEx integration depend on the exact manner of integration.
