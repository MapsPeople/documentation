# Turn Off Collisions Based on Zoom Level

When using the MapsIndoors SDK, the system for detecting collisions will sometimes, at high zoom levels, result in the Labels of POI's that are close together being hidden, no matter what you do. Here we present a small workaround, so you can disable collisions for specific zoom levels.

> Please note that on Web it is only possible to do this when using **Mapbox** as a map provider.

This is accomplished by checking if there is a `zoom_changed` event, and if there is, enabling or disabling the `text-allow-overlap` depending on the zoom levels.

```
mapView.on('zoom_changed', () => {
    if (mi.getZoom() < mi.getMaxZoom() - 1) {
        mi.getMap().setLayoutProperty('MI_POINT_LAYER', 'text-allow-overlap', true);
    } else {
        mi.getMap().setLayoutProperty('MI_POINT_LAYER', 'text-allow-overlap', false);
    }
});
```

[\
](https://docs.mapsindoors.com/location-details)
