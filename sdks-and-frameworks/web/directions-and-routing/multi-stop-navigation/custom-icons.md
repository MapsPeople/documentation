# Custom Icons

### **Creating Custom Stop Icons**

Ready to add a touch of personality to your multi-stop routes? Let's dive into the world of custom stop icons! This article dives into the world of custom stop icon design for multi-stop routes within the MapsIndoors Javascript SDK. It explains how to leverage the [`RouteStopIconProvider`](https://app.mapsindoors.com/mapsindoors/js/sdk/latest/docs/RouteStopIconProvider.html) interface to craft unique icons that perfectly match your application's style and branding.

### **The RouteStopIconProvider Interface**

The [`RouteStopIconProvider`](https://app.mapsindoors.com/mapsindoors/js/sdk/latest/docs/RouteStopIconProvider.html) interface acts as a blueprint for creating custom stop icons. While the MapsIndoors SDK provides a default implementation (DefaultRouteStopIconProvider), this interface empowers you to take control and design stop icons beyond the built-in options.

The core functionality of [`RouteStopIconProvider`](https://app.mapsindoors.com/mapsindoors/js/sdk/latest/docs/RouteStopIconProvider.html) revolves around a single asynchronous function called [`getImage`](https://app.mapsindoors.com/mapsindoors/js/sdk/latest/docs/RouteStopIconProvider.html#getImage). It accepts an optional parameter named `stopNumber`. This parameter provides the actual stop number (not the index) within the route and is intended to display the stop number directly on the custom stop icon. This allows you to create numbered icons that visually communicate the stop sequence within the route.

The [`getImage`](https://app.mapsindoors.com/mapsindoors/js/sdk/latest/docs/RouteStopIconProvider.html#getImage) function returns a Promise that resolves to an [`HTMLImageElement`](https://developer.mozilla.org/en-US/docs/Web/API/HTMLImageElement/Image) representing the custom stop icon that will be displayed on the map. It likely utilizes libraries like Canvas or SVG to generate the icon image.

### **Implementing a Custom RouteStopIconProvider**

Now that we understand the core concept, let's see how to implement a custom [`RouteStopIconProvider`](https://app.mapsindoors.com/mapsindoors/js/sdk/latest/docs/RouteStopIconProvider.html) class. Here's a basic example:

```javascript
/**
 * Custom route stop icon provider.
 */
class CustomRouteStopIconProvider {
    /**
     * Gets the icon for the specified stop number.
     *
     * @param {number} [stopNumber] - The stop number.
     * @returns {Promise<HTMLImageElement>} - The icon image.
     */
    async getImage(stopNumber) {
        return new Promise((resolve) => {
            // This example creates a simple circle icon with a number
            const width = 64;
            const height = width;
            const canvas = document.createElement('canvas');
            canvas.width = width;
            canvas.height = height;
            const context = canvas.getContext('2d');

            context.beginPath();
            context.arc(width / 2, height / 2, width / 2 - 2, 0, 2 * Math.PI);
            context.strokeStyle = '#fff'; // Set your desired stroke color here
            context.lineWidth = 4;
            context.fillStyle = '#00f'; // Set your desired fill color here
            context.fill();
            context.stroke();
            
            if (Number.isInteger(stopNumber)) {
                context.fillStyle = '#fff';
                context.font = 'bold 28px sans-serif';
                context.textAlign = 'center';
                context.textBaseline = 'middle';
                context.fillText(stopNumber, width / 2, height / 2);
            }

            const img = new Image(canvas.width, canvas.height);
            img.onload = () => resolve(img);
            img.src = canvas.toDataURL();
        });
    }
}
```

This example creates a simple blue circle with a white number overlay representing the stop number. You can customize this logic to generate any desired icon based on your application's needs. The code utilizes the Canvas API to create a circular icon and conditionally adds the stop number in the center.

By following this approach and leveraging the [`RouteStopIconProvider`](https://app.mapsindoors.com/mapsindoors/js/sdk/latest/docs/RouteStopIconProvider.html) interface, you can create unique and informative stop icons that enhance the visual experience of your MapsIndoors multi-stop routes.

### Using the CustomRouteStopIconProvider

```javascript
const redStopIconProvider = new mapsindoors.directions.DefaultRouteStopIconProvider({
    fillColor: '#f00' // Red background color
});

const greenStopIconProvider = new mapsindoors.directions.DefaultRouteStopIconProvider({
    fillColor: '#0f0', // Green background color
    numbered: false,
});

const customRouteStopIconProvider = new CustomRouteStopIconProvider();

const routeStopConfigs = new Map([
    [0, { label: 'John\'s desk',  iconProvider: redStopIconProvider}], 
    [1, { label: 'Meeting room 4', iconProvider: greenStopIconProvider }],
    [2, { label: 'Jane\'s desk', iconProvider: customStopIconProvider }]
]);

miDirectionsRendererInstance.setRoute(routeResult, routeStopConfigs);
```

<figure><img src="../../../../.gitbook/assets/image (53).png" alt=""><figcaption><p>Multi-stop route with a custom icon.</p></figcaption></figure>
