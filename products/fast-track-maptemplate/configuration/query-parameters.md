# Query Parameters

Why use Query Parameters?

The Map Template supports using query parameters for all the properties provided by the `MapsIndoorsMap` component.

**The supported query parameters are the following:**

1. `apiKey` - Used like this `apiKey=yourApiKey`. If no apiKey is provided, the app will default to `3ddemo`.
2. `venue` - Used like this `venue=yourVenueName`. If no venue is provided, the app will select the first venue from the solution in alphabetical order.
3. `locationId` - Used like this `locationId=yourLocationId`
4. `primaryColor` - Used like this `primaryColor=000000`. **Note!** You need to provide a hex color value, **without** the `#`, due to the hashtag being a reserved symbol that has a predefined purpose in a query string. If no primary color is provided, the app will default to the MapsPeople brand color.
5. `logo` - Used like this `logo=https://images.g2crowd.com/uploads/product/image/social_landscape/social_landscape_7a75ff13f42605422950b411ab7e03b5/mapspeople.png`. Use an image address to provide a different logo on the loading screen. If no logo is provided, the app will default to the MapsPeople icon.
6. `appUserRoles` - Used like this `appUserRoles=visitor,staff,security`. **Note!** You need to provide a list of comma separated values, **without** any spaces between the comma and the value. This will further be converted into an array of appUserRoles.
7. `directionsFrom` - Used like this `directionsFrom=yourOriginLocationId`.
8. `directionsTo` - Used like this `directionsTo=yourDestinationLocationId`.
9. `externalIDs` - Used like this `externalIDs=0.0.1,0.0.2,0.0.3`. **Note!** You need to provide a list of comma separated values, **without** any spaces between the comma and the value. This will further be converted into an array of external IDs. Because of the way browsers work, you **cannot** use External IDs with the `,`, `&`, `#` and `+`, character in them, as they are interpreted by the browser in a particular way.
10. `tileStyle` - Used like this `tileStyle=yourTileStyleName`. If no tile style is provided, the app will show the default tile style.
11. `mapboxAccessToken` - Used like this `mapboxAccessToken=yourMapboxAccessToken`. If no mapboxAccessToken is provided, the app will default to the access token in the `.env` file.  If both the mapboxAccessToken and the gmApiKey are present, the app will load a Mapbox map.
12. `gmApiKey` - Used like this `gmApiKey=yourGmApiKey`. If no gmApiKey is provided, the app will default to the access token in the `.env` file. If both the mapboxAccessToken and the gmApiKey are present, the app will load a Mapbox map.
13. `startZoomLevel` - Used like this `startZoomLevel=22`.
14. `gmMapId` - Used like this `gmMapId=yourGmMapId`.
15. `pitch` - Used like this `pitch=30`. Not compatible with MapsIndoors 2D models and MapsIndoors labels on Google Maps. The value of the pitch can be between 0-85 degrees on a Mapbox map.
16. `bearing` - Used like this `bearing=180`. Not compatible with MapsIndoors 2D models and MapsIndoors labels on Google Maps. It accepts any value, and will modify it to fit into the range \[0, 360].
17. `language` - The language to show textual content in. Supported values are "en" for English, "da" for Danish, "de" for German, "fr" for French, "it" for Italian and "es" for Spanish. If the prop is not set, the language of the browser will be used (if it is one of the supported languages - otherwise it will default to English).
18. `kioskOriginLocationId` - If running the Map Template as a Kiosk, provide the Location ID that represents the location of the Kiosk.
19. `timeout` - If you want the Map Template to reset the map position and the UI elements to the initial state after some time of inactivity, use this to specify the number of seconds of inactivity before resetting. This property is not dependent on the `kioskOriginLocationId`.
20. `useKeyboard` - If running the Map Template as a Kiosk, set this prop to `true` and it will prompt a virtual keyboard. This property is dependent on the `kioskOriginLocationId`.
21. `miTransitionLevel` - The zoom level on which to transition from Mapbox to MapsIndoors data. Default value is 17. This feature is only available for Mapbox.
22. `category` - If you want to indicate an active category on the map. The value should be the Key (Administrative ID).
23. `searchAllVenues` - If you want to perform search across all venues in the solution. The standard behaviour is searching in one venue.
24. `useMapProviderModule` - Set to "true" if the Map Template should take MapsIndoors solution modules into consideration when determining what map type to use.
25. `hideNonMatches` - Determine whether the locations on the map should be filtered (only show the matched locations and hide the rest) or highlighted (show all locations and highlight the matched ones with a red dot by default). If set to true, the locations will be filtered.

**Note!** All the query parameters need to be separated with the `&` symbol, without any spaces in between.

**Note!** When using parameters such as `directionsTo`, `directionsFrom`, `locationId`, `externalIDs`, and `tileStyle` make sure you are using the correct `apiKey` parameter to which they belong.

**Note!** When using the `gmMapId` property, you need to use it together with the `gmApiKey` that it is associated with.

Example of URL:

`https://domain.com/?apiKey=yourApiKey&venue=yourVenueName&locationId=yourLocationId&primaryColor=000000&logo=https://images.g2crowd.com/uploads/product/image/social_landscape/social_landscape_7a75ff13f42605422950b411ab7e03b5/mapspeople.png&appUserRoles=visitor,staff,security`

**Important!** Not all the query parameters can be used together, as they serve their own purpose which in some cases overlaps with other query parameters. Example of cases that **DO NOT** work together:

1. `locationId` + `startZoomLevel` → the `locationId` has priority over the `startZoomLevel`
2. `locationId` + `externalIDs` → the `locationId` has priority over the `externalIDs`
3. `directionsTo` + `directionsFrom` + `locationId` → the `directionsTo` + `directionsFrom` have priority over the `locationId`
4. `directionsTo` + `directionsFrom` + `externalIDs` → the `directionsTo` + `directionsFrom` have priority over the `externalIDs`
