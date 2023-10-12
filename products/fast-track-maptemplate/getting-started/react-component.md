---
description: >-
  This page will give you an introduction on how to get started using the Map
  Template as a React Component. In this guide you will learn how to install and
  use the React Component step by step.
---

# React Component

### Using NPM

Install the package:

`npm install @mapsindoors/map-template`

Make sure the MapsIndoors JavaScript SDK is loaded by having this somewhere in your HTML:

```
<script src="https://app.mapsindoors.com/mapsindoors/js/sdk/4.21.5/mapsindoors-4.21.5.js.gz"></script>
```

Use the `MapsIndoorsMap` component in a React component:

```
import MapsIndoorsMap from '@mapsindoors/map-template/dist/mapsindoors-react.es.js';

// Somewhere in your JSX:
<div style={{
      display: 'block',
      width: '100vw',
      height: '100vh'
}}>
      <MapsIndoorsMap></MapsIndoorsMap>
</div>
```

Add properties to the MapsIndoorsMap component as needed ([see list](broken-reference)).

Use query parameters to configure the MapsIndoorsMap component by setting the `supportsUrlParameters` prop to `true`.
