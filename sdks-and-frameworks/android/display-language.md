# Display Language

The language of MapsIndoors is independent of the chosen language on the device on which the app is used. This means that you need to explicitly tell MapsIndoors which language to use.

If you do not specify a language, MapsIndoors will show information in the default language defined in the MapsIndoors CMS. Likewise, if you specify a language that is not supported, MapsIndoors will also show information in the default language.

Additionally, aside from methods mentioned here, you can provide translations via the standard method for your device, such as using individual localized strings.

### Configuring POI translations in CMS[​](https://docs.mapsindoors.com/display-language#configuring-poi-translations-in-cms) <a href="#configuring-poi-translations-in-cms" id="configuring-poi-translations-in-cms"></a>

To provide multiple languages for items in the MapsIndoors CMS, such as "Meeting Room" or "Restroom", the translation must be provided by the user in the CMS. A translation can be provided in any language. In order to add support for additional languages that we currently do not support, please contact your MapsIndoors representative, and we will enable you to add translations in your desired language.

Once your language of choice has been created, you can add the translation by clicking on any POI, which will open a menu on the left side of the screen. Here, you will see the following menu point, where you can enter translations for the languages you wish. If a field is left empty, the fallback language is English. In the example below, English (en) and Danish (da) are the enabled languages.

#### Use Fixed Language[​](https://docs.mapsindoors.com/display-language#use-fixed-language) <a href="#use-fixed-language" id="use-fixed-language"></a>

The MapsIndoors language can be fixed to a specific language by supplying an [ISO 639-1 language code](https://en.wikipedia.org/wiki/List\_of\_ISO\_639-1\_codes), for example French:

```kotlin
MapsIndoors.setLanguage("fr")
```

#### Use Device Language[​](https://docs.mapsindoors.com/display-language#use-device-language) <a href="#use-device-language" id="use-device-language"></a>

The MapsIndoors language can be aligned with the device language by supplying the current language code of the device:

```kotlin
val lang = resources.configuration.locales[0].language
MapsIndoors.setLanguage(lang)
```

#### Translate HTML instructions on directions.

When requesting routes from MapsIndoors at the moment only external routes are translated into the current language set on `MapsIndoors`. The internal routes are always returned in english. Here is an example of how to achieve translated HTML instructions on internal routes.

First add strings that corresponds to the directions received from HTML instructions on the route, to the Application resources `res/values/strings.xml`:

```xml
<resources>
    <string name="direction_right">Turn right</string>
    <string name="direction_left">Turn left</string>
    <string name="direction_straight">Continue straight ahead</string>
    <string name="direction_slightly_right">Turn slight right</string>
    <string name="direction_slightly_left">Turn slight left</string>
    <string name="direction_sharp_right">Turn sharp right</string>
    <string name="direction_sharp_left">Turn sharp left</string>
    <string name="direction_make_uturn">Turn around</string>
    <string name="direction_elevator">Take elevator to %1$s</string>
    <string name="direction_stairs">Take stairs to %1$s</string>
</resources>
```

Now inside the code where you handle the `MPRoute` route response you can create a method to receive the translated instruction.

```kotlin
//Populate a map with localized strings, remember to repopulate the map if the MapsIndoors language is changed
fun setupNames() {
    directionNames["Turn left"] = res.getString(R.string.direction_left)
    directionNames["Turn slight left"] = res.getString(R.string.direction_slightly_left)
    directionNames["Turn sharp left"] = res.getString(R.string.direction_sharp_left)
    directionNames["Turn right"] = res.getString(R.string.direction_right)
    directionNames["Turn slight right"] = res.getString(R.string.direction_slightly_right)
    directionNames["Turn sharp right"] = res.getString(R.string.direction_sharp_right)
    directionNames["Continue straight ahead"] = res.getString(R.string.direction_straight)
    directionNames["Turn around"] = res.getString(R.string.direction_make_uturn_right)
}

//Use this method to get a translated HTML instruction for a step
fun getInstructionFromStep(@NonNull routeStep: MPRouteStep): String? {
    var instruction: String? = routeStep.htmlInstructions
    //Check if route step is a step or elevator as they have floor level specific instruction
    if (routeStep.highway == "steps") {
        instruction = "Take stairs to level " + routeStep.endFloorName
    } else if (routeStep.highway == "elevator") {
        instruction = "Take elevator to " + routeStep.endFloorName
    } else if (directionNames.containsKey(routeStep.htmlInstructions)){
        instruction = directionNames[routeStep.htmlInstructions]
    }

    return instruction
}
```

#### Translate Route Labels

When using the Directions Renderer, the route use labels to describe the action when clicking on them. These values are not solution specific, and does not contain translations for any language. If you want them to be translated you have to assign a value in a language specific string resource. Currently only 2 values are used:

```xml
<resources>
    <string name="misdk_level">Level</string>
    <string name="misdk_next">Next</string>
</resources>
```
