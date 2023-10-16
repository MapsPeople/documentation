---
description: >-
  In this guide you will be introduced to the concept of Display Rules and how
  you can use Display Rules to change how Locations (POIs, Rooms and Areas) are
  displayed on the map.
---

# Display Rules

## Introduction

Display Rules are the backbone of how you style the MapsIndoors data on your map. They are an incredibly powerful and flexible way to decide at which zoom levels icons appear, what colors to use for different types of places, and which 3D Models to use for your bookable desks, landmarks, or office decoration.

With the MapsIndoors CMS, you can control the initial appearance of Locations. In your app, at _runtime_, you can change Display Rules individually for a Location, or collective for a specific Type. You can do this based on logic inside your app, or as a result of outside logic in an external system that should reflect on the map. Besides the CMS, and in your app at runtime, you are also able to use the [Integration API](../../sdks-and-frameworks/integration-api/) to control Display Rules.

Here are a few examples for cases where it could make sense to change the Locations' Display Rules, but there are a lot of other use cases besides these too.

1. You can use Display Rules to match the colors of the selected Location's polygon to your brand guidelines to ensure a coherent visual expression throughout your application.
2. It is possible to connect your MapsIndoors Solution to an _occupancy_ monitoring system using [Live Data integrations](../../sdks-and-frameworks/web/data-visualization/live-data/), so the information about whether or not a room is occupied, and by how many people, is relayed to the MapsIndoors system. This information could then be used to color a Room red when it is occupied, or green if it is available.
3. A similar concept could be applied to Icons. Instead of the color of the Polygon changing depending on occupancy status, the Icon used for the Location could change based on whether or not the room is available.

## Display Rule Hierarchy[​](https://docs.mapsindoors.com/display-rules#display-rule-hierarchy) <a href="#display-rule-hierarchy" id="display-rule-hierarchy"></a>

In each MapsIndoors SDK, the "Main Display Rule" outlines a list of sensible defaults for all geodata. Each Location Type inherits its values from this Main Display Rule, unless the value is specifically overridden.

Each Location (Room, Area or POI) uses the Display Rule from the combined Main and Type Display Rules, except for values that are specifically set for the individual Location.

Here's a visualization of the inheritance principle:

<figure><img src="https://docs.mapsindoors.com/img/map/display-rule-inheritance.png" alt=""><figcaption></figcaption></figure>

As an example, you set all polygons to be `red` in your Main Display Rule. The Location Type for "Meeting Room" specifies that their polygons should be `blue`, while the "Executive Meeting Room" Location specifically has an `orange` polygon.

No matter what is specified in this hierarchy, you can override it _runtime_ in your app. That means, as an example, that all matches for a specific search query can be specified to have a polygon color that is `pink`, regardless of what exists in the Main Display Rule, on their Types, and for those Locations specifically.

To remove a value from the Display Rule (to make it inherit from further up the hierarchy) set the property to `null`.

<mark style="color:red;">**TODO: Add a code snippet on how to change (setDisplayRule)**</mark>

### Display Rule Properties[​](https://docs.mapsindoors.com/display-rules#display-rule-properties) <a href="#display-rule-properties" id="display-rule-properties"></a>

In the CMS, you can edit your Types' Display Rules in `Solution Details > Types > Edit [Location Type name] > Display Rules`. This will open an overview of all Display Rules properties.

The "Lock" icon present in all sections determines whether or not the Display Rule is inheriting from the Main Display Rule. Click the lock to enable overriding the value.

#### General[​](https://docs.mapsindoors.com/display-rules#general) <a href="#general" id="general"></a>

![Display Rules](https://docs.mapsindoors.com/img/map/Display\_Rules\_General.png)

The "General" visibility switch determines whether Locations of this Type are visible on the map. The system will accept a Boolean here, so either `true` or `false`.

#### Icon[​](https://docs.mapsindoors.com/display-rules#icon) <a href="#icon" id="icon"></a>

<figure><img src="https://docs.mapsindoors.com/img/map/Display_Rules_Icon.png" alt=""><figcaption></figcaption></figure>

The "Icon" section contains options related to the appearance of the Icon.

1. **Visibility** - Controls whether the Icon is visible on the map.
   * The system will accept a Boolean here, so either `true` or `false`.
2. **Zoom from** - Sets the minimum Zoom Level at which the Icon is visible.
   * The value must be a number between 1 and 999, with 1 being very far away, and 999 acting as "max zoom". Google Maps only supports up to zoom level 22 at most, with Mapbox supporting up to level 25. Setting a value above these, your Icons will not show up on the map.
   * In a general use case, most maps will only need values from zoom level 15, unless you're building a view to show multiple Venues across a country or the like.
3. **Zoom to** - Sets the maximum Zoom Level at which the Icon is visible.
   * The value must be a number between 1 and 999, with 1 being very far away, and 999 acting as "max zoom". Google Maps only supports up to zoom level 22 at most, with Mapbox supporting up to level 25.
   * The recommended setting is to click the "Max zoom" checkbox, which sets the value to 999 in the data.
4. **Icon** - Use the Media Library in the CMS to control which Icon is shown on the map.
   * The Media Library is a tool to select the displayed Icon from either a pre-loaded selection of Icons, or for you to upload your own.
   * In-app, you can provide a URL to a desired Icon.
   * In-app, you can also define `iconSize`, by giving the desired size in pixels.

#### Label[​](https://docs.mapsindoors.com/display-rules#label) <a href="#label" id="label"></a>

<figure><img src="https://docs.mapsindoors.com/img/map/Display_Rules_Label.png" alt=""><figcaption></figcaption></figure>

The "Label" section contains options related to the appearance of the Label. The Label is the text associated with the Location on the map, positioned next to the Icon or on its own.

1. **Visibility** - Controls whether the Label is visible on the map.
   * The system will accept a Boolean here, so either `true` or `false`.
2. **Zoom from** - Sets the minimum Zoom Level at which the Label is visible.
   * The value must be a number between 1 and 999, with 1 being very far away, and 999 acting as "max zoom". Google Maps only supports up to zoom level 22 at most, with Mapbox supporting up to level 25. Setting a value above these, your Labels will not show up on the map.
3. **Zoom to** - Sets the maximum Zoom Level at which the Label is visible.
   * The value must be a number between 1 and 999, with 1 being very far away, and 999 acting as "max zoom". Google Maps only supports up to zoom level 22 at most, with Mapbox supporting up to level 25.
   * The recommended setting is to click the "Max zoom" checkbox, which sets the value to 999 in the data.
4. **Template** - Controls the information the Label should contain. Only applies to the CMS, any change at runtime will overwrite the information set up by the Template.
   * Location Name - Only displays the Name of the Location.
   * External ID - Only displays the External ID set on the Location.
   * External ID & Location Name - Displays both the External ID and the Location Name, with the External ID first.
   * Location Name & External ID - Displays both the Location Name and the External ID, with the Location Name first.
5. **Max width** - Specify how wide (in pixels) a Label can be before forcing a line-break.
   * A value of `0` will ensure no line-breaks for this label.

#### Polygon[​](https://docs.mapsindoors.com/display-rules#polygon) <a href="#polygon" id="polygon"></a>

<figure><img src="https://docs.mapsindoors.com/img/map/Display_Rules_Polygon.png" alt=""><figcaption></figcaption></figure>

Polygons are independent from tiles. Tiles are drawn by MapsPeople and overlaid onto the mapping engine (Mapbox or Google Maps). Polygons are an overlay with customisable attributes that are then overlaid on top of the Tiles. Therefore, while you can edit the "Polygon" attributes of a Location connected to a Tile, be that an Area or a Room, you need to account for attributes such as the opacity of the Polygon in the resulting appearance. It is not currently possible to override the appearance of Tiles.

The "Polygon" section contains options related to the appearance of the Polygon.

1. **Visibility** - Controls whether the Polygon is visible on the map.
   * The system will accept a Boolean here, so either `true` or `false`.
2. **Zoom from** - Sets the minimum Zoom Level at which the Polygon is visible.
   * The value must be a number between 1 and 999, with 1 being very far away, and 999 acting as "max zoom". Google Maps only supports up to zoom level 22 at most, with Mapbox supporting up to level 25. Setting a value above these, your Polygon will not show up on the map.
3. **Zoom to** - Sets the maximum Zoom Level at which the Polygon is visible.
   * The value must be a number between 1 and 999, with 1 being very far away, and 999 acting as "max zoom". Google Maps only supports up to zoom level 22 at most, with Mapbox supporting up to level 25.
   * The recommended setting is to click the "Max zoom" checkbox, which sets the value to 999 in the data.
4. **Stroke color** - Controls the stroke color of the Polygon.
   * In the CMS, you can select a color using the color picker displayed when clicking the color input field.
   * If setting the color in-app, the value provided must be in 6-digit HEX code (eg. #3071D9).
5. **Stroke width** - Controls the stroke width (in pixels) of the Polygon.
6. **Stroke opacity** - Controls the stroke opacity of the Polygon.
   * The value here should be between `0` and `1`. A value of `1` gives 100% opacity, `0.2` gives 20% opacity, etc.
7. **Fill color** - Controls the fill color of the Polygon.
   * In the CMS, you can select a color using the color picker displayed when clicking the color input field.
   * If setting the color in-app, the value provided must be in 6-digit HEX code (eg. #3071D9).
8. **Fill opacity** - Controls the fill opacity of the Polygon.
   * The value here should be between `0` and `1`. A value of `1` gives 100% opacity, `0.2` gives 20% opacity, etc.

#### 2D Model[​](https://docs.mapsindoors.com/display-rules#2d-model) <a href="#2d-model" id="2d-model"></a>

{% hint style="info" %}
Please note that this functionality needs to be enabled by MapsPeople. You can contact your MapsPeople representative to have the 2D model functionality enabled for your Solution(s).
{% endhint %}

<figure><img src="https://docs.mapsindoors.com/img/cms/2d-models.png" alt=""><figcaption></figcaption></figure>

2D Models are a way of including images on the map, and customising their appearance. They are uploaded using the Media Library.

The size of the 2D Models on the map follow the zoom levels, as opposed to how icons work, which always keep their size. The most common use case is to have logos for exhibitors, stores, or the like on the map.

1. **Visibility** - Controls whether the 2D model is visible on the map.
   * The system will accept a Boolean here, so either `true` or `false`.
2. **Zoom from** - Sets the minimum Zoom Level at which the 2D model is visible.
   * The value must be a number between 1 and 999, with 1 being very far away, and 999 acting as "max zoom". Google Maps only supports up to zoom level 22 at most, with Mapbox supporting up to level 25. Setting a value above these, your Polygon will not show up on the map.
3. **Zoom to** - Sets the maximum Zoom Level at which the 2D model is visible.
   * The value must be a number between 1 and 999, with 1 being very far away, and 999 acting as "max zoom". Google Maps only supports up to zoom level 22 at most, with Mapbox supporting up to level 25.
   * The recommended setting is to click the "Max zoom" checkbox, which sets the value to 999 in the data.
4. **2D Model** - Open the Media Library, where you can choose which 2D model to display.
   * The Media Library is a tool to select the displayed Icon from either a pre-loaded selection of Icons, or for you to upload your own.
   * In-app, you can provide a URL to a desired 2D Model.
5. **Width x Height** - Controls the width and height of the 2D model, measured in meters. These values cannot be changed individually, the 2D model must maintain its original aspect ratio.
   * "Fit to Location" is a great option for quickly setting a Width and Height that makes the 2D Model take up as much space as possible in the polygon.&#x20;
     * Note that the "anchor point" is the deciding factor here. For Locations where the anchor point has not been moved (the place where the icon is shown inside the polygon), it will be computed to be in the center of the polygon. This is also the point that is used for the calculation of where to place the 2D Model and have it take as much space as possible — if the anchor point is close to an edge of the polygon, there will not be a lot of space as there would be if it was placed at the center.&#x20;
6. **Bearing (rotation)** - Controls the rotation of the 2D model. Measured in degrees, like a compass bearing. In the illustration below, the entered value would be 45, as the image would be placed at a 45 degree bearing.

![An illustration that demonstrates how to interpret bearing](https://docs.mapsindoors.com/img/cms/one\_way\_bearing\_compass.png)

### 3D Walls[​](https://docs.mapsindoors.com/display-rules#3d-walls) <a href="#3d-walls" id="3d-walls"></a>

{% hint style="warning" %}
[Please visit this page](https://docs.mapsindoors.com/3d-maps) to see what the requirements are to use the 3D functionality in MapsIndoors.
{% endhint %}

<figure><img src="https://docs.mapsindoors.com/img/cms/3d-walls.png" alt=""><figcaption></figcaption></figure>

The MapsIndoors CMS gives you the options for to display your map in 3D. This is achieved by visualising Rooms as Walls and Extrusions. The appearance of these can be determined by these Display Rule configurations.

1. **Visibility** - Controls whether the 3D Walls are visible on the map.
   * The system will accept a Boolean here, so either `true` or `false`.
2. **Zoom from** - Sets the minimum Zoom Level at which the 3D Walls are visible.
   * The value must be a number between 1 and 999, with 1 being very far away, and 999 acting as "max zoom". Mapbox supports up to level 25. Setting a value above these, your 3D Walls will not show up on the map.
3. **Zoom to** - Sets the maximum Zoom Level at which the 3D walls are visible.
   * The value must be a number between 1 and 999, with 1 being very far away, and 999 acting as "max zoom". Mapbox supports up to level 25.
   * The recommended setting is to click the "Max zoom" checkbox, which sets the value to 999 in the data.
4. **Wall color** - Controls the color of the 3D Walls.
   * In the CMS, you can select a color using the color picker displayed when clicking the color input field.
   * If setting the color in-app, the value provided must be in 6-digit HEX code (eg. #3071D9).
5. **Wall height** - Controls the height of the 3D Walls, measured in meters.

### 3D Room Extrusion[​](https://docs.mapsindoors.com/display-rules#3d-room-extrusion) <a href="#3d-room-extrusion" id="3d-room-extrusion"></a>

{% hint style="warning" %}
[Please visit this page](https://docs.mapsindoors.com/3d-maps) to see what the requirements are to use the 3D functionality in MapsIndoors.
{% endhint %}

<figure><img src="https://docs.mapsindoors.com/img/cms/3d-room-extrusion.png" alt=""><figcaption></figcaption></figure>

The MapsIndoors CMS gives you the options for to display your map in 3D. This is achieved by visualising Rooms as Walls and Extrusions. The appearance of these can be determined by these Display Rule configurations.

1. **Visibility** - Controls whether the Extrusion is visible on the map.
   * The system will accept a Boolean here, so either `true` or `false`.
2. **Zoom from** - Sets the minimum Zoom Level at which the Extrusion is visible.
   * The value must be a number between 1 and 999, with 1 being very far away, and 999 acting as "max zoom". Mapbox supports up to level 25. Setting a value above these, your 3D Walls will not show up on the map.
3. **Zoom to** - Sets the maximum Zoom Level at which the Extrusion is visible.
   * The value must be a number between 1 and 999, with 1 being very far away, and 999 acting as "max zoom". Mapbox supports up to level 25.
   * The recommended setting is to click the "Max zoom" checkbox, which sets the value to 999 in the data.
4. **Extrusion color** - Controls the color of the Extrusion.
   * In the CMS, you can select a color using the color picker displayed when clicking the color input field.
   * If setting the color in-app, the value provided must be in 6-digit HEX code (eg. #3071D9).
5. **Extrusion height** - Controls the height of the Extrusion, measured in meters.

### 3D models[​](https://docs.mapsindoors.com/display-rules#3d-models) <a href="#3d-models" id="3d-models"></a>

{% hint style="warning" %}
[Please visit this page](https://docs.mapsindoors.com/3d-maps) to see what the requirements are to use the 3D functionality in MapsIndoors.
{% endhint %}

<figure><img src="https://docs.mapsindoors.com/img/cms/3d-model-display-rules.webp" alt=""><figcaption></figcaption></figure>

3D Models are essential for creating a great 3D map experience. They are uploaded using the Media Library.

1. **Visibility** - Controls whether the 3D Model is visible on the map.
   * The system will accept a Boolean here, so either `true` or `false`.
2. **Zoom from** - Sets the minimum Zoom Level at which the Extrusion is visible.
   * The value must be a number between 1 and 999, with 1 being very far away, and 999 acting as "max zoom". Mapbox supports up to level 25. Setting a value above these, your 3D Walls will not show up on the map.
3. **Zoom to** - Sets the maximum Zoom Level at which the Extrusion is visible.
   * The value must be a number between 1 and 999, with 1 being very far away, and 999 acting as "max zoom". Mapbox supports up to level 25.
   * The recommended setting is to click the "Max zoom" checkbox, which sets the value to 999 in the data.
4. **3D Model** - Open the Media Library, where you can choose which 3D model to display.
   * The Media Library is a tool to upload and select the model you want to show on the map.
   * In-app, you can provide a URL to a desired model.
5. **X-axis rotation** - Controls the rotation of the model on the X-axis.
   * This value is the rotation on a given axis measured in degrees, and can be any value between 0 and 360.
6. **Y-axis rotation** - Controls the rotation of the model on the Y-axis.
   * This value is the rotation on a given axis measured in degrees, and can be any value between 0 and 360.
7. **Z-axis rotation** - Controls the rotation of the model on the Z-axis.
   * This value is the rotation on a given axis measured in degrees, and can be any value between 0 and 360.
8. **Scale** - Control the scale of the model. Can be used to make the model larger or smaller on the map.
   * This value is a multiplier in relation to the original size of the uploaded model.

## Conclusion[​](https://docs.mapsindoors.com/display-rules#conclusion) <a href="#conclusion" id="conclusion"></a>

Putting all this together, a sample set of Display Rules for a given Type could look like this (the values shown here are identical to the default Main Display Rule):

```

"displayRule": {
    "visible": true,
    "iconVisible": true,
    "zoomFrom": 16.0,
    "zoomTo": 22.0,
    "icon": "https://app.mapsindoors.com/mapsindoors/cms/assets/icons/misc/default-marker.png?71488",
    "iconScale": 1.0,
    "iconSize": {
        "width": 20.0,
        "height": 20.0
    },
    "labelVisible": true,
    "label": "{{name}}",
    "labelZoomFrom": 16.0,
    "labelZoomTo": 22.0,
    "labelMaxWidth": 0, //0 means infinite eg lines will not be broken based on width
    "polygon": {
        "visible": false,
        "zoomFrom": 18,
        "zoomTo": 22,
        "strokeWidth": 2.0,
        "strokeColor": "#3071D9",
        "strokeOpacity": 1.0,
        "fillColor": "#3071D9",
        "fillOpacity": 0.2
    },
    "walls": { 
        "visible": true,
        "color": "#707a89",
        "height": 2.0,
        "zoomFrom": 16.0,
        "zoomTo": 22.0
    },
    "extrusion": {
        "visible": true,
        "color": "#aeb9cb",
        "height": 2.25,
        "zoomFrom": 16.0,
        "zoomTo": 22.0
    },
    "model3D": { 
        "visible": true,
        "zoomFrom": 16.0,
        "zoomTo": 22.0,
        "model": null,
        "rotationX": 0.0,
        "rotationY": 0.0,
        "rotationZ": 0.0
    },
    "model2D": {
        "visible": true,
        "zoomFrom": 16.0,
        "zoomTo": 22.0,
        "model": null,
        "widthMeters": 0,
        "heightMeters": 0,
        "bearing":0
    }
}
```

All properties are optional.

| Properties              | Type    | Validation      | Description                                                                                                                                                                                                                                           |
| ----------------------- | ------- | --------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `visible`               | boolean |                 | Must be true for the location to be shown in the map. If not true all other parameters are ignored.                                                                                                                                                   |
| `iconVisible`           | boolean |                 | Must be `true` for the icon to be visible in the map. If this is not `true` the `zoomFrom`, `zoomTo`, `iconUrl`, `iconScale` and `iconSize` parameters are ignored.                                                                                   |
| `zoomFrom`              | number  | `1`-`999`       | The minimum zoom level the image/icon will be visible on the map.                                                                                                                                                                                     |
| `zoomTo`                | number  | `1`-`999`       | The maximum zoom level the image/icon will be visible on the map.                                                                                                                                                                                     |
| `icon`                  | string  |                 | A URL to an image to represent the Location on the map.                                                                                                                                                                                               |
| `iconScale`             | double  | `> 0`           | ⚠️ Deprecated                                                                                                                                                                                                                                         |
| `iconSize`              | object  |                 | Specifies the size that the image will appear on the map. `{{ "{width: number, height: number" }}}`.                                                                                                                                                  |
| `iconSize.width`        | double  | `> 0`           |                                                                                                                                                                                                                                                       |
| `iconSize.height`       | double  | `> 0`           |                                                                                                                                                                                                                                                       |
| `labelVisible`          | boolean |                 | Controls the visibility of the label.                                                                                                                                                                                                                 |
| `label`                 | string  |                 | Descriptive text for the Location. This can either be a static text or a dynamic text, retrieved from a property on the Location, using double curly braces as delimiters. E.g. `"{{ "{{ name " }}}}"` or a combination `"Room: {{ "{{ name " }}}}"`. |
| `labelZoomFrom`         | number  | `1`-`999`       | The maximum zoom level the image/icon will be visible on the map.                                                                                                                                                                                     |
| `labelZoomTo`           | number  | `1`-`999`       | The maximum zoom level the image/icon will be visible on the map.                                                                                                                                                                                     |
| `labelMaxWidth`         | double  | `>= 0`          | In pixels. `0` represents an unlimited max length.                                                                                                                                                                                                    |
| `polygon`               | object  |                 | Everything under this parameter apply only to locations that have polygon data defined such as Rooms and Areas, but not POIs.                                                                                                                         |
| `polygon.visible`       | boolean |                 | Must be `true` for the polygon to be visible on the map. If this is not `true` all parameters under polygon are ignored.                                                                                                                              |
| `polygon.zoomFrom`      |         | `1`-`999`       | The lowest zoom level where the polygon will be shown.                                                                                                                                                                                                |
| `polygon.zoomTo`        |         | `1`-`999`       | The highest zoom level where the polygon will be shown. This number must be higher than `polygon.zoomFrom` unless `polygon.zoomFrom` is equal to the highest available zoom level for the Solution (this is `22` in some cases).                      |
| `polygon.strokeWidth`   |         | `>= 0`          | The width of the outline of the polygon.                                                                                                                                                                                                              |
| `polygon.strokeColor`   |         | Valid hex color | The color of the outline of the polygon.                                                                                                                                                                                                              |
| `polygon.strokeOpacity` |         | `0`-`1`         | The opacity of the outline of the polygon. Set this to `0` if only the interior is to be shown.                                                                                                                                                       |
| `polygon.fillColor`     |         | Valid hex color | The color of the interior of the polygon.                                                                                                                                                                                                             |
| `polygon.fillOpacity`   |         | `0`-`1`         | The color of the interior of the polygon. Set this to 0 if only the outline is to be shown.                                                                                                                                                           |

For further guides on how to use Display Rules in Practice, see [the following guides](https://docs.mapsindoors.com/display-rules-in-practice/).
