# Remove Labels from Buildings and Venues

**Are your building and venue labels disrupting your user experience?**

Traditionally, both Buildings and Venues are treated as Locations in the Web SDK, inherently displaying Labels which might not always align with the desired user experience. A conventional method to manage their visibility is illustrated below via a [**`setDisplayRule`**](https://app.mapsindoors.com/mapsindoors/js/sdk/latest/docs/mapsindoors.MapsIndoors.html#setDisplayRule)

It can be a best practice to set this display rule after initializing your MapsIndoors instance.

```javascript
mapsIndoorsInstance.setDisplayRule(['MI_BUILDING', 'MI_VENUE'], { visible: false });
```

Here, `MI_BUILDING` and `MI_VENUE` are specific Location Types dedicated to facilitate display rule settings for Buildings and Venues respectively.

**Enhancing User Interaction with Adaptive Zoom-Level Popups**

<figure><img src="../../.gitbook/assets/zoom_popup_venue_building (1).gif" alt=""><figcaption><p>Dynamically adding a Mapbox Popup based on zoom level</p></figcaption></figure>

Let's say a user zooms out to get a broad overview of a large campus or city area. At a lower zoom level, the display becomes less cluttered, paving the way for a more clear, bird's eye view of the venues available. Here’s where dynamic, zoom-dependent [**popups**](https://docs.mapbox.com/mapbox-gl-js/example/popup/) come into play, serving as intuitive markers and information windows. This example is specific to Mapbox, for Google Maps you will need to use [**info windows**](https://developers.google.com/maps/documentation/javascript/infowindows).

Integrating the following code snippet enables the application to listen for changes in zoom level, and depending on the zoom threshold, dynamically fetch venues and exhibit crisp, stylish popups at each venue’s anchor point:

```javascript
let popups = [];  

mapsIndoorsInstance.addListener('zoom_changed', (zoomLevel) => {
    if (zoomLevel < 19) {
        mapsindoors.services.VenuesService.getVenues().then(venues => {
            venues.forEach(venue => {
                const anchor = venue.anchor.coordinates;
                const popup = new mapboxgl.Popup({ closeOnClick: true, closeButton: false, className: 'custom-popup' })
                    .setLngLat([anchor[0], anchor[1]])
                    .setHTML(`<h2>${venue.name}</h2>`)
                    .addTo(mapsIndoorsInstance.getMap());
                popups.push(popup);
            });
        });
    } else {
        popups.forEach(popup => popup.remove());
        popups = [];
    }
});
```

**What does this achieve?**&#x20;

When the map zoom level is below 19, unobtrusive popups appear, providing users with a quick overview of each venue’s name. Upon zooming in beyond the level 19, the popups discreetly vanish, preventing any visual obstruction and facilitating an unhindered exploration of the MapsIndoors map.

