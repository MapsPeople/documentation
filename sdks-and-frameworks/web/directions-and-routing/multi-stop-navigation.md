# Multi-stop navigation

This article builds on existing knowledge and assumes familiarity with the [Directions Service](directions-service/) and the [Directions Renderer](directions-renderer/).

The MapsIndoors Javascript SDK empowers you to streamline navigation within venues using the multi-stop feature. This functionality allows you to obtain directions to multiple destinations within a venue effortlessly. Provide a sequence of stops, and MapsIndoors will generate the most efficient route, considering two options:

* **Navigation with multiple stops:** Navigate through the stops in the exact order you specify. This is ideal when the order of visits is crucial.
* **Optimized multi-stop navigation (traveling salesman algorithm):** Let MapsIndoors intelligently reorder your stops to create the fastest possible route, saving you valuable time.

<figure><img src="../../../.gitbook/assets/27.06.2024_10.47.23_REC 2.gif" alt=""><figcaption><p>Navigation with multiple stops</p></figcaption></figure>

### Leverage Multi-Stop Navigation in Your App

To get a route with multiple stops along the path, use the [`DirectionsService's`](https://app.mapsindoors.com/mapsindoors/js/sdk/latest/docs/mapsindoors.services.DirectionsService.html) [`getRoute`](https://app.mapsindoors.com/mapsindoors/js/sdk/latest/docs/mapsindoors.services.DirectionsService.html#getRoute) in combination with the stops parameter. The stops parameter takes an array of [`LatLngLiterals`](https://app.mapsindoors.com/mapsindoors/js/sdk/latest/docs/LatLngLiteral.html)

```javascript
const origin = { lat: 30.362364120965957, lng: -97.74102144956545 };
const destination = { lat: 30.3603809, lng: -97.7421568, floor: 0 };

const stops = [
    { lat: 30.3603751, lng: -97.7420869, floor: 0 },
    { lat: 30.3604412, lng: -97.7421172, floor: 0 },
    { lat: 30.3604593, lng: -97.7422238, floor: 0 },
];

const routeResult = await miDirectionsServiceInstance.getRoute({
    origin, 
    destination, 
    stops
});

miDirectionsRendererInstance.setRoute(routeResult);
```

<figure><img src="../../../.gitbook/assets/image (43).png" alt=""><figcaption><p>Route with multiple stops, in the given input order.</p></figcaption></figure>

### **Optimized multi-stop navigation**

To optimize the route for the most direct path, you can use the `optimize` parameter. When set to `true`, this parameter ensures that the stops are ordered to optimize the travel time.

```javascript
const origin = { lat: 30.362364120965957, lng: -97.74102144956545 };
const destination = { lat: 30.3603809, lng: -97.7421568, floor: 0 };
const stops = [
    { lat: 30.3603751, lng: -97.7420869, floor: 0 },
    { lat: 30.3604412, lng: -97.7421172, floor: 0 },
    { lat: 30.3604593, lng: -97.7422238, floor: 0 },
];

const routeResult = await miDirectionsServiceInstance.getRoute({
    origin, 
    destination, 
    stops,
    // Optimize the route for the fastest travel time
    optimize: true
});

miDirectionsRendererInstance.setRoute(routeResult);
```

<figure><img src="../../../.gitbook/assets/image (44).png" alt=""><figcaption><p>Route with multiple stops, optimized for shortest travel time.</p></figcaption></figure>

### Customizing all Route Stop Icons

The MapsIndoors Javascript SDK offers customization options to tailor the appearance of your multi-stop route. By default, MapsIndoors provides a standard icon to visually represent each stop on your route. However, you can override the [`DefaultRouteStopIconProvider`](https://app.mapsindoors.com/mapsindoors/js/sdk/latest/docs/mapsindoors.directions.DefaultRouteStopIconProvider.html) on the [`DirectionsRenderer`](https://app.mapsindoors.com/mapsindoors/js/sdk/latest/docs/mapsindoors.directions.DirectionsRenderer.html) to create a more customized experience.

To customize the icons used for route stops, you can provide a [`DefaultRouteStopIconProvider`](https://app.mapsindoors.com/mapsindoors/js/sdk/latest/docs/mapsindoors.directions.DefaultRouteStopIconProvider.html). For example, to set the fill color to blue (`#00f`):

```javascript
const routeStopIconProvider = new mapsindoors.directions.DefaultRouteStopIconProvider({
    fillColor: '#00f' 
}); 

const directionsRendererOptions = { 
    mapsIndoors: mapsIndoorsInstance,
    defaultRouteStopIconProvider: routeStopIconProvider
};
const miDirectionsRendererInstance = new mapsindoors.directions.DirectionsRenderer(directionsRendererOptions);
```

<figure><img src="../../../.gitbook/assets/image (46).png" alt=""><figcaption><p>Stop icons with a different background color.</p></figcaption></figure>

To omit the numbering of the icons, set the `numbered` boolean parameter to `false`:

```javascript
const routeStopIconProvider = new mapsindoors.directions.DefaultRouteStopIconProvider({
    fillColor: '#00f',
    numbered: false
}); 

const directionsRendererOptions = { 
    mapsIndoors: mapsIndoorsInstance,
    defaultRouteStopIconProvider: routeStopIconProvider
};

const miDirectionsRendererInstance = new mapsindoors.directions.DirectionsRenderer(directionsRendererOptions);
```

<figure><img src="../../../.gitbook/assets/image (47).png" alt=""><figcaption><p>Stop icons without numbers.</p></figcaption></figure>

### Adding Labels to Route Stops

The MapsIndoors Javascript SDK allows you to enhance the visual representation of your multi-stop routes by incorporating labels beneath each stop icon. This can provide additional context or information about the stop for users.

To add labels to your stops, create a Map of [`RouteStopConfig`](https://app.mapsindoors.com/mapsindoors/js/sdk/latest/docs/RouteStopConfig.html) objects, using the stop index as the key, and the RouteStopConfig as the value. The [`RouteStopConfig`](https://app.mapsindoors.com/mapsindoors/js/sdk/latest/docs/RouteStopConfig.html) offers a `label` property that you can set to the desired text for the stop label.

```javascript
const routeStopConfigs = new Map([
    [0, { label: 'John\'s desk' }], 
    [1, { label: 'Meeting room 4' }], 
    [2, { label: 'Jane\'s desk' }]
]);

miDirectionsRendererInstance.setRoute(routeResult, routeStopConfigs);
```

<figure><img src="../../../.gitbook/assets/image (45).png" alt=""><figcaption><p>Numbered stop icons with labels displayed underneath.</p></figcaption></figure>

### Customizing Individual Stop Icons

The [`RouteStopConfig`](https://app.mapsindoors.com/mapsindoors/js/sdk/latest/docs/RouteStopConfig.html) object also lets you configure a different [`RouteStopIconProvider`](https://app.mapsindoors.com/mapsindoors/js/sdk/latest/docs/RouteStopIconProvider.html) for the individual stops. The [`DefaultRouteStopIconProvider`](https://app.mapsindoors.com/mapsindoors/js/sdk/latest/docs/mapsindoors.directions.DefaultRouteStopIconProvider.html) class allows customizing the fill color of the default icon as shown previously. The [`RouteStopConfig`](https://app.mapsindoors.com/mapsindoors/js/sdk/latest/docs/RouteStopConfig.html) has the `iconProvider` property, which can be used to override the [`DirectionsRenderer`](https://app.mapsindoors.com/mapsindoors/js/sdk/latest/docs/mapsindoors.directions.DirectionsRenderer.html)'s [`DefaultRouteStopIconProvider`](https://app.mapsindoors.com/mapsindoors/js/sdk/latest/docs/mapsindoors.directions.DefaultRouteStopIconProvider.html) for the individual stops.

Here is an example of how to use the [`DefaultRouteStopIconProvider`](https://app.mapsindoors.com/mapsindoors/js/sdk/latest/docs/mapsindoors.directions.DefaultRouteStopIconProvider.html) to customize the background color of specific stop icons:

```javascript
const redStopIconProvider = new mapsindoors.directions.DefaultRouteStopIconProvider({
    fillColor: '#f00' // Red background color
});

const greenStopIconProvider = new mapsindoors.directions.DefaultRouteStopIconProvider({
    fillColor: '#0f0', // Green background color
    numbered: false,
});

const routeStopConfigs = new Map([
    [0, { label: 'John\'s desk',  iconProvider: redStopIconProvider}], 
    [1, { label: 'Meeting room 4', iconProvider: greenStopIconProvider }],
    [2, { label: 'Jane\'s desk' }]
]);

miDirectionsRendererInstance.setRoute(routeResult, routeStopConfigs);
```

<figure><img src="../../../.gitbook/assets/image (49) (1).png" alt=""><figcaption><p>Stop icons with different background colors.</p></figcaption></figure>
