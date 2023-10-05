---
description: >-
  This page will give you an introduction on how to get started using the Map
  Template as a Web Component. In this guide you will learn how to install and
  use the Web Component step by step.
---

# Web Component

### Using NPM

Install the package:

`npm install @mapsindoors/map-template`

In your script:

```
import MapsIndoorsMap from '@mapsindoors/map-template/dist/mapsindoors-webcomponent.es.js';
window.customElements.define('mapsindoors-map', MapsIndoorsMap);
```

In your styles, make sure to give it a size. For example:

```
mapsindoors-map {
    display: block;
    width: 100vw;
    height: 100vh;
}
```

Make sure the MapsIndoors JavaScript SDK is loaded by having this somewhere in your HTML:

```
<script src="https://app.mapsindoors.com/mapsindoors/js/sdk/4.21.5/mapsindoors-4.21.5.js.gz"></script>
```

Use the web component in your HTML:

```
<mapsindoors-map></mapsindoors-map>
```

Add attributes to the web component as needed (see supported properties below).

### Using just the browser

```
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>My Map</title>
    <script src="https://app.mapsindoors.com/mapsindoors/js/sdk/4.21.5/mapsindoors-4.21.5.js.gz"></script>
    <script type="module">
        import MapsindoorsMap from 'https://www.unpkg.com/@mapsindoors/map-template/dist/mapsindoors-webcomponent.es.js';
        window.customElements.define('mapsindoors-map', MapsIndoorsMap)
    </script>
    <style>
        body {
            margin: 0;
        }
        mapsindoors-map {
            display: block;
            width: 100vw;
            height: 100vh;
        }
    </style>
</head>
<body>
    <mapsindoors-map></mapsindoors-map>
</body>
</html>
```

Add attributes to the web component as needed ([see list](broken-reference)).
