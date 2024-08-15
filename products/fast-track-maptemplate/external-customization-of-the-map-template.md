# External customization of the Map Template

When using the Map Template as a React Component or as a Web Component, you can customize the application by accessing the MapsIndoors instance. To do this, listen for the `mapsIndoorsInstanceAvailable` event on the `window` object.&#x20;

You can read about all the methods that can be used on the MapsIndoors Instance [here](https://app.mapsindoors.com/mapsindoors/js/sdk/latest/docs/mapsindoors.MapsIndoors.html).

1. ### React Component

To use the MapsIndoors instance within a React Component, you can create a `useEffect` hook that listens for the `mapsIndoorsInstanceAvailable` event on the `window` object.

<pre class="language-jsx"><code class="lang-jsx">import MapsIndoorsMap from '@mapsindoors/map-template/dist/mapsindoors-react.es.js';
import { useEffect } from 'react';

function App() {

    useEffect(() => {
<strong>        window.addEventListener('mapsIndoorsInstanceAvailable', () => {
</strong>             window.mapsIndoorsInstance.setDisplayRule('yourLocationId', { 'polygonFillColor': '#ff69b4' })
        })
    }, [])

    return (
        &#x3C;div>
            &#x3C;MapsIndoorsMap apiKey="yourApiKey" mapboxAccessToken="yourMapboxAccessToken" />
        &#x3C;/div>
    )
}

export default App;
</code></pre>



2. ### Web Component

To use the MapsIndoors instance within a Web Component, include a script tag that listens for the `mapsIndoorsInstanceAvailable` event on the `window` object.

```html
<body>
    <mapsindoors-map api-key="yourApiKey" mapbox-access-token="yourMapboxAccessToken"></mapsindoors-map>
    <script>
        window.addEventListener('mapsIndoorsInstanceAvailable', () => {
             window.mapsIndoorsInstance.setDisplayRule('yourLocationId', { 'polygonFillColor': '#ff69b4' })
        })
    </script>
</body>
```

In conclusion, integrating the Map Template as either a React Component or a Web Component allows for great customization through the MapsIndoors instance.&#x20;

By listening for the `mapsIndoorsInstanceAvailable` event, you can easily access and manipulate various features of the map, enhancing the functionality and user experience of your application.&#x20;

Whether you prefer working within a React framework or using the standard web components, the flexibility of the MapsIndoors instance will enable you to achieve your goals effectively.&#x20;

Explore the available methods and tailor your map to fit your specific needs, ensuring a seamless and dynamic interaction for your users [here](https://app.mapsindoors.com/mapsindoors/js/sdk/latest/docs/mapsindoors.MapsIndoors.html).
