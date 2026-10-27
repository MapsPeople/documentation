---
description: >-
  Applying and overriding wayfinding route styling with DirectionsRenderer in
  the MapsIndoors web SDK.
---

# Styling the Route

The `DirectionsRenderer` decides how a calculated route looks on the map — the line itself, its animation, the direction arrows drawn along it, the markers at each stop and floor change, and the markers at the start and end.

Every route always has a style. Values are resolved in three layers, each overriding the one before it:

1. **Built-in defaults** — the SDK's baseline look (a solid blue line, 4 px wide, with a flowing animation).
2. **Solution styling** — [configured in the CMS](../../../../products/cms/directions-renderer.md) under **Solution Settings → Directions Renderer**. Every app on the Solution picks it up automatically, with no code.
3. **Runtime options** — anything you pass to the renderer in code, which sits on top of the CMS configuration and resets when the app reloads.

So the recommended path is to **style once in the CMS** and let it flow to every app. Use the runtime options below only when you need to override the Solution styling for a specific app or moment.

{% hint style="warning" %}
The renderer reads the Solution styling **once, when it is constructed**. Create the `DirectionsRenderer` _after_ the MapsIndoors instance has fired its `ready` event, or the CMS styling is silently skipped and you get the built-in defaults.
{% endhint %}

## Setting options in code

Pass styling options when you construct the renderer, or later with `setOptions()`:

```javascript
const directionsRenderer = new mapsindoors.directions.DirectionsRenderer({
    mapsIndoors: mapsIndoorsInstance,
    strokeColor: '#3071D9',
    strokeWeight: 6,
    stamp: { preset: 'arrow', arrowStyle: 'chevronDouble', color: '#FFFFFF' }
});

// …later, override just the arrow color — every other field is kept:
directionsRenderer.setOptions({ stamp: { color: '#FF0000' } });
```

Grouped options — `halo`, `stamp`, `legBoundaryIcons`, `startDisplayRule`, `endDisplayRule`, and `elevation` — **merge field by field**. In the example above, `setOptions` changes only `stamp.color`; the `preset` and `arrowStyle` set at construction are preserved. To revert a whole group back to its construction baseline, pass `undefined` for it. To turn the route animation off entirely, pass `animation: null`.

### Options reference

| Option             | Type                 | Default               | Notes                                                                                                                                                        |
| ------------------ | -------------------- | --------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| `mapsIndoors`      | `MapsIndoors`        | —                     | Required. The MapsIndoors instance.                                                                                                                          |
| `strokeColor`      | `string`             | `'#3071D9'`           | Route line color.                                                                                                                                            |
| `strokeOpacity`    | `number`             | `1`                   | `0`–`1`.                                                                                                                                                     |
| `strokeWeight`     | `number`             | `4`                   | Line width in pixels.                                                                                                                                        |
| `strokeStyle`      | `string`             | `'solid'`             | `'solid'`, `'dashed'`, or `'dotted'`. See engine support below.                                                                                              |
| `halo`             | `object`             | _(CMS)_               | `{ enabled, color, weight, opacity }`. Merges field by field.                                                                                                |
| `stamp`            | `object`             | _(CMS)_               | Direction arrows / custom stamp — see [Direction arrows](styling-the-route.md#direction-arrows). Merges field by field.                                      |
| `animation`        | `object` \| `null`   | _(default animation)_ | The route animation controller, or `null` to disable. Tune it via [Customizing the Route Animation](/broken/pages/ea1abd56341b157b1cc004ce484d9da116f65988). |
| `legBoundaryIcons` | `object`             | _(CMS)_               | Floor-change (connector) icons per type, plus a shared `scale`. Applied on the next route.                                                                   |
| `startDisplayRule` | `object`             | _(CMS)_               | Start (origin) marker: `{ icon, label, zoom range, … }`. Merges field by field.                                                                              |
| `endDisplayRule`   | `object`             | _(CMS)_               | End (destination) marker, as above.                                                                                                                          |
| `elevation`        | `object`             | _(CMS)_               | `{ elevated, heightMeters }`. See engine support below.                                                                                                      |
| `fitBounds`        | `boolean`            | `true`                | Fit the viewport to the route.                                                                                                                               |
| `fitBoundsPadding` | `number` \| `object` | —                     | Padding when fitting the viewport.                                                                                                                           |
| `fitBoundsMaxZoom` | `number`             | _(CMS)_               | Caps how far the map zooms in when fitting a route (`1`–`24`).                                                                                               |
| `visible`          | `boolean`            | `true`                | Initial visibility of the route.                                                                                                                             |

{% hint style="info" %}
`strokeColor` / `strokeWeight` / `strokeOpacity` and `animation` can be changed live with `setOptions()`. Stop-icon styling is set when the renderer is constructed (via the stop-icon provider) and is not changed by `setOptions()`.
{% endhint %}

## Direction arrows

The `stamp` option repeats a small marker along the route line to reinforce the direction of travel:

| Field        | Type               | Default     | Notes                                                                                                                                                  |
| ------------ | ------------------ | ----------- | ------------------------------------------------------------------------------------------------------------------------------------------------------ |
| `preset`     | `string`           | `'none'`    | `'none'` (no stamp) or `'arrow'` (built-in arrow).                                                                                                     |
| `arrowStyle` | `string`           | `'chevron'` | `'chevron'`, `'chevronDouble'`, `'triangle'`, `'arrow'` (CMS: "Line arrow"), or `'dart'` (CMS: "Navigation dart"). Applies when `preset` is `'arrow'`. |
| `color`      | `string`           | `'#FFFFFF'` | Tint for the built-in arrow.                                                                                                                           |
| `iconUrl`    | `string` \| `null` | `null`      | A custom icon repeated along the line — use a Media-Library URL (an absolute `https` URL).                                                             |
| `spacingPx`  | `number`           | `24`        | Distance between stamps, in pixels (`8`–`200`).                                                                                                        |
| `scale`      | `number`           | `1`         | Size multiplier on the base 24 px icon (`0.2`–`4`).                                                                                                    |

```javascript
// A double chevron in white, spaced a little wider:
directionsRenderer.setOptions({
    stamp: { preset: 'arrow', arrowStyle: 'chevronDouble', color: '#FFFFFF', spacingPx: 32 }
});

// A custom icon along the line (Mapbox):
directionsRenderer.setOptions({
    stamp: { iconUrl: 'https://app.mapsindoors.com/…/my-stamp.svg', scale: 1.5 }
});
```

A custom **`iconUrl` always wins over the arrow `preset`**: when `iconUrl` is set, the custom icon is drawn instead of the built-in arrow. Set `iconUrl` back to `null` to return to the arrow. (On Google Maps a custom icon can't render, so it falls back to the built-in arrow — see [Engine support](styling-the-route.md#engine-support).)

{% hint style="info" %}
**Designing a custom stamp.** The image is repeated along the line and **rotated to follow the direction of travel — its right-hand side points forward**, so author the icon **pointing right (→)**, the same convention as the built-in arrows. It rotates with the route rather than the screen, so it will render sideways or upside-down on legs heading left or down — use a shape that reads at any angle (arrow-like; avoid text and logos). `stamp.color` does not tint a custom image — bake the color into the file — and use a transparent background.
{% endhint %}

## Other styling

* **Halo** — `halo: { enabled, color, weight, opacity }` draws a contrasting outline beneath the line so it stays readable over busy floor plans. Off by default; when on, the halo width is added to each side, so the total width is the line weight plus twice the halo weight.
* **Animation** — a moving overlay that shows direction of travel, configured on the animation controller via `directionsRenderer.getAnimation().setOptions({ … })`. Choose the `type` (`'flow'` (default), `'pulse'`, `'comet'`, or `'none'`), the `speed` (in screen pixels per second, so it reads the same at every zoom), a minimum duration per leg, and the overlay's own colour/width/opacity. Set `forceAnimation: true` to keep it running when the device requests reduced motion — for kiosks and signage. See [Customizing the Route Animation](/broken/pages/ea1abd56341b157b1cc004ce484d9da116f65988) for more on the animation controller.
* **Stop, connector, and terminus markers** — icons at each stop, at floor changes (`legBoundaryIcons`), and at the start/end (`startDisplayRule` / `endDisplayRule`). Every route-styling icon renders at a fixed 24 px base multiplied by its `scale`, so sizes stay consistent across map engines.

{% hint style="info" %}
**Icon size & resolution.** Because icons always render at 24 px × `scale`, the source image's dimensions never change the drawn size — only its sharpness. Prefer **SVG** (crisp at any scale and pixel density). For raster icons use a **square, transparent-background PNG at ≥3× the displayed size** — 72×72 px at scale 1, up to 288×288 px at the maximum scale 4. Media-Library URLs are fetched server-resized for the screen's pixel density automatically; icons hosted elsewhere are fetched at their natural size, which makes an adequately sized source matter more there.
{% endhint %}

* **3D elevated line** — `elevation: { elevated: true, heightMeters: 10 }` lifts the route off the floor in a tilted view.

## Engine support

The five arrow styles, line/halo/opacity, animation, stop icons, connector icons, start/end markers, and the camera cap all render on **both Mapbox and Google Maps**. A few features are Mapbox-only or approximated on Google:

| Feature                                        | Mapbox              | Google Maps                                                          |
| ---------------------------------------------- | ------------------- | -------------------------------------------------------------------- |
| Line, halo, animation, arrows, markers, camera | ✅                   | ✅                                                                    |
| Dashed / dotted line                           | ✅ (Mapbox GL JS v3) | ⚠️ approximated with spaced dots; dashes can overshoot sharp corners |
| Custom stamp `iconUrl`                         | ✅                   | ❌ falls back to the built-in arrow                                   |
| 3D elevated line                               | ✅ (Mapbox GL JS v3) | ❌ renders flat                                                       |

{% hint style="warning" %}
The line **halo**, **dashed/dotted** styles, the **direction stamp**, the **start/end and connector markers**, and the **3D elevated line** require **Mapbox GL JS v3** (the 3D elevated line needs a recent v3 — v3.22 verified). On Mapbox GL JS v1/v2 the route falls back to a plain colored line with animation, stop icons, and the camera cap. Google-engine limitations (custom icon → arrow; 3D → flat) are silent — there is no console warning.
{% endhint %}

> See all available directions render options in the [reference documentation](https://app.mapsindoors.com/mapsindoors/js/sdk/latest/docs/mapsindoors.directions.DirectionsRenderer.html).

For configuring the same styling without code — so it applies to every app on the Solution — see [Solution Settings → Directions Renderer](/broken/pages/186b8038d55c575046ee24b5985826968b190298#directions-renderer) in the CMS.
