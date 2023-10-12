# Editing Data

## Location[​](https://docs.mapsindoors.com/cms-editing-data#location) <a href="#location" id="location"></a>

<figure><img src="../../.gitbook/assets/CleanShot 2023-07-05 at 15.09.24@2x.png" alt=""><figcaption></figcaption></figure>

Each Location also has several settings associated with it. If you select a Location on the Map or in the List view in the CMS, you will be presented with a modal with the following settings:

* **Type** - Locations must have a Type applied, which can be set in the Location details editor. When creating a new Location, some settings are inherited from the selected Type e.g., Name and Icon. You can always change the inherited settings to something else if necessary.
* **Name & Description** - Type in the name of your Location and a Description. Entering it in the default language is mandatory, but you can also enter it in alternative languages.
* **Area** - Choose the color of the Area the Location covers. You can also set whether the Area, is visible or not.
* **Status** - Toggle whether or not this Location appears in searches.
* **Restrictions** - Determine which, if any, App User Role Restrictions this Location should be subject to.
* **Categories** - Add which, if any, Categories this Location belongs to.
* **Location Icon** - You can set an Icon to be used on the Map for this Location.
* **Image Options** - Here you can connect an image to a Location. See below for further details.
* **Search Aliases** - Other search terms that can be searched and still return this Location, even if it does not match the Name, Type, or Category.
* **Venue Details** - Select which Building and Floor this Location should belong to.
* **External ID** - You can define an External ID that a Location should use alongside its internal ID.
* **Coordinates** - The coordinates of your Location.
* **MapsIndoors Location ID** - The internal ID of your Location.
* **Active** - If your Location is only displayed and searchable for a given period, you can define that here.
* **Custom Properties** - MapsIndoors supports Custom Properties, defined by key-value pairs.
* **Location History** - See the editing history of this Location.

#### Image Options with IndoorView[​](https://docs.mapsindoors.com/cms-editing-data#image-options-with-indoorview) <a href="#image-options-with-indoorview" id="image-options-with-indoorview"></a>

> IndoorView is only supported for web

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

## Split and Combine[​](https://docs.mapsindoors.com/cms-editing-data#split-and-combine) <a href="#split-and-combine" id="split-and-combine"></a>

{% hint style="warning" %}
This is a paid feature. [Contact us to learn more!](https://resources.mapspeople.com/en-us/contact-us)
{% endhint %}

Split and Combine are features that enable you to edit a Room's geometry. A Room can either be split in two or combined with another Room.

The Split and Combine features are useful if you want to make quick changes to your Rooms. If you want to make groundbreaking changes to a Floor or Building layout, reach out to your contact at MapsPeople with your updated CAD drawings.

One use case for Split and Combine is to manage the layout of an exhibition for a temporary event in a convention center. Combine booths or split them into smaller ones based on the event demands.

Another use case is if you make changes to your office layout without making any structural changes to the building. E.g. you might knock down a plaster wall to combine two meeting rooms or the like, which doesn’t necessarily require a full redrawing of your floor layout by our team of specialists.

#### How to split[​](https://docs.mapsindoors.com/cms-editing-data#how-to-split) <a href="#how-to-split" id="how-to-split"></a>

Select a Room on the map. In the Location Details editor, click on the “Split”-button. Place two points on the borders of the Rooms where you'd like to split it. Then choose which Room is the new Room on the map. The new Room will get a new ID, but otherwise copy all other data from the original Room. The old Room will retain all of the original data.

You can get a helping hand after placing the first splitting point by holding down the "Shift" key on your keyboard. The line will be drawn perpendicular (at a 90° angle) to the Room's wall.

If you exit the Split mode before completing the split, your changes will be discarded.

**Detailed constraints for Split**[**​**](https://docs.mapsindoors.com/cms-editing-data#detailed-constraints-for-split)

1. The split must contain two points touching the edge/Walls of the Room.
2. The split can not extend outside of the Room.
3. The split must not intersect with any holes in the Room.
4. The split must be at least 1 meter from any Walls or holes.
5. The outcome of the split must be at least 1 square meters in size.
6. A split must not overlap itself.

#### How to combine[​](https://docs.mapsindoors.com/cms-editing-data#how-to-combine) <a href="#how-to-combine" id="how-to-combine"></a>

Select a Room on the map. In the Location Details editor, click on the “Combine”-button.

The selected Room is now highlighted on the map, with the compatible Rooms highlighted in a different color. Click on the other Room you'd like to combine with your selected Room.

The combined Room’s Location Details are based on the first selected Room’s Location Details. If you have any external integrations that rely on a Room's ID, make sure the Rooms are selected in the right order.

If you exit the Combine mode before completing the combination, your changes will be discarded.

**Detailed constraints for Combine**[**​**](https://docs.mapsindoors.com/cms-editing-data#detailed-constraints-for-combine)

1. All Locations must be of the same Type.
2. The Rooms must share at least 1 meter of unbroken Wall.
