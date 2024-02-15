---
description: >-
  This page is dedicated to describe all sections and sub-sections of Solution
  Settings.
---

# Solution Settings

### Main Display Rule

The Main Display Rule section sets the default styling parameters for all geographical data displayed on the map. Each Location and Location Type values can be overridden with Display Rule values set in Main Display Rule. Any adjustment made here, will affect Locations and Location Type inheriting from Main Display Rule

<figure><img src="../../.gitbook/assets/Screenshot 2024-02-12 at 14.22.02.png" alt=""><figcaption></figcaption></figure>

### Building Highlight

One way you can alter the look and feel of your map is by changing the color of the outline surrounding your buildings.

In the CMS, you can edit the Building Outline Display Rules by navigating from `Solution Details` -> `Solution Settings` -> `Building Highlight`. Here you will find the "Polygon" section that contains options related to the appearance of the Building Outline. You see your setting by navigating to `Map`.

<figure><img src="../../.gitbook/assets/Screenshot 2024-02-12 at 14.20.19.png" alt=""><figcaption><p>Building Highlight section inside Solution Settings</p></figcaption></figure>

1. **Visibility** - Controls whether the Building Outline is visible on the map.
   * The system will accept a Boolean here, so either `true` or `false`.
2. **Zoom from** - Sets the minimum Zoom Level at which the Building Outline is visible.
   * The value should be a number between 1 and 25, with 1 being very far away, and 25 being very close (25 not available for all Solutions). In a general use case, most users will only need values between 15 and 25.
   * If you are developing using the JavaScript SDK for Google Maps, the value must be an integer. If you are developing for Android or iOS, or using a different map provider, the value may be fractional.
3. **Zoom to** - Sets the maximum Zoom Level at which the Building Outline is visible.
   * The value should be a number between 1 and 25, with 1 being very far away, and 25 being very close (25 not available for all Solutions). In a general use case, most users will only need values between 15 and 25.
   * Checking the `Max zoom` checkbox will ensure that the Building Outline will be visible at the largest possible Zoom level (this value may increase in the future).
   * If you are developing using the JavaScript SDK for Google Maps, the value must be an integer. If you are developing for Android or iOS, or using a different map provider, the value may be fractional.
4. **Stroke color** - Controls the stroke color of the Building Outline.
   * In the CMS, you can select a color using the color picker displayed when clicking the color input field.
   * If setting the color in-app, the value provided must be in 6-digit HEX code (eg. #3071D9).
5. **Stroke width** - Controls the stroke width (in pixels) of the Building Outline.
6. **Stroke opacity** - Controls the stroke opacity of the Building Outline.
   * The value here should be between 0 and 1, for example a value of 1 gives 100% opacity, 0.2 gives 20% opacity, etc.

### Map Behavior

<figure><img src="../../.gitbook/assets/Screenshot 2024-02-12 at 14.32.14.png" alt=""><figcaption></figcaption></figure>

Currently clustering and collision handling are not respected by the Web SDK.

Selectable - Determine if the Location is clickable on the Map or not.

### 3D Settings

<figure><img src="../../.gitbook/assets/Screenshot 2024-02-12 at 14.39.37.png" alt=""><figcaption></figcaption></figure>

Here you can control the opacity of Room Extrusions and Walls. Setting any value for one of them, will affect all extrusions and walls inside you solution.
