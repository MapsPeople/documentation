# How to change the state DisplayRules

**This article assumes that you have already read the Getting Started guide.**

The state DisplayRules controls how Locations are displayed on the map in different states For example, you can change the icon scale of a Location when it is hovered over or highlight a search result. The state DisplayRules gives access to the same properties as the regular [DisplayRules](https://app.mapsindoors.com/mapsindoors/js/sdk/latest/docs/DisplayRule.html), which can be used to control the appearance of Locations.

### Available states

<table><thead><tr><th width="152">State</th><th>Description</th></tr></thead><tbody><tr><td>hover</td><td>The state when the user hovers over a Location.</td></tr><tr><td>highlight</td><td>The state when Locations are programmatically highlighted using the <code>mapsIndoors.highlight()</code> method.</td></tr><tr><td>selection</td><td>The state when the user has selected a Location by clicking on it.</td></tr><tr><td>hoverHighlight</td><td>The state when the user hovers over a highlighted Location.</td></tr><tr><td>hoverSelection</td><td>The state when the user hovers over a selected Location.</td></tr></tbody></table>

To change the state DisplayRules, you can access the Solution Config object using the `mapsIndoors.getSolutionConfig()` method. Once you have the Solution Config object, you can access the state DisplayRules using the `solutionConfig.stateDisplayRules` property.

**Example:**

```javascript
// Get the Solution Config object.
const solutionConfig = mapsIndoors.getSolutionConfig();

// Get the hover state DisplayRule.
const hoverDisplayRule = solutionConfig.stateDisplayRules.hover;

// Set the icon scale to 2. This will result in the icon being scaled to double size on hover.
hoverDisplayRule.iconScale = 2;

// Update the SolutionCofig to apply the changes.
mapsIndoors.setSolutionConfig(solutionConfig);
```

<figure><img src="../../../.gitbook/assets/how-to-change-the-state-display-rules_hover.png" alt=""><figcaption><p>An example of the hover state DisplayRule in action</p></figcaption></figure>

### Hover

The default SDK behavior is to scale the icon and lighten the fill and stroke color of the polygon and extrusion.

**Default values for the hover DisplayRule:**

```json
{
    'iconScale': 1.25,
    'polygonLightnessFactor': 0.2,
    'extrusionLightnessFactor': 0.2,
    'badgeScale': 1.25
}
```

The lightnessFactor is used to lighten the fill and stroke color of both the polygon and the extrusion by 20%.

#### Highlight and Selection

The `hoverHighlight` and `hoverSelection` is two separate state DisplayRules to configure the appearance of highlighted or selected locations when hovered.

**Default values for the hoverHighlight DisplayRule:**

```json
{
    'zoomFrom': 15.0,
    'zoomTo': 999,
    'iconScale': 1.25,
    'polygonZoomFrom': 15.0,
    'polygonZoomTo': 999,
    'polygonLightnessFactor': 0.4,
    'extrusionZoomFrom': 15.0,
    'extrusionZoomTo': 999,
    'extrusionLightnessFactor': 0.4,
    'badgeVisible': true,
    'badgeZoomFrom': 15,
    'badgeZoomTo': 999,
    'badgeRadius': 6,
    'badgeStrokeWidth': 4.0,
    'badgeStrokeColor': '#fafafa',
    'badgeFillColor': '#ec4899',
    'badgeScale': 1.25
};
```

**Default values for the hoverSelection DisplayRule:**

```json
{
    'zoomFrom': 0.0,
    'zoomTo': 999,
    'visible': true,
    'iconVisible': true,
    'icon': 'https://app.mapsindoors.com/mapsindoors/gfx/select-pin.png',
    'iconScale': 1.25,
    'iconPlacement': 'ABOVE',
    'iconSize': {
        'width': 24.0,
        'height': 28.0
    },
    'polygonZoomFrom': 15.0,
    'polygonZoomTo': 999,
    'polygonLightnessFactor': 0.4,
    'extrusionZoomFrom': 15.0,
    'extrusionZoomTo': 999,
    'extrusionLightnessFactor': 0.4
}
```

### Highlight

The default SDK behaiour is to add a small badge to the upper left corner of the Location icon and ensure visibility by setting the zoomFrom and zoomTo values, and the fill and stroke color of the polygon and extrusion.

**Default values for the highlight DisplayRule:**

```json
{
    'zoomFrom': 15.0,
    'zoomTo': 999,
    'polygonZoomFrom': 15.0,
    'polygonZoomTo': 999,
    'polygonLightnessFactor': 0.2,
    'extrusionZoomFrom': 15.0,
    'extrusionZoomTo': 999,
    'extrusionLightnessFactor': 0.2,
    'badgeVisible': true,
    'badgeZoomFrom': 15,
    'badgeZoomTo': 999,
    'badgeRadius': 6,
    'badgeStrokeWidth': 4.0,
    'badgeStrokeColor': '#fafafa',
    'badgeFillColor': '#ec4899',
    'badgeScale': 1
};
```

**Highlight all Meeting Rooms:**

```javascript
// Get the LocationsService object.
const locationsService = mapsindoors.services.LocationsService;

// Get all Locations of the type "Meeting Room".
const meetingRoomLocations = await locationsService.getLocations({ types: ['MeetingRoom'] });

// Highlight the Locations.
mapsIndoors.highlight(meetingRoomLocations.map(location => location.id));
```

<figure><img src="../../../.gitbook/assets/how-to-change-the-state-display-rules_highlight.png" alt=""><figcaption><p>An example of the highlight state DisplayRule in action</p></figcaption></figure>

**Clear the highlight:**

```javascript
mapsIndoors.highlight([]);
```

### Selection

The selection state is for changing the appearance of a single Location, for example when the user clicks on it. To select a Location, call the mapsIndoors.selectLocation() method, passing in the Location object as the parameter.

**Default values for the selection DisplayRule:**

```json
{
    'zoomFrom': 0.0,
    'zoomTo': 999,
    'visible': true,
    'iconVisible': true,
    'icon': 'https://app.mapsindoors.com/mapsindoors/gfx/select-pin.png',
    'iconScale': 1.0,
    'iconPlacement': 'ABOVE',
    'iconSize': {
        'width': 24.0,
        'height': 28.0
    },
    'polygonZoomFrom': 15.0,
    'polygonZoomTo': 999,
    'polygonLightnessFactor': 0.2,
    'extrusionZoomFrom': 15.0,
    'extrusionZoomTo': 999,
    'extrusionLightnessFactor': 0.2
}
```

**Select a Location, when the user clicks it:**

```javascript
// (location) is the Location object that is clicked by the user.
mapsIndoors.on('click', (location) => {
    mapsIndoors.selectLocation(location);
});
```

<figure><img src="../../../.gitbook/assets/how-to-change-the-state-display-rules_selection.png" alt=""><figcaption><p>An example of the selection state DisplayRule in action</p></figcaption></figure>

**Clear current selection:**

```javascript
mapsIndoors.deselectLocation();
```
