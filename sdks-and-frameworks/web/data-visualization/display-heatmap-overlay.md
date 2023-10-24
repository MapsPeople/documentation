# Display Heatmap Overlay

If you use Mapbox as your map engine, it provides the option of creating a heatmap as an alternative way to display datapoints on your map. The MapsIndoors SDK also provides support for integrating this heatmap into the MapsIndoors layers.

### Creating a heatmap[​](https://docs.mapsindoors.com/display-heatmap#creating-a-heatmap) <a href="#creating-a-heatmap" id="creating-a-heatmap"></a>

This guide will focus on how to display the heatmap layer in a visually pleasing way in your MapsIndoors solution. In order to actually create your heatmap, please refer to the documentation from Mapbox.

* Creating a heatmap with Mapbox: [https://docs.mapbox.com/help/tutorials/make-a-heatmap-with-mapbox-gl-js/](https://docs.mapbox.com/help/tutorials/make-a-heatmap-with-mapbox-gl-js/)
* Creating a heatmap layer with Mapbox: [https://docs.mapbox.com/mapbox-gl-js/example/heatmap-layer/](https://docs.mapbox.com/mapbox-gl-js/example/heatmap-layer/)

### Inserting the heatmap layer between MapsIndoors layers[​](https://docs.mapsindoors.com/display-heatmap#inserting-the-heatmap-layer-between-mapsindoors-layers) <a href="#inserting-the-heatmap-layer-between-mapsindoors-layers" id="inserting-the-heatmap-layer-between-mapsindoors-layers"></a>

Once you have created your heatmaps with Mapbox, you will need a way to get it to work with the layers that MapsIndoors already applies to your Mapbox map. By following the guides above, you should be able to simply overlay it on top of everything. But in order for it to be integrated more seamlessly in the MapsIndoors layers, you could also choose to insert it between the tile layer and the layer containing the area polygons.

In order to insert a heatmap between layers on the web SDK, refer to the [Mapbox GL JS API Reference](https://docs.mapbox.com/mapbox-gl-js/api/map/#map#addlayer). Then use the following code snippet but replace `map` with your Mapbox instance, and insert the relevant parameters from the API Reference:

```javascript
map.addLayer({....}, 'MI_POLYGON_LAYER');
```
