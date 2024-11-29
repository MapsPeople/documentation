# Display Language

The language of MapsIndoors is independent of the chosen language on the device on which the app is used. This means that you need to explicitly tell MapsIndoors which language to use.

If you do not specify a language, MapsIndoors will show information in the default language defined in the MapsIndoors CMS. Likewise, if you specify a language that is not supported, MapsIndoors will also show information in the default language.

Additionally, aside from methods mentioned here, you can provide translations via the standard method for your device, such as using individual localized strings.

### Configuring POI translations in CMS[​](https://docs.mapsindoors.com/display-language#configuring-poi-translations-in-cms) <a href="#configuring-poi-translations-in-cms" id="configuring-poi-translations-in-cms"></a>

To provide multiple languages for items in the MapsIndoors CMS, such as "Meeting Room" or "Restroom", the translation must be provided by the user in the CMS. A translation can be provided in any language. In order to add support for additional languages that we currently do not support, please contact your MapsIndoors representative, and we will enable you to add translations in your desired language.

Once your language of choice has been created, you can add the translation by clicking on any POI, which will open a menu on the left side of the screen. Here, you will see the following menu point, where you can enter translations for the languages you wish. If a field is left empty, the fallback language is English.

### Use Fixed Language[​](https://docs.mapsindoors.com/display-language#use-fixed-language) <a href="#use-fixed-language" id="use-fixed-language"></a>

The MapsIndoors language can be fixed to a specific language by supplying an [ISO 639-1 language code](https://en.wikipedia.org/wiki/List_of_ISO_639-1_codes), for example French:

```swift
MPMapsIndoors.shared.language = "fr"
```

### Use Device Language[​](https://docs.mapsindoors.com/display-language#use-device-language) <a href="#use-device-language" id="use-device-language"></a>

The MapsIndoors language can be aligned with the device language by supplying the current language code of the device:

```swift
let languageCode = Locale.current.language.languageCode?.identifier
MPMapsIndoors.shared.language = languageCode
```

### Wayfinding Translations

#### Translate route instructions shown by `MPDirectionsRenderer`

The `MPDirectionsRenderer` (see [Directions Renderer](directions/directions-renderer/)) displays simple string values in route markers, providing context to the route visualization, e.g. "Enter" or "Exit".\
\
The MapsIndoors SDK includes localized english strings for this purpose, with the following keys:

* `"miDirectionsEnter"`
* `"miDirectionsExit"`
* `"miDirectionsPark"`
* `"miDirectionsLevel"`
* `"miDirectionsNext"`

You can override these, or provide additional localizations in your application.

#### Translate HTML instructions on directions

When requesting routes from MapsIndoors only external routes are translated into the current language set on `MPMapsIndoors.shared`. The internal routes are always returned in english. Here is an example of how to achieve translated HTML instructions on internal routes. Add localized strings to your application, e.g. `"direction_right"`.

```swift
// Use this method to get a translated HTML instruction for a step
func getInstructionFromStep(routeStep: MPRouteStep) -> String? {
    
    // Populate a dict with localized strings
    var directionNames = [String: String]()
    directionNames["Turn right"] = NSLocalizedString("direction_right", comment: "")
    directionNames["Turn left"] = NSLocalizedString("direction_left", comment: "")
    directionNames["Continue straight ahead"] = NSLocalizedString("direction_straight", comment: "")
    directionNames["Turn slight right"] = NSLocalizedString("direction_slightly_right", comment: "")
    directionNames["Turn slight left"] = NSLocalizedString("direction_slightly_left", comment: "")
    directionNames["Turn sharp right"] = NSLocalizedString("direction_sharp_right", comment: "")
    directionNames["Turn sharp left"] = NSLocalizedString("direction_sharp_left", comment: "")
    directionNames["Turn around"] = NSLocalizedString("direction_make_uturn", comment: "")
    directionNames["Take elevator to"] = NSLocalizedString("direction_elevator", comment: "")
    directionNames["Take stairs to"] = NSLocalizedString("direction_stairs", comment: "")

    let instruction = routeStep.html_instructions
    
    // Check if route step is a step or elevator as they have floor level specific instruction
    if routeStep.highway.typeString == "steps" {
        return directionNames["Take stairs to"] ?? "" + (routeStep.end_location.floor_name ?? "")
    } else if routeStep.highway.typeString == "elevator" {
        return directionNames["Take elevator to"] ?? "" + (routeStep.end_location.floor_name ?? "")
    } else if let translation = directionNames[instruction] {
        return translation
    }

    return instruction
}
```

Now, where you handle the `MPRoute` route response you can create a method to receive the translated instruction.
