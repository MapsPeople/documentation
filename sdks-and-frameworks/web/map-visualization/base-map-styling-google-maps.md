# Base Map Styling - Google Maps

External business POIs from the base map can clutter up your map with irrelevant content. This article describes how to hide unwanted features from the base map - and how to change the colours to better match your branding guidelines.



<figure><img src="../../../.gitbook/assets/image (19).png" alt=""><figcaption><p>Use styling to control or hide visual features like the business POIs on the right hand side</p></figcaption></figure>



With Google Maps there are two methods to style the base map. Both style methods can be used across JavaScript, iOS and Android for a consistent look across platforms and apps.



**Google Maps Cloud Styling**

Cloud-based maps styling makes it easy to style, customize, and manage your maps using the Google Cloud Console, letting you create a customized map experience for your users without having to update your apps' code each time you make a style change.

_**Warning - paid feature:** Functionality accessed by adding a `map ID` triggers a map load charged against the Dynamic Maps SKU for **Android** and **iOS**. See_ [_Google Maps Billing_](https://developers.google.com/maps/billing-and-pricing/pricing#dynamic-maps) _for more information. Use of Cloud Styling with JavaScript does not add any additional cost._

Follow the guidelines linked below to create a Style and associate it with a `map ID`.

{% embed url="https://developers.google.com/maps/documentation/cloud-customization/overview" %}
Feature documentation from Google Maps
{% endembed %}

To use a map ID with MapsIndoors, simply add the `mapId` parameter alongside other options in [GoogleMapsView](https://app.mapsindoors.com/mapsindoors/js/sdk/latest/docs/mapsindoors.mapView.GoogleMapsView.html)&#x20;

```javascript
// main.js

const mapViewOptions = {
  element: document.getElementById('map'),
  // your other map options here
  mapId: "8e0a97af9386fef",
};
const mapViewInstance = new mapsindoors.mapView.GoogleMapsView(mapViewOptions);
```

\
**Google Maps JSON Style (legacy method)**

Use a JSON style declaration to control both colours and visual display of features like roads, parks and other points of interest. You can also hide features entirely.

The Styling Wizard can be an easy way to generate a JSON style:

{% embed url="https://mapstyle.withgoogle.com/" %}
Google Maps Styling Wizard (legacy)
{% endembed %}

To use a JSON style with MapsIndoors, simply add the styles array in [GoogleMapsView](https://app.mapsindoors.com/mapsindoors/js/sdk/latest/docs/mapsindoors.mapView.GoogleMapsView.html). Here is an example hiding all Google-supplied POIs:

```javascript
// main.js

const mapViewOptions = {
  element: document.getElementById('map'),
  // your other map options here
  styles: [
  {
    "featureType": "poi",
    "stylers": [
      {
        "visibility": "off"
      }
    ]
  }
  ],
};
const mapViewInstance = new mapsindoors.mapView.GoogleMapsView(mapViewOptions);
```
