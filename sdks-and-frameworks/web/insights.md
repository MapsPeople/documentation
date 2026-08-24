---
hidden: true
noIndex: true
---

# Insights

## Configure Insights event logging (Web SDK)

{% hint style="danger" %}
**Internal draft — not public.** Insights v1 is not a public feature yet. Do not add this page to the public GitBook space, the docs sitemap, or any customer-facing changelog until the release is announced. Share this file directly (or paste it into a private Google Doc) with the customer that needs it.
{% endhint %}

This page explains how to configure the MapsIndoors **Web SDK** so Insights receives the v1 event taxonomy with a correct `viewVariant`.

Every Insights event carries a `viewVariant`. Insights slices counts and top lists by that field. The SDK cannot tell a kiosk from a desktop browser — the host app must set it. If you never set it, the platform breakdown is wrong and **cannot be backfilled**.

{% hint style="warning" %}
`viewVariant` is write-once from Insights' point of view. Events that arrive without it stay unattributed. There is no retroactive correction.
{% endhint %}

### Prerequisites

Before any Insights data will show up:

1. **Web SDK 4.58.6 or later.** 4.58.5 shipped the v1 event names and the analytics context API. 4.58.6 added `venue_loaded`. Use the [current release](https://app.mapsindoors.com/mapsindoors/js/sdk/latest/docs/MapsIndoors.html) (4.59.2 at the time of writing).
2. **The Insights module is enabled on the solution.** If the Insights dashboard is missing in the CMS, ask MapsPeople to enable the module. Event logging on the solution must also be on — the SDK only sends events when the solution allows it.
3. **Do not disable SDK event logging.** Event logging is on by default. Calling `mapsindoors.MapsIndoors.disableEventLogging(true)` stops every Insights event.

```html
<script src="https://app.mapsindoors.com/mapsindoors/js/sdk/4.59.2/mapsindoors-4.59.2.js.gz"></script>
```

### Set the analytics context (`viewVariant`)

Call `setAnalyticsContext` on the `MapsIndoors` instance immediately after you construct it. The only supported key is `viewVariant`.

Allowed values are exposed as `mapsindoors.MapsIndoors.AnalyticsViewVariants`:

| Constant  | Value     | Use when                            |
| --------- | --------- | ----------------------------------- |
| `DESKTOP` | `desktop` | Desktop- or laptop-sized browser UI |
| `MOBILE`  | `mobile`  | Phone-sized **browser** UI          |
| `KIOSK`   | `kiosk`   | Kiosk or signage deployment         |

{% hint style="info" %}
**`mobile` means a phone-sized browser — not a native app.** The iOS and Android SDKs send **no** `viewVariant`. Do not set `mobile` to represent native traffic, and do not expect native events to appear in the Web platform breakdown.
{% endhint %}

The SDK does not infer the variant from the user agent, viewport, or map provider. A kiosk running a desktop Chrome window still needs `kiosk`. A responsive web app that switches to a phone layout needs `mobile`.

#### Basic setup

This matches the published Web SDK API (`MapsIndoors.setAnalyticsContext`, `MapsIndoors.AnalyticsViewVariants`):

```javascript
<script src="https://api.mapbox.com/mapbox-gl-js/v3.28.1/mapbox-gl.js"></script>
<link href="https://api.mapbox.com/mapbox-gl-js/v3.28.1/mapbox-gl.css" rel="stylesheet" />

mapsindoors.MapsIndoors.setMapsIndoorsApiKey('YOUR_MAPSINDOORS_API_KEY');
const mapViewInstance = new mapsindoors.mapView.MapboxV3View({
    accessToken: 'YOUR_MAPBOX_ACCESS_TOKEN',
    element: document.getElementById('map'),
    center: { lat: 30.36022358949809, lng: -97.74204591828197 },
    zoom: 17
});
const mapsIndoorsInstance = new mapsindoors.MapsIndoors({
    mapView: mapViewInstance,
    venue: 'YOUR_MAPSINDOORS_VENUE_ID'
});
mapsIndoorsInstance.setAnalyticsContext({
    viewVariant: mapsindoors.MapsIndoors.AnalyticsViewVariants.DESKTOP
});
```

`configureAnalytics({ context })` is a convenience wrapper around the same call:

```javascript
mapsIndoorsInstance.configureAnalytics({
    context: {
        viewVariant: mapsindoors.MapsIndoors.AnalyticsViewVariants.KIOSK
    }
});
```

Call it again if the host UI actually changes variant (for example a responsive app crossing a phone-sized breakpoint, or a build that can run as kiosk or desktop). Repeated calls shallow-merge; only `viewVariant` is stored.

```javascript
function resolveViewVariant() {
    const { DESKTOP, MOBILE, KIOSK } = mapsindoors.MapsIndoors.AnalyticsViewVariants;

    if (window.MI_KIOSK === true) {
        return KIOSK;
    }

    // Phone-sized browser — not a native SDK.
    if (window.matchMedia('(max-width: 767px)').matches) {
        return MOBILE;
    }

    return DESKTOP;
}

mapsIndoorsInstance.setAnalyticsContext({
    viewVariant: resolveViewVariant()
});
```

`clearAnalyticsContext()` removes the stored variant. Subsequent events go out without one. Do not call it in production unless you are tearing the instance down.

#### Validation and errors

The public API rejects invalid input:

* An unknown `viewVariant` throws. Allowed values: `desktop`, `mobile`, `kiosk`.
* Any key other than `viewVariant` throws.

```javascript
// Throws: Invalid "viewVariant" value "tv"
mapsIndoorsInstance.setAnalyticsContext({ viewVariant: 'tv' });

// Throws: Unsupported analytics context key(s): sessionId
mapsIndoorsInstance.setAnalyticsContext({ viewVariant: 'desktop', sessionId: 'abc' });
```

Read back the allowed constants with `mapsIndoorsInstance.getAnalyticsViewVariants()` (returns `{ DESKTOP: 'desktop', MOBILE: 'mobile', KIOSK: 'kiosk' }`).

#### Timing: set context within one second

Bootstrap lifecycle events (`sdk_loaded`, `mapsindoors_instantiated`, `map_instantiated`, `venue_loaded`) wait briefly so `setAnalyticsContext` can run first. They flush as soon as a valid `viewVariant` is set, or after **1 second** without one.

Set the context in the same turn as `new mapsindoors.MapsIndoors(...)`. If the constructor and `setAnalyticsContext` are more than a second after the SDK script has loaded and enabled logging, those bootstrap events are sent **without** `viewVariant` and cannot be repaired.

### v1 events and their triggers

The Web SDK emits these events automatically. You do not register a custom Insights logger. You do need to go through the SDK APIs listed below — a custom search box or a click handler that never calls `selectLocation` will not produce the corresponding event.

`viewVariant` is attached to every event once context is set. `venueId` is promoted to a top-level field when the SDK can resolve it.

#### App lifetime

| Event                      | Trigger                                                                                                                                                                                                                                       |
| -------------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `sdk_loaded`               | The SDK script has loaded and event logging is enabled.                                                                                                                                                                                       |
| `mapsindoors_instantiated` | `new mapsindoors.MapsIndoors(...)`                                                                                                                                                                                                            |
| `map_instantiated`         | A map view is created (`GoogleMapsView`, `MapboxView`, or `MapboxV3View`).                                                                                                                                                                    |
| `venue_loaded`             | The map settles on a venue. Debounced by 3 seconds so the startup default venue is replaced by the venue the app actually opens. Re-setting the same venue is ignored; a later change to a different venue emits again. Requires **4.58.6+**. |
| `sdk_unloaded`             | The browser is unloading the page (`beforeunload`).                                                                                                                                                                                           |
| `entered_foreground`       | The browser window receives focus.                                                                                                                                                                                                            |
| `entered_background`       | The browser window loses focus.                                                                                                                                                                                                               |

#### Map and search

| Event               | Trigger                                                                                                                                                                                                        |
| ------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `location_clicked`  | `mapsIndoorsInstance.selectLocation(location)` is called for a location with an id. Typical source: the map `click` listener.                                                                                  |
| `search_finalized`  | `mapsindoors.services.LocationsService.getLocations({ q: '...' })` is called. Debounced **2 seconds** after the last search. Payload includes `query`, `result_count`, and `venue_id` when it can be resolved. |
| `location_searched` | A location that originated from a search is selected: either `selectLocation(location, { source: 'search' })`, or `selectLocation` on a location returned by `getLocations` with a `q` parameter.              |

{% hint style="warning" %}
A map `click` listener that only calls `goTo(location)` does **not** emit `location_clicked`. Call `selectLocation(location)` (you can still `goTo` afterwards).
{% endhint %}

```javascript
mapsIndoorsInstance.on('click', (location) => {
    mapsIndoorsInstance.selectLocation(location);
    mapsIndoorsInstance.goTo(location);
});

// Search: getLocations with `q` emits search_finalized (after 2s).
// Selecting a result emits location_clicked and location_searched.
const results = await mapsindoors.services.LocationsService.getLocations({
    q: 'coffee',
    venue: mapsIndoorsInstance.getVenue()?.id
});

mapsIndoorsInstance.selectLocation(results[0], { source: 'search' });
```

`location_clicked` / `location_searched` include `location_id`, `type_admin_id`, `location_type`, `building_id`, `category_keys`, and `venue_id` when those fields exist on the location.

#### Directions

Directions events are emitted by `mapsindoors.directions.DirectionsRenderer`, using a route from `mapsindoors.services.DirectionsService.getRoute(...)`.

| Event                  | Trigger                                                                                                                                                                                                                                                                         |
| ---------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `directions_started`   | `directionsRenderer.setRoute(route)` for a route the Directions Service tagged with origin/destination context. Includes `origin_location_id`, `destination_location_id`, `origin_venue_id` / `destination_venue_id` when resolved, `venue_id`, and `route_duration` (seconds). |
| `directions_completed` | Navigation ends: `directionsRenderer.setRoute(null)`, or an explicit `directionsRenderer.completeDirectionsUsageTracking()`. Adds `usage_duration` (seconds the route was shown) and `usage_percentage` (0–100, from how far the user stepped through the route).               |

`DirectionsRenderer.destroy()` does **not** emit `directions_completed`. Call `completeDirectionsUsageTracking()` first if you destroy the renderer while a route is active.

```javascript
const directionsService = new mapsindoors.services.DirectionsService();
const directionsRenderer = new mapsindoors.directions.DirectionsRenderer({
    mapsIndoors: mapsIndoorsInstance
});

const route = await directionsService.getRoute({
    origin: { lat: 57.0582, lng: 9.9505, floor: 0 },
    destination: { lat: 57.0584, lng: 9.9510, floor: 1 },
    travelMode: 'WALKING'
});

await directionsRenderer.setRoute(route); // directions_started

// When the user finishes or dismisses wayfinding:
await directionsRenderer.setRoute(null); // directions_completed
```

#### Configuration and Live Data

| Event                      | Trigger                                      |
| -------------------------- | -------------------------------------------- |
| `language_changed`         | `mapsindoors.MapsIndoors.setLanguage(...)`   |
| `livedata_enabled_for_map` | `liveDataManager.enableLiveData(domainType)` |
| `subscription_started`     | `liveDataManager.subscribe(topic)`           |
| `subscription_stopped`     | `liveDataManager.unsubscribe(topic)`         |

Live Data events include `domain_type`.

#### Names that changed in v1

If you are comparing older logs or docs, these were renamed in 4.58.5:

| Before 4.58.5          | v1 name                |
| ---------------------- | ---------------------- |
| `directions_requested` | `directions_started`   |
| `directions_rendered`  | `directions_completed` |
| `map_clicked`          | `location_clicked`     |
| `search_performed`     | `search_finalized`     |

`location_searched` and `venue_loaded` are new.

### How to verify

Insights is not real-time.

1. Deploy a build that uses **4.58.6+** and calls `setAnalyticsContext` with the correct `viewVariant`.
2. Exercise the flows you care about: open the map (venue), click a POI (`selectLocation`), run a text search (`getLocations` with `q`) and select a result, start and clear a route (`setRoute` / `setRoute(null)`).
3. Wait about **one hour**. The Insights importer runs at **five minutes past each hour**. The hour that just closed is imported shortly after `:05`; the current (trailing) bucket is **provisional** and can still change on the next run.
4. Open the **Insights dashboard in the CMS** and confirm:
   * Counts move after the next importer run.
   * The platform / view-variant breakdown shows `desktop`, `mobile`, or `kiosk` as you set — not an empty or mixed bucket you did not intend.
   * Search, location, and directions widgets reflect the actions you performed.

If the dashboard is empty after two importer runs: confirm the Insights module is on the solution, you are not calling `disableEventLogging(true)`, and the SDK version in the page is 4.58.6 or later. If counts exist but the platform split is empty or all-desktop, `setAnalyticsContext` was missing, late (bootstrap events flushed after 1 second), or set to the wrong variant — those events cannot be rewritten.

### API reference (Web SDK)

| Method                                                     | Role                                                    |
| ---------------------------------------------------------- | ------------------------------------------------------- |
| `mapsIndoorsInstance.setAnalyticsContext({ viewVariant })` | Sets the variant attached to every subsequent event.    |
| `mapsIndoorsInstance.configureAnalytics({ context })`      | Wrapper around `setAnalyticsContext`.                   |
| `mapsIndoorsInstance.getAnalyticsViewVariants()`           | Returns `{ DESKTOP, MOBILE, KIOSK }`.                   |
| `mapsindoors.MapsIndoors.AnalyticsViewVariants`            | Same constants as a static.                             |
| `mapsIndoorsInstance.clearAnalyticsContext()`              | Clears the stored variant.                              |
| `mapsindoors.MapsIndoors.disableEventLogging(true)`        | Stops all event logging. Leave this unset for Insights. |

JSDoc: [MapsIndoors class](https://app.mapsindoors.com/mapsindoors/js/sdk/latest/docs/MapsIndoors.html).
