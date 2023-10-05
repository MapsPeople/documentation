---
description: Android V4
---

# Display Heatmap Overlay

If you use Mapbox as your map provider, it provides the option of creating a heatmap as an alternative way to display datapoints on your map. The MapsIndoors SDK also provides support for integrating this heatmap into the MapsIndoors layers.

### Creating a heatmap[​](https://docs.mapsindoors.com/display-heatmap#creating-a-heatmap) <a href="#creating-a-heatmap" id="creating-a-heatmap"></a>

This guide will focus on how to display the heatmap layer in a visually pleasing way in your MapsIndoors solution. In order to actually create your heatmap, please refer to the documentation from Mapbox themselves.

* Creating a heatmap with Mapbox: [https://docs.mapbox.com/help/tutorials/make-a-heatmap-with-mapbox-gl-js/](https://docs.mapbox.com/help/tutorials/make-a-heatmap-with-mapbox-gl-js/)
* Creating a heatmap layer with Mapbox: [https://docs.mapbox.com/mapbox-gl-js/example/heatmap-layer/](https://docs.mapbox.com/mapbox-gl-js/example/heatmap-layer/)

### Inserting the heatmap layer between MapsIndoors layers[​](https://docs.mapsindoors.com/display-heatmap#inserting-the-heatmap-layer-between-mapsindoors-layers) <a href="#inserting-the-heatmap-layer-between-mapsindoors-layers" id="inserting-the-heatmap-layer-between-mapsindoors-layers"></a>

Once you have created your heatmaps with your map provider, you will need a way to get it to work with the layers that MapsIndoors already applies to your Mapbox map. By following the above guides, you should be able to simply overlay it on top of everything. But in order for it to be integrated more seamlessly in the MapsIndoors layers, you could also choose to insert between the tile layer and the layer containing the area polygons.

> This will only work with Android SDK v4.0.1 and newer.

To achieve this you will use the MapsIndoors `Layers` class that comes with the Mapsindoors Mapbox dependency. It can be done like this:

```
mapboxMap.getStyle { style ->
    style.addLayerBelow(createHeatmapLayer(), Layers.AREA_FILL)
}
```

`Layers.AREA_FILL` is the lowest of the MapsIndoors layers (except for tiles, but as that is changed depending on floor indexes, it is not available through the SDK). This means the heatmap will be rendered above tiles, but underneath any other MapsIndoors-specific item on the map.

```
mapboxMap.getStyle { style ->
    style.addLayerBelow(createHeatmapLayer(), MPLayers.POI)
}
```

`Layers.POI` is the Location marker layer of the MapsIndoors layers. This means the heatmap will be rendered on top of markers, but below the positioning dot ("blue dot") and route markers.
