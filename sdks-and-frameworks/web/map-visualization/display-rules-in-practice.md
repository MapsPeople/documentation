---
description: Web v4
---

# Display Rules in Practice

here are two ways to change the appearance of the map content in MapsIndoors and Google Maps.

* Using Display Rules
* Using Google Maps styling (or Mapbox for Android and Web)

Each has its own purpose which will be explained below.

To get an overview of what Display Rules are and can be used for, read the [Display Rules](../../../products/cms/display-rules.md) page first.

### Changing the Appearance When the User Clicks a Location[​](https://docs.mapsindoors.com/display-rules-in-practice#changing-the-appearance-when-the-user-clicks-a-location) <a href="#changing-the-appearance-when-the-user-clicks-a-location" id="changing-the-appearance-when-the-user-clicks-a-location"></a>

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

### Change the Label for All Locations for the Type `PRINTER`[​](https://docs.mapsindoors.com/display-rules-in-practice#change-the-label-for-all-locations-for-the-type-printer) <a href="#change-the-label-for-all-locations-for-the-type-printer" id="change-the-label-for-all-locations-for-the-type-printer"></a>

```javascript
mapsIndoors.setDisplayRule('PRINTER',  {
    label: "{{ "Printer: {{ name " }}}}"
});
```

### Change the Label for a Single Location[​](https://docs.mapsindoors.com/display-rules-in-practice#change-the-label-for-a-single-location) <a href="#change-the-label-for-a-single-location" id="change-the-label-for-a-single-location"></a>

```javascript
mapsIndoors.setDisplayRule('c66dccd480624c428ea5b78d',  {
    label: "{{ "Printer: {{ name " }}}}"
});
```

### Apply the Same Display Rule to Multiple Locations[​](https://docs.mapsindoors.com/display-rules-in-practice#apply-the-same-display-rule-to-multiple-locations) <a href="#apply-the-same-display-rule-to-multiple-locations" id="apply-the-same-display-rule-to-multiple-locations"></a>

```javascript
mapsIndoors.setDisplayRule(['c66dccd480624c428ea5b780', 'c66dccd480624c428ea5b79c','c66dccd480624c428ea5b76a', ...], {
    icon: "https://app.mapsindoors.com/mapsindoors/cms/assets/icons/building-icons/printer.png"
});
```

### Reset the Display Rule Back to Default[​](https://docs.mapsindoors.com/display-rules-in-practice#reset-the-display-rule-back-to-default) <a href="#reset-the-display-rule-back-to-default" id="reset-the-display-rule-back-to-default"></a>

```javascript
mapsIndoors.setDisplayRule('PRINTER', null);
```

```javascript
mapsIndoors.setDisplayRule('c66dccd480624c428ea5b78d', null);
```

```javascript
mapsIndoors.setDisplayRule(['c66dccd480624c428ea5b780', 'c66dccd480624c428ea5b79c','c66dccd480624c428ea5b76a', ...], null);
```
