# Kiosk

Please do the following before getting started:

* Disable the on-screen keyboard in the operating system of the computer the Kiosk app will run on
* Disable the browser's position

### Create Kiosk Locations in the CMS[​](https://docs.mapsindoors.com/kiosk#create-kiosk-locations-in-the-cms) <a href="#create-kiosk-locations-in-the-cms" id="create-kiosk-locations-in-the-cms"></a>

Log in to the CMS and create a new Location Type. Give it a memorable name like e.g. "Kiosk".

Create all your physical Kiosks as Locations in the CMS, assigning them to the Location Type you just created.

### Configure the Kiosk[​](https://docs.mapsindoors.com/kiosk#configure-the-kiosk) <a href="#configure-the-kiosk" id="configure-the-kiosk"></a>

To set up your Kiosk, you need to set a few parameters in the URL. The `?` symbol has to be in front of the first parameter, and all other parameters are separated with an `&` symbol.

This is what the full URL for an app in Kiosk mode could look like:

[http://kiosk.mapsindoors.com/demo?originLocation=652cf26a26784b4e9a390d8b\&zoom=22\&timeout=20\&legend=true\&bearing=180\&pitch=60\&liveData=occupancy,availability,position](http://kiosk.mapsindoors.com/demo?originLocation=652cf26a26784b4e9a390d8b\&zoom=22\&timeout=20\&legend=true\&bearing=180\&pitch=60\&liveData=occupancy,availability,position)

Splitting the URL, this is what it contains:

* **`demo`** is the `Alias` for your Solution (you can use an API key in the same way if an Alias is not set). This is found in "Solution Details" -> "App Settings" -> "App Configuration" for the `Alias`, or "Solution Details" -> "App Settings" -> "API Keys" for API keys:

![A screenshot of the page overview of Aliases](https://docs.mapsindoors.com/img/cms/Solution\_Details\_Alias.PNG)![A screenshot of the page overview of API Keys](https://docs.mapsindoors.com/img/cms/Solution\_Details\_API\_Keys\_V2.png)

* **`originLocation=652cf26a26784b4e9a390d8b`** is the Location selected as "origin Location", i.e. where your Kiosk is located in the real world. This is a Location ID found in the "Location Details" panel, in the "Details" tab:

![A screenshot of the page overview of the Kiosk](https://docs.mapsindoors.com/img/various/Kiosk\_Location\_ID.png)

* **`zoom=22`** is the zoom level to start at
* **`&timeout=20`** is the timeout period (in seconds) before resetting the map view
* **`&legend=true`** is whether the Legend should be displayed
* **`&bearing=180`** is how much the map should be rotated from North (in this case 180 degrees, so due South)
* **`&pitch=60`** is how much the map should tilt towards the horizon
* **`&liveData=occupancy,availability,position`** is which Live Data Domains should be enabled on the Kiosk

All the parameters can be combined individually and differently for each Kiosk you set up.

The URL parameters are saved in the browser's _local storage_ to persist across an app refresh, without the need for manually entering them again. They are all prefixed with `MIKIOSK:param`. The saved parameters are overwritten when new URL parameters are set, and can only be deleted by clearing the browser's local storage.

When testing the Kiosk, it is highly recommended to use Chrome's "Incognito" or Safari's "Private Browsing" feature to ensure the local storage is empty.

#### No URL Parameters[​](https://docs.mapsindoors.com/kiosk#no-url-parameters) <a href="#no-url-parameters" id="no-url-parameters"></a>

It is also possible to launch the kiosk without utilising URL parameters like those specified above, by using the link [http://kiosk.mapsindoors.com/](http://kiosk.mapsindoors.com/). If the kiosk is launched in this manner, you will be asked to enter an API-key and a Location ID to use as an origin point, information on which can be found [here](https://docs.mapsindoors.com/kiosk#configure-the-kiosk/). The kiosk will then launch based on these parameters.

![A screenshot of the page overview of the Kiosk with no URL](https://docs.mapsindoors.com/img/various/kiosk-no-url.png)

### Legend Fields[​](https://docs.mapsindoors.com/kiosk#legend-fields) <a href="#legend-fields" id="legend-fields"></a>

To update and present custom fields in the Legend, add _Custom Properties_ to the Location set as `originLocation`.

The Custom Properties are grouped together by the index (number) defined in the key. You can add as many fields as you would like.

#### Example[​](https://docs.mapsindoors.com/kiosk#example) <a href="#example" id="example"></a>

| Key            | Value                  |
| -------------- | ---------------------- |
| 1LegendHeading | Header text goes here  |
| 1LegendContent | Content text goes here |
| 2LegendHeading | Header text goes here  |
| 2LegendContent | Content text goes here |
| ...            | ...                    |

### Google Analytics Tracking[​](https://docs.mapsindoors.com/kiosk#google-analytics-tracking) <a href="#google-analytics-tracking" id="google-analytics-tracking"></a>

Google Analytics is used for general analytics, page view and event tracking in the Kiosk app.

To collect statistics in Google Analytics, provide your MapsIndoors representative with a Google Analytics Property ID (tracking id).

You will receive stats and events in the Google Analytics Dashboard approximately 24 hours after the key has been set.

#### Page View Tracking[​](https://docs.mapsindoors.com/kiosk#page-view-tracking) <a href="#page-view-tracking" id="page-view-tracking"></a>

A page view event is sent for the following views:

* Home
* Search
* Details

#### Event tracking[​](https://docs.mapsindoors.com/kiosk#event-tracking) <a href="#event-tracking" id="event-tracking"></a>

The following events is sent from the kiosk when triggered:

| Category       | Action                   | Label                                                      | Internal | Description                                                           |
| -------------- | ------------------------ | ---------------------------------------------------------- | -------- | --------------------------------------------------------------------- |
| Initialization | URL parameter configured | Origin Location was set                                    | True     | When the origin location parameter is set                             |
| Initialization | URL parameter configured | Timeout was set                                            | True     | When the timeout parameter is set                                     |
| Initialization | URL parameter configured | Zoom parameter was set                                     | True     | When the zoom level parameter is set                                  |
| Initialization | URL parameter configured | Bearing parameter was set                                  | True     | When the bearing parameter is set                                     |
| Initialization | URL parameter configured | Pitch parameter was set                                    | True     | When the pitch parameter is set                                       |
| Initialization | URL parameter configured | Legend was set                                             | True     | When the legend params is set to true                                 |
| Initialization | URL parameter configured | LiveData parameter was set (\[DOMAIN TYPES])               | True     | When live data parameter is set                                       |
| Home           | Reset                    | Kiosk was reset                                            | True     | When the kiosk is dirty and has been idle for X amount of seconds     |
| Search         | Location selected        | Location id: \[LOCATION ID], Search query: \[SEARCH QUERY] | False    | When a location is selected in the list and a search query is entered |
| Search         | Location selected        | Location id: \[LOCATION ID]                                | False    | When a location is selected in the list                               |
| Search         | Category filter applied  | Selected "\[CATEGORY NAME]" Category                       | False    | When a category is selected                                           |
| Details        | QR Code                  | QR code generated                                          | False    | When a QR code is successfully generated                              |
| Details        | Send SMS                 | SMS successfully sent                                      | False    | When a SMS is successfully sent                                       |
| Details        | Send SMS                 | SMS failed to send                                         | False    | When a SMS is unsuccessfully sent                                     |

### Configuring Chrome to Run in Kiosk Mode[​](https://docs.mapsindoors.com/kiosk#configuring-chrome-to-run-in-kiosk-mode) <a href="#configuring-chrome-to-run-in-kiosk-mode" id="configuring-chrome-to-run-in-kiosk-mode"></a>

Starting Chrome in "kiosk mode" enforces fullscreen mode, removes the address bar, the close and minimise buttons as well as bookmarks. Starting the browser in "kiosk mode" requires the `--kiosk` parameter along with a target URL.

Here's how you would do it on a computer running Windows:

1.  Create a shortcut to chrome.exe located in the application folder

    ```
    C:\Program Files (x86)\Google\Chrome\Application
    ```
2. Right-click on the shortcut and select "Properties"
3. In the "Target" field add the `--kiosk` parameter to the path
4.  Now add the target URL, e.g.:

    ```
    "C:\Program Files (x86)\Google\Chrome\Application\chrome.exe" --kiosk "http://kiosk.mapsindoors.com/demo"
    ```

Now close all instances of Chrome, and start Chrome in kiosk mode using the shortcut.

### Send SMS with Link to Route[​](https://docs.mapsindoors.com/kiosk#send-sms-with-link-to-route) <a href="#send-sms-with-link-to-route" id="send-sms-with-link-to-route"></a>

For the SMS feature to work, MapsPeople needs to activate it.

The limit for sending an SMS to the same phone number is 3 messages per 10 minutes.

### URL Parameters[​](https://docs.mapsindoors.com/kiosk#url-parameters) <a href="#url-parameters" id="url-parameters"></a>

#### `originLocation`[​](https://docs.mapsindoors.com/kiosk#originlocation) <a href="#originlocation" id="originlocation"></a>

The `originLocation` URL parameter is set with a Location's ID. This can be found in the CMS on the Location's details page.

The Kiosk will center the viewport on the `originLocation` when initialized and every time it resets, and all routes start originate at this Location.

The `originLocation` URL parameter is saved in the browser's local storage as `MIKIOSK:{miApiKey}-paramOriginLocation`.

**Example**[**​**](https://docs.mapsindoors.com/kiosk#example-1)

```
http://kiosk.mapsindoors.com/demo?originLocation=f952e8bcf8f0423b96f23611
```

#### `liveData`[​](https://docs.mapsindoors.com/kiosk#livedata) <a href="#livedata" id="livedata"></a>

The `liveData` URL parameter is used for enabling Live Data. The URL parameter accepts a comma-separated list of Live Data Domain types.

Available Domain types can be found here: [https://app.mapsindoors.com/mapsindoors/js/sdk/latest/docs/mapsindoors.LiveDataManager.html#.LiveDataManager.LiveDataDomainTypes](https://app.mapsindoors.com/mapsindoors/js/sdk/latest/docs/mapsindoors.LiveDataManager.html#.LiveDataManager.LiveDataDomainTypes)

The `liveData` URL parameter is saved in the browser's local storage as `MIKIOSK:{miApiKey}-paramLiveData`.

[Get in touch](https://resources.mapspeople.com/contact-us) to hear more about how to set up Live Data integrations for your Solution.

**Example**[**​**](https://docs.mapsindoors.com/kiosk#example-2)

```
http://kiosk.mapsindoors.com/demo?liveData=occupancy,availability,position
```

#### `zoom`[​](https://docs.mapsindoors.com/kiosk#zoom) <a href="#zoom" id="zoom"></a>

The `zoom` level URL parameter is used for setting a default zoom level. It defaults to 21 if the parameter is not set. The configured zoom level is used when the kiosk is initialized and every time it resets to the initial view. The zoom range will have to be between 0 - 22.

The `zoom` level URL parameter is saved in the browser's local storage as `MIKIOSK:{miApiKey}-paramZoom`.

**Example**[**​**](https://docs.mapsindoors.com/kiosk#example-3)

```
http://kiosk.mapsindoors.com/demo?zoom=22
```

#### `timeout`[​](https://docs.mapsindoors.com/kiosk#timeout) <a href="#timeout" id="timeout"></a>

The `timeout` URL parameter is used to set a period after which the Kiosk will reset to its initial state if it has not been touched. The entered value is in seconds.

The `timeout` URL parameter is saved in the browser's local storage as `MIKIOSK:{miApiKey}-paramTimeout`.

**Example**[**​**](https://docs.mapsindoors.com/kiosk#example-4)

```
http://kiosk.mapsindoors.com/demo?timeout=20
```

#### `legend`[​](https://docs.mapsindoors.com/kiosk#legend) <a href="#legend" id="legend"></a>

By adding the `legend` URL parameter, the Legend card is displayed on top of the map. The Legend provides a list of Categories for which there are Locations in the Venue associated with the `originLocation`.

The `legend` URL parameter is saved in the browser's local storage as `MIKIOSK:{miApiKey}-paramLegend`.

**Example**[**​**](https://docs.mapsindoors.com/kiosk#example-5)

```
http://kiosk.mapsindoors.com/demo?legend=true
```

#### `bearing`[​](https://docs.mapsindoors.com/kiosk#bearing) <a href="#bearing" id="bearing"></a>

By adding the `bearing` URL parameter, the angle (heading) of the map is controlled. With a range between 0 and 360 degrees, where 0° is north, the map is rotated around its center.

The `bearing` URL parameter is saved in the browser's local storage as “`MIKIOSK:{miApiKey}-paramBearing`”.

**Example**[**​**](https://docs.mapsindoors.com/kiosk#example-6)

```
http://kiosk.mapsindoors.com/demo?bearing=180
```

#### `pitch`[​](https://docs.mapsindoors.com/kiosk#pitch) <a href="#pitch" id="pitch"></a>

By adding the `pitch` URL parameter, the angle towards the horizon measured in degrees of the map is controlled, with a range between 0 and 60 degrees. Zero degrees results in a two-dimensional map, as if you're looking straight down on the ground.

The `pitch` URL parameter is saved in the browser's local storage as `MIKIOSK:{miApiKey}-paramPitch`.

**Example**[**​**](https://docs.mapsindoors.com/kiosk#example-7)

```
http://kiosk.mapsindoors.com/demo?pitch=60
```

### Live Data Badges[​](https://docs.mapsindoors.com/kiosk#live-data-badges) <a href="#live-data-badges" id="live-data-badges"></a>

When subscribing to Live Data (see _URL parameters_), badges will be applied to the icons for Locations that have Live Data.

* **Availability:** Will add a green badge with a checkmark, or a red badge with a cross, depending on the _availability_ of the Location.
* **Occupancy**: Will add a black badge with a number depicting the _occupancy_ of the location (`nrOfPeople`).
