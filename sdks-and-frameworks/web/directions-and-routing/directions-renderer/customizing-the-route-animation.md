# Customizing the Route Animation

The `DirectionsRenderer` includes functionality to display an animated line that traces the route's path when displayed. You have control over the visual style and timing of this animation.

#### Accessing the Route Animation Controller

To customize the animation, you first need to access the dedicated animation controller object from your `DirectionsRenderer` instance. Use the `getAnimation()` method for this:

JavaScript

```javascript
// Assuming 'directionsRenderer' is your initialized DirectionsRenderer instance
// (See the main Directions Renderer page for setup)

const routeAnimation = directionsRenderer.getAnimation();
```

#### Setting Animation Options

The `routeAnimation` object has a `setOptions()` method that allows you to configure the animation's properties. Pass an object to this method where the keys match the options you want to modify.

These options are defined by the `mapsindoors.directions.RouteAnimationOptions` interface:

**Interface:** `mapsindoors.directions.RouteAnimationOptions`

| Property           | Type     | Default Value          | Description                                                                                                                                                                                                         |
| ------------------ | -------- | ---------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `speed`            | `number` | `300`                  | The speed of the animation in meters per second (m/s).                                                                                                                                                              |
| `minAnimationTime` | `number` | `1.2`                  | The minimum duration in seconds for the animation. Ensures very short routes still have a perceivable animation effect.                                                                                             |
| `strokeColor`      | `string` | `'hsl(215, 67%, 96%)'` | The color of the animated line. Accepts any valid CSS color value (e.g., `'#ff0000'`, `'rgba(0, 255, 0, 0.8)'`, `'navy'`). See [MDN CSS color value](https://developer.mozilla.org/en-US/docs/Web/CSS/color_value). |
| `strokeOpacity`    | `number` | `1`                    | The opacity of the animated line, from `0.0` (fully transparent) to `1.0` (fully opaque). See [MDN CSS opacity](https://developer.mozilla.org/en-US/docs/Web/CSS/opacity).                                          |
| `strokeWeight`     | `number` | `2`                    | The thickness (weight) of the animated line in pixels.                                                                                                                                                              |

Example

This example shows how to create a `DirectionsRenderer` and immediately configure its animation to be a thicker, red line with a slightly faster speed.

JavaScript

```javascript
// Assuming 'mapsIndoors' and 'stopIconProvider' are defined as needed

// 1. Initialize the DirectionsRenderer (as described on the main page)
const directionsRenderer = new mapsindoors.directions.DirectionsRenderer({
    mapsIndoors: mapsindoors,
    fitBoundsPadding: 200,
    defaultRouteStopIconProvider: stopIconProvider,
});

// 2. Get the route animation controller
const routeAnimation = directionsRenderer.getAnimation();

// 3. Set desired animation options
routeAnimation.setOptions({
    strokeColor: '#E41E26', // A distinct red color
    strokeWeight: 6,        // Make the line noticeably thicker
    speed: 450              // Increase the animation speed
});

// 4. Later, when you render a route...
// directionsRenderer.setRoute(myRoute);
// ...the animation will use the custom settings defined above.
```

By calling `setOptions()` on the object returned by `getAnimation()` (here stored in `routeAnimation`), you can tailor the route reveal animation to better match your application's design or desired user experience. These settings will be applied the next time a route is rendered using `setRoute()`.
