---
description: Building on top of the Map Template
---

# Customization

### Working with the components package and the map-template package

To have any Stencil component (`packages/component`) changes reflected in the Map Template package (`packages/map-template`), you must run `npx lerna run build` from the root folder. There are no watch scripts yet.

### Adding translations for a new language

If you want to run the Map Template in another language than the four already supported ones, you need to make some small adjustments to the code and add translations yourself.

The Map Template uses [i18next](https://www.i18next.com/) and [i18next-react](https://react.i18next.com/) to help manage UI internationalisation (translating your Locations data should be done from the MapsIndoors CMS).

To add a new language, locate the folder `packages/map-template/src/i18n`.

Here you find five files, one for setting up the i18next, and one JavaScript file for each of the four already supported languages, named by convention with their IETF primary language tag. These files contains one object with translation keys and values.

Copy the content of the `en.js` file (contains translation for English) into a new file, for example `pt.js` if you plan to support Portuguese. Now translate each value in the object into your desired language.

Finally, in the `initialize.js` file, import your new file with your new language, and then use it in the resources object, similar to the other four languages. Continuing the example of adding Portuguese, the object should now have a `pt` entry:

```
...
fr: {
	translation: fr
},
pt: {
	translation: pt
}


```

You should now be able to run the Map Template with your new language if you run it in a browser that uses that language, or if you use the `language` prop or query parameter.
