---
icon: browser
cover: ../.gitbook/assets/web-app-banner.jpg
coverY: 0
---

# Web App

You can implement MapsIndoors as a native SDK or framework, or access it as a hosted web application via the MapsIndoors CMS. This allows you to embed the map in your solution using an iframe or share the direct link (map.mapsindoors.com/**my-indoor-map**) with colleagues, customers, and others.

<figure><img src="../.gitbook/assets/web-app.png" alt=""><figcaption><p>The MapsIndoors Web App</p></figcaption></figure>

## Configuration

Under the solution that you want to deploy a Web App for, navigate to :wrench: **Solution Details** → **App Settings** → [**App Configuration**,](https://cms.mapsindoors.com/app-settings/app-config) and you'll find the configuration for your MapsIndoors Web App.&#x20;

Here, you can configure how your web app should behave regarding language, styling, load view, categories, and more.&#x20;

<figure><img src="../.gitbook/assets/cms-config (1).png" alt=""><figcaption><p>App configuration page</p></figcaption></figure>

The most important factor here is the **Map Provider** section. You need to input either your Mapbox Access Token or your Google Maps API key. Once you have successfully entered a key/token, you'll be able to open your Web App, ready for you and your customers to use.&#x20;

{% hint style="info" %}
If you don't have a Mapbox token/Google Maps API key yet, [go here](../sdks-and-frameworks/web/tutorial/getting-started/map-engine-provider/)
{% endhint %}

### Styling and further configuration

The following overview covers what can be configured from the MapsIndoors CMS. Some parameters are still not configurable through the CMS and must be added as URL parameters. You can find an extensive overview of all configurable parameters [here](fast-track-maptemplate/configuration/query-parameters.md).

<table><thead><tr><th>Parameters</th><th>Configurable in CMS</th><th data-hidden></th></tr></thead><tbody><tr><td>apiKey</td><td>✅</td><td></td></tr><tr><td>venue</td><td>✅</td><td></td></tr><tr><td>locationId</td><td>❌</td><td></td></tr><tr><td>primaryColor</td><td>✅</td><td></td></tr><tr><td>logo</td><td>✅</td><td></td></tr><tr><td>appUserRoles</td><td>❌</td><td></td></tr><tr><td>directionsFrom</td><td>❌</td><td></td></tr><tr><td>directionsTo</td><td>❌</td><td></td></tr><tr><td>externalIDs</td><td>❌</td><td></td></tr><tr><td>tileStyle</td><td>❌</td><td></td></tr><tr><td>mapboxAccessToken</td><td>✅</td><td></td></tr><tr><td>gmApiKey</td><td>✅</td><td></td></tr><tr><td>startZoomLevel</td><td>✅</td><td></td></tr><tr><td>gmMapId</td><td>❌</td><td></td></tr><tr><td>pitch</td><td>✅</td><td></td></tr><tr><td>bearing</td><td>✅</td><td></td></tr><tr><td>language</td><td>✅</td><td></td></tr><tr><td>kioskOriginLocationId</td><td>❌</td><td></td></tr><tr><td>timeout</td><td>❌</td><td></td></tr><tr><td>useKeyboard</td><td>❌</td><td></td></tr><tr><td>miTransitionLevel</td><td>❌</td><td></td></tr><tr><td>category</td><td>❌</td><td></td></tr><tr><td>searchAllVenues</td><td>❌</td><td></td></tr><tr><td>useMapProviderModule</td><td>❌</td><td></td></tr><tr><td>hideNonMatches</td><td>❌</td><td></td></tr><tr><td>showRoadNames</td><td>❌</td><td></td></tr><tr><td>showExternalIDs</td><td>❌</td><td></td></tr><tr><td>searchExternalLocations</td><td>❌</td><td></td></tr><tr><td>center</td><td>✅</td><td></td></tr><tr><td>useAppTitle</td><td>❌</td><td></td></tr></tbody></table>
