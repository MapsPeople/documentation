---
description: Web v4
---

# Custom Properties

Custom Properties are key/value data that can be associated with different geodata (Venue/Building/Location) within MapsIndoors. MapsIndoors supports two different types of Custom Properties:

* Language-specific Custom Properties
* Generic Custom Properties

**Language-specific Custom Properties** are meant for values that is displayed to the enduser in their preferred language. Using language-specific Custom Properties, it is possible to store a key/value combination in multiple different languages. The MapsIndoors SDKs allows for retrieval of the correct values based on the user's preferred language. If your Solution has multiple languages, you must provide the necessary translations for each Custom Property in each of these languages.

**Generic Custom Properties** are meant for tagging data with key/value data that is relevant for the apps using the map data. These values will be available to the app regardless of the language of the end user, and are often not directly displayed. Examples of values that might be added as Generic Custom Properties could be:

* Whether or not a room is bookable
* The calendar id of a room used for booking
* Ids of a location in other systems

If a Key exists as both a Generic Custom Property and Language-specific Custom Property, the most specific element decides the value. This means that the language-specific Custom Property value will be supplied to the SDKs, as it is considered to be the most specific. This table shows the possible interactions between Generic Custom Property and a Language-specific Custom Property. Scenario 1 shows what happens if a Key is defined as a Generic Custom Property and given the value A, while a Language-specific Custom Property with the same Key is either not defined, or given an empty value:

| Scenario | Generic | Language Specific | SDK Value |
| -------- | :-----: | :---------------: | :-------: |
| 1        |    A    |                   |     A     |
| 2        |         |         B         |     B     |
| 3        |    A    |         B         |     B     |
| 4        |         |                   |           |

If a Solution uses more than one language, it is possible to give a value for a particular Key in only a subset of the languages. If for example a Solution uses both English and German, a Language-specific Custom Property could be given a value for only the German language. In this scenario if the app requests the German language, it would be given the German-specific value, while if the app requests the English language, it would be given either an empty value, or the value of the Generic Custom Property with the same Key if such a property is defined.

#### Custom Property Templates[​](https://docs.mapsindoors.com/custom-properties#custom-property-templates) <a href="#custom-property-templates" id="custom-property-templates"></a>

On Types it is possible to define Custom Property templates, which can ease getting consistent Custom Property Keys across multiple Locations. Keys added as Custom Property templates on a Type will be shown in the CMS on all Locations of that Type. This ensure that the key naming is consistent across all locations of that type. Adding values to the keys results in key/value pairs being available trough the SDK.

### Creating Custom Properties[​](https://docs.mapsindoors.com/custom-properties#creating-custom-properties) <a href="#creating-custom-properties" id="creating-custom-properties"></a>

Custom Properties are created for each Location, defined using a `key` and a `value`. This is found in a section in the menu for each Location. When adding a Generic Custom Property through the CMS, a value input field will be provided for each language in your Solution allowing you to input the translated values directly in the CMS.

<figure><img src="../../../.gitbook/assets/image (15).png" alt=""><figcaption></figcaption></figure>

You can add Custom Properties through the Integration API with the exact same requirements and options as when adding them via the MapsIndoors CMS.

### Reading Custom Properties[​](https://docs.mapsindoors.com/custom-properties#reading-custom-properties) <a href="#reading-custom-properties" id="reading-custom-properties"></a>

The method for reading and using these custom properties depends on which platform you're developing for. Here are some examples:

<figure><img src="../../../.gitbook/assets/image (18).png" alt=""><figcaption></figcaption></figure>

Using the above screenshot as an example basis you fetch the entire custom property using the following code:

```javascript
let data = location.getFieldForKey('email')
```

To retrieve individual segments of the property, you can use:

```javascript
let text = data.text
let value = data.value
let type = data.type
```

* `data.text` retrieves the content of the `key` field, and in the given example, would return `email`.
* `data.value` retrieves the content of the `value` field, and in the given example, would return `123@email.com`.
* `data.type` retrieves the type of the Custom Property, and will in most known cases return `text`.

#### Example 1[​](https://docs.mapsindoors.com/custom-properties#example-1) <a href="#example-1" id="example-1"></a>

You are a conference organizer that needs to associate some pieces of data with each exhibitor, like the contact info / email address, and if there's any kinds of refreshments at the stand.

Should this be the same value for all end users, or just for a subset of those users based on their language?

`contactInfo=(123) 555-5555`

{% hint style="info" %}
It could make sense to make this a **generic custom property** if your app does not render anything language specific in the application based on this value's string.&#x20;

All custom property values are strings. You'll need to potentially convert the data type on the front end of your application.
{% endhint %}

`refreshments@gen=True`

{% hint style="info" %}
It could make sense to make this a **generic custom property** if your app does not render anything language specific in the application based on this value's string.&#x20;

All custom property values are strings. You'll need to convert it to a Boolean value on the front end of your application.
{% endhint %}

<figure><img src="../../../.gitbook/assets/Screenshot 2023-10-24 at 8.41.02 PM.png" alt=""><figcaption></figcaption></figure>

#### Example 2[​](https://docs.mapsindoors.com/custom-properties#example-2) <a href="#example-2" id="example-2"></a>

You are a museum operator providing a digital map of your venue.&#x20;

Your digital map presents points of interest for the various exhibits and you would like to associate both a text description of the item exhibited as well as a link to a video of an expert giving additional insight about the item.

To accomplish this you create a **language specific custom property** called `itemDescription` and provide a description for each language your Solution supports. You choose a Language specific Custom Property for this purpose as the values is to be displayed to the end user and you need the user to be given description in their preferred language.

In addition to this you create a **language-specific custom property** called `videoLink` to store the link to the explanation video. This can make sense as a language specific custom property as the video's audio would be in the language of the user.

If for example you wish to show the same video to each user regardless of their language, it would make sense to have a **generic custom property.**

<div>

<figure><img src="../../../.gitbook/assets/Screenshot 2023-10-24 at 8.50.33 PM.png" alt=""><figcaption><p>generic property for video without audio</p></figcaption></figure>

 

<figure><img src="../../../.gitbook/assets/Screenshot 2023-10-24 at 8.50.27 PM.png" alt=""><figcaption><p>description and videoLink with language specific properties</p></figcaption></figure>

</div>
