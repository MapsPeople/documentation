---
icon: square-code
---

# Getting started

## ✅ Prerequisites

Make sure you have the following before using the @mapsindoors/built-in-map-edits package:

* **A MapsIndoors Client ID:** A `client_id` is required for [Application authentication](authentication.md). Please contact MapsPeople support to obtain your `client_id`. For development purposes, you can alternatively authenticate using a MapsIndoors CMS user account.
* **MapsIndoors Data:** Familiarity with the [MapsIndoors data model](../../products/cms/data-concepts.md) is recommended.
* **Authentication Knowledge**: Familiarity with OAuth and OpenID Connect (OIDC) is necessary.
* **Programming Skills**: Basic understanding of TypeScript or JavaScript is necessary.
* **Development Environment**: Set up Node.js with npm or Yarn.

***

## ⚙️ Install

You can add the @mapsindoors/built-in-map-edits package to your project using npm or yarn:

**NPM** [https://npmjs.org/](https://npmjs.org/):

```shell
npm install @mapsindoors/built-in-map-edits
```

**Yarn** [https://yarnpkg.com/](https://yarnpkg.com/):

```shell
yarn add @mapsindoors/built-in-map-edits
```

***

## 🚀 Usage

#### Obtaining an Access Token

To interact with the MapsIndoors API, you need an access token. This example demonstrates how to obtain an access token using the OAuth Resource Owner Password Credentials Grant flow. **This should only be used for development or testing purposes.**

**Warning: Do not use this method in production!** Never hardcode your MapsIndoors username and password directly into your client-side code because it exposes your credentials to anyone who views the source code. This example is for development/testing purposes only. Use a secure backend service to handle authentication.

```js
const response = await fetch('https://auth.mapsindoors.com/connect/token', {
    method: 'POST',
    headers: { 'Content-Type': 'application/x-www-form-urlencoded' },
    body: new URLSearchParams({
        'grant_type': 'password',
        'client_id': 'client',
        'username': 'YOUR_USERNAME', // Replace with your MapsIndoors username.
        'password': 'YOUR_PASSWORD' // Replace with your MapsIndoors password.
    })
});

if (!response.ok) {
    throw new Error(`HTTP error! status: ${response.status}`);
}

const data = await response.json();
const accessToken = data.access_token;
```

#### List Available Solutions

In MapsIndoors, a [**Solution**](https://docs.mapsindoors.com/other/glossary#solution) is the starting point for working with your organization's MapsIndoors data. Before opening a specific solution by its ID, you might need to find out which solutions are available.

You can retrieve a list of available MapsIndoors solutions that the access token grants access to using the `getAvailableSolutions()` method:

```js
import { MapsIndoorsEditor } from '@mapsindoors/built-in-map-edits';

MapsIndoorsEditor.accessToken = accessToken;

const availableSolutions = await MapsIndoorsEditor.getAvailableSolutions();

for (const { name, id } of availableSolutions) {
    console.log(`Solution: ${name} - (${id})`);
}
```

#### Obtaining a SolutionManager

To interact with the data (like Locations and Types) within a specific MapsIndoors Solution, you first need a `SolutionManager` instance. This object provides the methods for accessing and modifying data.

There are two primary ways to obtain a `SolutionManager`:

1. **Open by Solution ID:** Use this method if you know the ID of the MapsIndoors Solution you want to edit.
2. **Connect to an SDK Instance:** Use this method if you are integrating the editor with an existing MapsIndoors Web SDK map display.

Choose one of the following methods based on your use case:

**Note:** Both examples are a continuation of the example found in Obtaining an Access Token

**Method 1: Opening a Solution by ID (`open`)**

If you have the ID of the specific MapsIndoors solution you want to manage (perhaps obtained using `getAvailableSolutions()` above), you can use the \`MapsIndoorsEditor.open() method.

```js
import { MapsIndoorsEditor } from '@mapsindoors/built-in-map-edits';

MapsIndoorsEditor.accessToken = accessToken;

const solutionIdToOpen = 'YOUR_SOLUTION_ID';

try {
    const solutionManager = await MapsIndoorsEditor.open(solutionIdToOpen);
    console.log(`Successfully opened solution: ${solutionManager.solutionId}`);
    // Now you can use solutionManager to interact with the solution's data.
} catch (error) {
    // Handle errors (e.g., invalid token, invalid ID, network issues)
    console.error(`Failed to open solution ${solutionIdToOpen}:`, error);
}
```

_Replace `'YOUR_SOLUTION_ID'` with your desired solution ID._

This method returns a SolutionManager promise, which resolves once the necessary solution configuration is loaded.

**Method 2: Connecting to an Existing SDK Instance (`connect`)**

If you are integrating these editing capabilities into an application that already uses the MapsIndoors Web SDK (v4.39.1 or later), you can use the `MapsIndoorsEditor.connect()` method. This approach leverages the existing SDK instance, automatically determining the correct solutionId.

This is particularly useful for building interactive editing tools where changes can be visualized directly on the map provided by the SDK.

**Note:** In this example, it is assumed that `mapsIndoorsInstance` is a reference to an existing MapsIndoors SDK instance.

```js
import { MapsIndoorsEditor } from '@mapsindoors/built-in-map-edits';

MapsIndoorsEditor.accessToken = accessToken;

try {
    // Pass your existing MapsIndoors SDK instance to connect
    const solutionManager = await MapsIndoorsEditor.connect(mapsIndoorsInstance);
    console.log(`Successfully connected to SDK solution: ${solutionManager.solutionId}`);
    // Now you can use solutionManager to interact with the solution's data.
} catch (error) {
    // Handle errors (e.g., invalid token, invalid ID, network issues)
    console.error('Failed to connect to MapsIndoors SDK instance:', error);
}
```

_The minimum supported MapsIndoors SDK version is v4.39.1. For more information about the MapsIndoors SDK, see the_ [_MapsIndoors SDK documentation_](https://docs.mapsindoors.com/sdks-and-frameworks/web/tutorial)_._

Like `open()`, this method also returns a `SolutionManager` promise.

**Important:** You only need to use either open() or connect() to get your `SolutionManager`. The subsequent examples in this README assume you have obtained a solutionManager variable using one of these two methods.

#### List Available Location Types

The `SolutionManager.locationTypes` property provides a list of [**Location Types**](https://docs.mapsindoors.com/other/glossary#type) configured within the current solution. This list can be used to populate a select list or dropdown menu in your application's user interface, allowing users to easily select valid location types when modifying locations. The names are displayed in the solution's default language.

```js
const locationTypes = solutionManager.locationTypes;

for(const [key, type] of locationTypes) {
    // Find the translation object that matches the solution's default language
    const translation = type.translations.find(translation => translation.language === solutionManager.defaultLanguage);

    console.log(`Location Type: ${key} - ${translation?.name}`);
}
```

#### Update a Location's Type

Change the type of an existing [**Location**](https://docs.mapsindoors.com/other/glossary#location--place--poi) using a location type key obtained from the `SolutionManager.locationTypes` list.

```js
const locationEditor = await solutionManager.getLocation('LOCATION_ID');

await locationEditor
    .setType('LOCATION_TYPE_KEY')
    .save();
```

_Remember to replace `'LOCATION_ID'` and `'LOCATION_TYPE_KEY'` with your actual values._

#### Update a Location's Name and Description

Set localized names and descriptions for a specific location across all available languages.

```js
const availableLanguages = solutionManager.availableLanguages;
const locationEditor = await solutionManager.getLocation('LOCATION_ID');

for(const language of availableLanguages) {
    locationEditor
        .setName(language, 'LANGUAGE_SPECIFIC_NAME')
        .setDescription(language, 'LANGUAGE_SPECIFIC_DESCRIPTION');
}

await locationEditor.save();
```

_Remember to replace `'LOCATION_ID'`, `'LANGUAGE_SPECIFIC_NAME'`, and `'LANGUAGE_SPECIFIC_DESCRIPTION'` with your actual values._

#### Retrieving a LocationEditor on Click

This example assumes you have a working MapsIndoors Web SDK integration. If you're new to the SDK, please refer to the [MapsIndoors Web SDK Tutorial](https://docs.mapsindoors.com/sdks-and-frameworks/web/tutorial) for setup and basic usage instructions.

Set up a click event listener to retrieve a `LocationEditor` instance for the clicked location. This allows you to interact with the location programmatically.

**Note:** This example is a continuation of the example found in Method 2: Connecting to an Existing SDK Instance (`connect`)

```js
let locationEditor = null;

// Get LocationEditor when a MapsIndoors location is clicked
mapsIndoorsInstance.on('click', async (e) => {
    if (!e?.id) {
        locationEditor = null;
        return;
    }

    try {
        locationEditor = await solutionManager.getLocation(e.id);
        console.log(`LocationEditor retrieved for ID: ${e.id}`);
    } catch (error) {
        console.error(`Failed to get LocationEditor for ID ${e.id}:`, error);
        locationEditor = null;
    }
});
```

#### Move a Location to Specific Coordinates

Move a location to a new latitude and longitude.

```js
const locationEditor = await solutionManager.getLocation('LOCATION_ID');

locationEditor.moveTo({ lat: 55.6761, lng: 12.5683 });

await locationEditor.save();
```

_Remember to replace `'LOCATION_ID'` with your actual value._

#### Example: Interacting with a Single Location through the SDK

This example assumes you have a working MapsIndoors Web SDK integration. If you're new to the SDK, please refer to the [MapsIndoors Web SDK Tutorial](https://docs.mapsindoors.com/sdks-and-frameworks/web/tutorial) for setup and basic usage instructions.

This example demonstrates how to interact with a single location using the `SolutionManager` obtained from an existing SDK connection. It enables you to make a location draggable on the map, allowing for interactive movement.

**Note:** This example is a continuation of the example found in Method 2: Connecting to an Existing SDK Instance (`connect`)

```js
let locationEditor = null;

mapsIndoorsInstance.on('click', async (e) => {
    if (!e?.id) {
        locationEditor = null;
        return;
    }

    try {
        locationEditor = await solutionManager.getLocation(e.id);
    } catch (error) {
        locationEditor = null;
    }
});

window.addEventListener('keydown', handleKeyDown);
window.addEventListener('keyup', handleKeyUp);

function handleKeyDown(e) {
    if(e.key !== 'd') {
        return;
    }
    
    if(!locationEditor) {
        return;
    }

    locationEditor.draggable = true;
}

function handleKeyUp(e) {
    if(e.key !== 'd') {
        return;
    }
    
    if(!locationEditor) {
        return;
    }
    
    locationEditor.draggable = false;
}
```

_Remember to call `await locationEditor.save();` to persist the changes._

#### Move a Location by Distance and Direction

Move a location a given distance (meters) in a specific direction (degrees), where the direction is measured clockwise from true north.

```js
const locationEditor = await solutionManager.getLocation('LOCATION_ID');

// Move 10 meters in 45 degrees
locationEditor.move(10, 45); 

await locationEditor.save();
```

_Remember to replace `'LOCATION_ID'` with your actual value._

#### Rotate a Location

Rotate a location by a specified angle in degrees, measured clockwise from true north.

```js
const locationEditor = await solutionManager.getLocation('LOCATION_ID');

locationEditor.rotate(90); // Rotate 90 degrees

await locationEditor.save();
```

_Remember to replace `'LOCATION_ID'` with your actual value._

#### Resetting Location Changes

The `reset` method in the `LocationEditor` class allows you to revert any changes made to a location back to its original state or the last saved state. This is useful when you want to discard any modifications and restore the location to its previous state, including its position and rotation.

To reset the changes made to a location, simply call the `reset` method on the `LocationEditor` instance. This will revert the location data to its original state and dispatch a `change` event to notify any listeners about the reset.

```js
const locationEditor = await solutionManager.getLocation('LOCATION_ID');

// Set the name, description, position, and rotation for the location
locationEditor
    .setName('en', 'New Name')
    .setDescription('en', 'New Description')
    .moveTo({ lat: 55.6761, lng: 12.5683 })
    .rotate(90);

// Save the changes
await locationEditor.save();

// Make further changes
locationEditor
    .setName('en', 'Another Name')
    .setDescription('en', 'Another Description')
    .moveTo({ lat: 55.6762, lng: 12.5684 })
    .rotate(180);

// Reset the changes to the last saved state
locationEditor.reset();
```

_Remember to replace `'LOCATION_ID'` with your actual value._
