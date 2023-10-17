# Web Component

This page will give you an introduction on how to get started using the Map Template as a Web Component. In this guide you will learn how to install and use the Web Component step by step.

### Using NPM

Install the package:

`npm install @mapsindoors/map-template`

In your script:

```
import MapsIndoorsMap from '@mapsindoors/map-template@stable/dist/mapsindoors-webcomponent.es.js';
window.customElements.define('mapsindoors-map', MapsIndoorsMap);
```

In your styles, make sure to give it a size. For example:

```
mapsindoors-map {
    display: block;
    width: 100vw;
    height: 100svh;
}
```

Use the Web Component in your HTML:

```
<mapsindoors-map></mapsindoors-map>
```

Add attributes to the Web Component as needed (see supported properties below).

**Note!** The `external-IDs` and `app-user-roles` expect an array, which in a Web Component is handled differently (see example below).

In your script, define the array of external IDs that you want to be shown on the Map Template. Then get a hold of the `mapsIndoorsMapElement` using the `document.querySelector()` method. When you have the `mapsIndoorsMapElement`, assign its prop `externalIDs` the array of External IDs that you defined at the beginning.

```javascript
const externalIDsArray = ["externalID-1", "externalID-2"]
const mapsIndoorsMapElement = document.querySelector('mapsindoors-map')
mapsIndoorsMapElement.externalIDs = externalIDsArray;
```

Use query parameters to configure the Web Component by setting the `supports-url-parameters` attribute to `true`.

### Using just the browser

```
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>My Map</title>
    <script type="module">
        import MapsindoorsMap from '@mapsindoors/map-template@stable/dist/mapsindoors-webcomponent.es.js';
        window.customElements.define('mapsindoors-map', MapsIndoorsMap)
    </script>
    <style>
        body {
            margin: 0;
        }
        mapsindoors-map {
            display: block;
            width: 100vw;
            height: 100svh;
        }
    </style>
</head>
<body>
    <mapsindoors-map></mapsindoors-map>
</body>
</html>
```

Add attributes to the Web Component as needed (see supported properties above).([see list](broken-reference/)).

Use query parameters to configure the Web Component by setting the `supports-url-parameters` attribute to `true`.
