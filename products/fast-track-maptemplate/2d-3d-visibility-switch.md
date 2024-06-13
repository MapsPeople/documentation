---
description: >-
  This article is intended to present the new map visualisation options to
  switch between 2D and 3D maps, allowing you to deliver performant solutions
  based on the device or use case.
---

# 2D/3D Visibility Switch

<figure><img src="../../.gitbook/assets/2d3dswitch.gif" alt=""><figcaption></figcaption></figure>

The Map Template allows you to seamlessly switch between the two different view modes (2D / 3D), based on your use case.&#x20;

**This feature allows you to:**&#x20;

* **Transition effortlessly** - switch between detailed 3D views and streamlined 2D layouts with a simple click. (Calling the SDK functions) + link to the SDK implementation page&#x20;
* **Optimise performance** - enabling the 2D view which is tailored for speed and clarity, providing a fast and responsive mapping experience on older devices.
* **Adjust camera** - enjoy the best viewing angle automatically, as the feature adjusts the camera position to suit the selected map mode (top-down for 2D maps and a 45-degree angle for 3D — the camera position is not locked, just adjusted).



**Enabling the 2D/3D Visibility Switch on your own Map Template**&#x20;

In order to view the 2D/3D Visibility Switch in your own app, you need to have the following modules enabled: `Mapbox`, `3D Walls` and `2D Walls` in the CMS.

Below you can find a step by step guide to how you can enable the previously mentioned modules:

1. Go to the [CMS](https://cms.mapsindoors.com/map) and select your own solution (the API Key used in the Map Template)
2. Go to the Deployment tab -> Solutions tab&#x20;
3. In the search field placed at the top right of the page, search for the name of your solution
4. When you found your solution, click on the pen icon placed at the beginning of the row&#x20;
5. Scroll down to the `Modules` section and enable the `Mapbox`, `3D Walls` and `2D Walls`  modules
6. When having them enabled, press `Save and close` and refresh the Map Template. The expected behaviour is to see the 2D/3D Visibility Switch on the top left corner.

**Note!** This feature is only available on Mapbox.&#x20;

Read more about the SDK methods that enable this implementation [here](../../sdks-and-frameworks/web/map-visualization/managing-features-visibility-for-mapbox-v3.md).
