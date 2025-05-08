# Editing Data

## Location[​](https://docs.mapsindoors.com/cms-editing-data#location) Details Editor <a href="#location" id="location"></a>

Each Location has several settings associated with it. If you select a Location on the Map or in the List view in the CMS, you will be presented with a modal with the following settings:

<figure><img src="../../.gitbook/assets/image (63).png" alt=""><figcaption><p>Part of the Location Details editor</p></figcaption></figure>

#### General

* **Location Type** - Locations must have a Type applied, which can be set in the Location details editor. When creating a new Location, some settings are inherited from the selected Type e.g. Icon. You can always change the inherited settings to something else if necessary.
* **Name & Description** - Type the name of your Location and a Description. Entering it in the default language is mandatory, but you can also enter it in alternative languages if set on your Solution. Provided name is going to be used as a Label. In order to modify Label presentation under the Location: \
  \- For Mapbox Map Provider: use Space to separate words in-between. Use Enter to introduce a line-breaker.\
  \- For Google Maps Map Provider: use Space to separate words in-between. Use Enter to introduce a line-breaker.
* **Categories** - Add which, if any, Categories this Location belongs to.
* **External ID** - You can define an External ID that a Location should use alongside its internal ID.
* **Additional details** - You can define additional details which can then be used in your own implementations or they can be visualised on the MapsIndoors Web App. They can be: links, phone numbers, e-mails and opening hours. Read more about it [here](additional-location-details.md).

#### Area

* This section is only visible for Areas. It is **not** visible for rooms and locations without an Area.
* **Rotation (angle)** - Set the rotation of the Area. Positive values will rotate the are to the right, while negative values will rotate the are to the left.
* **Area as an Obstacle** - This section is only visible if your `Graph Setup` is set to `Automatic`. Checking this checkbox will set the Area to be an `Obstacle`. If an Area is an `Obstacle`, the Area will have an effect on the routing. Read more about this feature here.

#### Selectable

* Selectable - Determine if the Location is clickable on the Map or not. You can choose to inherit selectable value from Location Type.

#### Restrictions

* **Restrictions** - Determine which, if any, App User Role Restrictions this Location should be subject to.
* You can choose to inherit Restrictions that might be set on the Location's Type, or override them for this particular Location.

#### Search

* **Search Status** - Toggle whether or not this Location appears in searches.
* **Search Aliases** - Other search terms that can be searched and still return this Location, even if it does not match the Name, Type, or Category.

#### Visibility

* **Active** **to/from** - If your Location is only displayed and searchable for a given period, you can define that here.

#### Image

*   **Image Options** - Here you can add an image to a Location. You can select one from the [Media Library](media-library/), or set a url for an image hosted elsewhere. This should be a photo, as opposed to the pictogram you use for the Icon.

    * This image can be used when accessing the Location data using the MapsIndoors SDKs

    ```html
    {
      "id": "586ce41ebc1f571794b9e924",
       ...
      "properties": {
        "name": "Copenhagen",
         ...
        "imageURL": "https://media.mapsindoors.com/[SOLUTION_ID]/media/[MEDIA_NAME].png",
      }
    }
    ```

    * The Location image can be seamlessly integrated into your application as needed. Given the dynamic nature of its usage, specifying an exact size can be challenging. However, for users of our **Map Template** product, we offer some guidance.\
      \
      Within the **Map Template** interface for mobile-centric applications, a Location image  typically occupies around 90% of the screen resolution. For instance, on an iPhone with a browser width of 414px, the Location image is displayed at a width of 382px. To ensure crispness on such devices, accounting for a device pixel density of at least 2 is crucial. Therefore, for the iPhone example, the image should ideally be at least 382x2 = 764px wide. Though there's no fixed height limit, the standard aspect ratio tends towards 4:3, indicating a projected height spanning between 573px. Furthermore, keeping file sizes minimal enhances performance and expedites loading times, a vital consideration across all platforms.

#### Custom Properties

* **Custom Properties** - MapsIndoors supports Custom Properties, defined by key-value pairs.

Read more about [Custom Properties](../../sdks-and-frameworks/web/other-guides/custom-properties.md).

#### Details

* **Venue Details** - Select which Building and Floor this Location should belong to.
* **MapsIndoors Location ID** - The internal ID of your Location.
* **Coordinates** - The coordinates of your Location.

### Image Options with IndoorView[​](https://docs.mapsindoors.com/cms-editing-data#image-options-with-indoorview) <a href="#image-options-with-indoorview" id="image-options-with-indoorview"></a>

> IndoorView is only supported for implementations using the JavaScript SDK.

To start using the IndoorView feature for your Locations, please make sure that the _Google Street View panorama images_ are publicly available for your building by looking at [Google Maps](https://www.google.com/maps). If no imagery is available, please [contact a certified Street View Photographer](https://www.google.com/streetview/contacts-tools/).

1. Click "Set Street View image"
   * This will open a Google Street View window showing the image closest to this Location. Please note that the MapsIndoors CMS looks for panorama images within a certain radius from the Location's position, so make sure to have panorama images published in that Area.
2. Navigate Street View and find an image and viewing angle that is suitable
3. Click "Set image"

**Mapsindoors Support Matrix**[**​**](https://docs.mapsindoors.com/cms-editing-data#mapsindoors-support-matrix)

| MapsIndoors | Support for IndoorView                                             | Private hosted panorama images |
| ----------- | ------------------------------------------------------------------ | ------------------------------ |
| CMS         | Yes                                                                | No                             |
| Web App     | Yes                                                                | No                             |
| Android App | No                                                                 | No                             |
| iOS App     | No                                                                 | No                             |
| Web SDK     | Yes, through Google Maps Street View API or Static Street View API | No                             |
| Android SDK | No                                                                 | No                             |
| iOS SDK     | No                                                                 | No                             |

IndoorView only supports **publicly** available Google Street View imagery. If you want to know more about **privately** hosted panorama images, please see [Googles Custom Street View documentation](https://developers.google.com/maps/documentation/javascript/streetview#CustomStreetView).

**Developing Your App**[**​**](https://docs.mapsindoors.com/cms-editing-data#developing-your-own-app)

When developing your own app, you can still use the MapsIndoors CMS to save the Google Street View image information to a Location. When the Panorama image is set, the Location gets populated with a `streetViewConfig` property. Please see below for an example.

Location Object:

{% code overflow="wrap" fullWidth="false" %}
```
{
  "id": "586ce41ebc1f571794b9e924",
   ...
  "properties": {
    "name": "Copenhagen",
     ...
    "streetViewConfig": {
      "panoramaId": "CAoSLEFGMVFpcE41dTZnVzNpVnU1WmliRjk0T2tpMENhZ3Fya3ljVFh3TjVzN2lY",
      "povHeading": 203.1294893976259,
      "povPitch": -13.068754012666432
    }
  }
}
```
{% endcode %}

The parameters above can show a static Street View image through the Street View Static API. Please see [the Street View documentation](https://developers.google.com/maps/documentation/streetview/intro) for more information.

A Street View panorama image can be initialized in a `<div>` the following way:

```
function initStreetView(streetViewConfig) {
       const panorama = new google.maps.StreetViewPanorama(
            document.getElementById('panorama'),
            {
                pano: streetViewConfig.panoramaId,
                pov: {
                    heading: streetViewConfig.povHeading,
                    pitch: streetViewConfig.povPitch
                },
                zoom: 0,
                visible: true
            });
}
```

Please see the official [Google Street View Service documentation](https://developers.google.com/maps/documentation/javascript/streetview) for more information.

## &#x20;<a href="#split-and-combine" id="split-and-combine"></a>
