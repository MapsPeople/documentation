---
description: Display Rules for the Web SDK v4
---

# Display Rules in Practice

There are two ways to change the appearance of the map content in MapsIndoors:

* Using Display Rules.
* Using Google Maps or Mapbox styling.

Each has its own purpose which will be explained below.

To get an overview of what Display Rules are and can be used for, read the [Display Rules](../../products/cms/display-rules.md) page first.

### Changing the Appearance when the User clicks on a Location

```javascript
let selectedPOI;
mapsIndoors.addListener("click", function (poi) {
    if (selectedPOI) {
        mapsIndoors.setDisplayRule(selectedPOI.id, null);
    }

    mapsIndoors.setDisplayRule(poi.id, {
        iconSize: { width: 30, height: 30 },
    });

    selectedPOI = poi;
});
```

### Change the Label for All Locations for the Type `PRINTER`

```javascript
mapsIndoors.setDisplayRule('PRINTER',  {
    label: "{{ "Printer: {{ name " }}}}"
});
```

### Change the Label for a single, specific Location

```javascript
mapsIndoors.setDisplayRule('c66dccd480624c428ea5b78d',  {
    label: "{{ "Printer: {{ name " }}}}"
});
```

### Apply the Same Display Rule to Multiple Locations

```javascript
mapsIndoors.setDisplayRule(['c66dccd480624c428ea5b780', 'c66dccd480624c428ea5b79c','c66dccd480624c428ea5b76a', ...], {
    icon: "https://app.mapsindoors.com/mapsindoors/cms/assets/icons/building-icons/printer.png"
});
```

### Reset the Display Rule Back to Default

```javascript
mapsIndoors.setDisplayRule('PRINTER', null);
```

```javascript
mapsIndoors.setDisplayRule('c66dccd480624c428ea5b78d', null);
```

```javascript
mapsIndoors.setDisplayRule(['c66dccd480624c428ea5b780', 'c66dccd480624c428ea5b79c','c66dccd480624c428ea5b76a', ...], null);
```

