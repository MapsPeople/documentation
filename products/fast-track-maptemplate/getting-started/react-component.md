# React Component

This page will give you an introduction on how to get started using the Map Template as a React Component. In this guide you will learn how to install and use the React Component step by step.

### Using NPM

Install the package:

`npm install @mapsindoors/map-template@stable`

Use the `MapsIndoorsMap` component in a React component:

```jsx
import MapsIndoorsMap from '@mapsindoors/map-template/dist/mapsindoors-react.es.js';

// Somewhere in your JSX:
<div style={{
      display: 'block',
      width: '100vw',
      height: '100dvh'
}}>
      <MapsIndoorsMap></MapsIndoorsMap>
</div>
```

Add properties to the MapsIndoorsMap component as needed ([see list](../configuration/)).

Use query parameters to configure the MapsIndoorsMap component by setting the `supportsUrlParameters` prop to `true`  in your `MapsIndoorsMap` component.

