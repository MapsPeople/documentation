---
description: >-
  This page will give you an introduction on how to configure the Map Template
  using the URL Query Parameters.
---

# Query Parameters

The Map Template supports using query parameters for all the properties provided by the `MapsIndoorsMap` component.&#x20;

The supported query parameters are the following:&#x20;

1. `apiKey` - Used like this `apiKey=yourApiKey`. If no apiKey is provided, the app will default to `3ddemo`.
2. `venue` - Used like this `venue=yourVenueName`. If no venue is provided, the app will select the first venue from the solution in alphabetical order.
3. `locationId` - Used like this `locationId=yourLocationId`
4. `primaryColor` - Used like this `primaryColor=000000`. **Note!** You need to provide a hex color value, **without** the `#`, due to the hashtag being a reserved symbol that has a predefined purpose in a query string. If no primary color is provided, the app will default to the MapsPeople brand color.
5. `logo` - Used like this `logo=https://images.g2crowd.com/uploads/product/image/social_landscape/social_landscape_7a75ff13f42605422950b411ab7e03b5/mapspeople.png`. Use an image address to provide a different logo on the loading screen. If no logo is provided, the app will default to the MapsPeople icon.&#x20;
6. `appUserRoles` -  Used like this `appUserRoles=visitor,staff,security`. **Note!** You need to provide a list of comma separated values, **without** any spaces between the comma and the value. This will further be converted into an array of appUserRoles.&#x20;
7. `directionsFrom` - Used like this `directionsFrom=yourOriginLocationId`.
8. `directionsTo` - Used like this `directionsTo=yourDestinationLocationId`.
9. `externalIDs` - Used like this `externalIDs=0.0.1,0.0.2,0.0.3`. **Note!** You need to provide a list of comma separated values, **without** any spaces between the comma and the value. This will further be converted into an array of external IDs. Because of the way browsers work, you **cannot** use External IDs with the `,`, `&`, `#` and `+`, character in them, as they are interpreted by the browser in a particular way.
10. `tileStyle` - Used like this `tileStyle=yourTileStyleName`. If no tile style is provided, the app will show the default tile style.&#x20;
11. `mapboxAccessToken` - Used like this `mapboxAccessToken=yourMapboxAccessToken`. If no mapboxAccessToken is provided, the app will default to the access token in the `.env` file. If both the mapboxAccessToken and the gmApiKey are present, the app will load a Mapbox map.
12. `gmApiKey` - Used like this `gmApiKey=yourGmApiKey`. If no gmApiKey is provided, the app will default to the access token in the `.env` file. If both the mapboxAccessToken and the gmApiKey are present, the app will load a Mapbox map.
13. `startZoomLevel` - Used like this `startZoomLevel=22`.

&#x20;

**Note!** All the query parameters need to be separated with the `&` symbol, **without** any spaces in between.

&#x20;

Example of URL:&#x20;

`https://domain.com/?apiKey=yourApiKey&venue=yourVenueName&locationId=yourLocationId&primaryColor=000000&logo=https://images.g2crowd.com/uploads/product/image/social_landscape/social_landscape_7a75ff13f42605422950b411ab7e03b5/mapspeople.png&appUserRoles=visitor,staff,security`

&#x20;

**Important!** Not all the query parameters can be used together, as they serve their own purpose which in some cases overlaps with other query parameters. Example of cases that **DON’T** work together:

1. `locationId` + `startZoomLevel` → the `locationId` has priority over the `startZoomLevel`
2. `locationId` + `externalIDs` → the `locationId` has priority over the `externalIDs`
3. `directionsTo` + `directionsFrom` + `locationId` → the `directionsTo` + `directionsFrom` have priority over the `locationId`
4. `directionsTo` + `directionsFrom` + `externalIDs` → the `directionsTo` + `directionsFrom` have priority over the `externalIDs`
