# Kiosk

**What is the MapsIndoors Kiosk?**&#x20;

Our MapsIndoors Kiosk is an extension of new features built on top of our [Map Template](../) repository. It enables you to only in a few steps add the Kiosk mode to your solution, and in this guide we will walk you through how you can easily get started right away.&#x20;

<figure><img src="../../../.gitbook/assets/Kiosk GIF compress.gif" alt=""><figcaption></figcaption></figure>

The MapsIndoors Kiosk builds on top of the design we have designed for Map Template. The kiosk is of course compatible with both Mapbox and Google Maps.\
With our Kiosk you can enable different features as you like to customize your Kiosk, through different Query Parameters.&#x20;

In Kiosk mode the key features are:&#x20;

* [UI design optimization for a Kiosk](./#ui-design)
* [Directions](./#directions)
* [QR code](./#create-a-qr-code-to-share-directions)
* [On-screen Keyboard ](./#on-screen-keyboard)
* [Time-out ](./#timeout)

### **Enabling the kiosk mode**

In order to enable the Kiosk mode you can use query parameters and properties to configure your app.

A new property has now been added to the list, which is `kioskOriginLocationId`. This property enables the kiosk mode on the Map Template.&#x20;

We have three simple ways to use the `kioskOriginLocationId` property, however it is important that the `kioskOriginLocationId` must belong to the correct solution (`apiKey`) in order to be displayed on the map.

#### Using query parameters:&#x20;

```
https://map.mapsindoors.com/?apiKey=02c329e6777d431a88480a09&kioskOriginLocationId=b47a973a8450439598c0189c
```

#### Using the web component:&#x20;

```
<mapsindoors-map
    api-key="02c329e6777d431a88480a09"
    kiosk-origin-location-id="b47a973a8450439598c0189c">
</mapsindoors-map>
```

#### Using the React component:&#x20;

```
<MapsIndoorsMap
    apiKey="02c329e6777d431a88480a09"
    kioskOriginLocationId="b47a973a8450439598c0189c"/>
```



### **UI design optimization for a Kiosk** <a href="#ui-design" id="ui-design"></a>

With the kiosk mode enabled, your app will use our UI design for a Kiosk. Building upon the design of Map Template components, the MapsIndoors Kiosk delivers a seamless and intuitive user experience optimised the user of experience of interaction with a large screen Kiosk. The design is specifically crafted to ensure effortless navigation and interaction, even for individuals who may be unfamiliar with the technology.\
\
Specifically, **here are the additional UI components we've added:**

<figure><img src="../../../.gitbook/assets/CleanShot 2024-03-12 at 12.51 1.png" alt=""><figcaption><p>"Open information modal" button</p></figcaption></figure>

<figure><img src="../../../.gitbook/assets/CleanShot 2024-03-12 at 12.50 3.png" alt=""><figcaption><p>Information modal</p></figcaption></figure>

<figure><img src="../../../.gitbook/assets/CleanShot 2024-03-12 at 12.50 2.png" alt=""><figcaption><p>On-screen keyboard and scroll buttons</p></figcaption></figure>

<div data-full-width="false"><figure><img src="../../../.gitbook/assets/CleanShot 2024-03-12 at 12.49 1.png" alt=""><figcaption><p>"Scan QR" for directions button</p></figcaption></figure></div>

<div align="center" data-full-width="false"><figure><img src="../../../.gitbook/assets/CleanShot 2024-03-12 at 12.50 1.png" alt=""><figcaption><p>"Scan QR" modal</p></figcaption></figure></div>

### **Instant directions in Kiosk mode** <a href="#directions" id="directions"></a>

For a smooth user experience when interacting with the kiosk, you can now select any location on the map to instantly get directions.&#x20;

<figure><img src="../../../.gitbook/assets/directions-next-step.gif" alt=""><figcaption></figcaption></figure>

You can change the accessibility if needed, and you can also scan the QR code for an easier view of the route on your mobile device . The QR code feature is further described in this article.

### Reset view button

If you ever get lost while navigating your web app in kiosk mode, you can use the **Reset View** button located in the bottom-right corner of the map. This will smoothly pan the view back to the location specified by the `kioskOriginLocationId` property.

<figure><img src="../../../.gitbook/assets/Screen Recording 2025-10-07 at 09.48.18.gif" alt=""><figcaption></figcaption></figure>

### Create a QR code to share directions

What’s worse than being in a huge airport and not being able to remember, how to find your way to the gate?&#x20;

No problem! We solved that by having a QR code functionality in your app.&#x20;

When getting directions, you can now click on the `Scan QR code` button. All you need to do is take your phone, scan the QR code on the screen and open it up in your browser. That will open up the app on your phone, with the directions ready for you. Click `Go!` and you will be able to get the directions to your preferred route!&#x20;

<figure><img src="../../../.gitbook/assets/directions-qr-code.gif" alt=""><figcaption></figcaption></figure>

Read more about the QR code configuration [here](qr-code-configuration.md).

### **On-screen keyboard**&#x20;

Is your kiosk screen missing a built in virtual keyboard? No worries, we’ve got you covered!&#x20;

You can now set the `useKeyboard` boolean property to `true`, and a virtual keyboard will show up when interacting with the input fields.&#x20;

Just like the `kioskOriginLocationId` property described above, the `useKeyboard` can be used as a URL parameter, as a property on the React component - or as an attribute on the web component.

```
https://map.mapsindoors.com/?apiKey=02c329e6777d431a88480a09&kioskOriginLocationId=b47a973a8450439598c0189c&useKeyboard=true
```

Simply click on the search field to display the virtual keyboard.

<figure><img src="../../../.gitbook/assets/image (10).png" alt=""><figcaption></figcaption></figure>

### **Timeout**

If you want the Kiosk to reset the map position and the UI elements to the initial state after some time of inactivity, use this property to specify inactivity in seconds before resetting.

```
https://map.mapsindoors.com/?apiKey=02c329e6777d431a88480a09&kioskOriginLocationId=b47a973a8450439598c0189c&timeout=10
```

<figure><img src="../../../.gitbook/assets/timeout.gif" alt=""><figcaption></figcaption></figure>

### Information Modal

In the Kiosk you can add an information modal which is a modal that can contain any preferred content you would like to show on the location.

The info legend can be configured in the CMS by clicking on the location which you selected to be the `kioskOriginLocationId`  and going to the `Custom Properties` section.&#x20;

<figure><img src="../../../.gitbook/assets/image (2) (1).png" alt=""><figcaption></figcaption></figure>

In order to add content on the legend info you need to use pre-defined custom properties: `1LegendHeading, 1LegendContent, 2LegendHeading, 2LegendContent etc`  as keys and add a value that you want to be displayed in the legend. (i.e. title, phone number, opening hours, information about the kiosk etc)

<figure><img src="../../../.gitbook/assets/image (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

In the Kiosk the information modal button will be presented in on the left side of the search field, as shown below:&#x20;

<figure><img src="../../../.gitbook/assets/image (2) (1) (1).png" alt=""><figcaption></figcaption></figure>

Clicking on the information modal button will open up the modal containing all the information added above.

<figure><img src="../../../.gitbook/assets/kiosk-legend (1).gif" alt=""><figcaption></figcaption></figure>

### URL parameters in combinations with Kiosk mode

You can of course use any other combination of our URL parameters (listed [here](../configuration/query-parameters.md)) in combination with our Kiosk.&#x20;

You can also use the `pitch`, `bearing` and `startZoomLevel`  or any other properties you want to configure as the starting point of the Kiosk. Read more about all the properties here.

If you want your kiosk to be displayed in a different language than English, make sure you check out the language options listed on the link above, under the \`language\` query parameter. The currently supported languages are English, German, French, Danish, Italian, Spanish and Dutch.

You can find an example [here](https://map.mapsindoors.com/?apiKey=02c329e6777d431a88480a09\&kioskOriginLocationId=b47a973a8450439598c0189c\&useKeyboard=true\&pitch=50\&bearing=180\&timeout=10), of our own-pre-built web app with the Kiosk mode, pitch and bearing set to set the angle of our virtual Kiosk on the map.



**Minimum viewport dimensions**

_Please note:_ The minimum viewport dimensions for the Kiosk are 1920px x 1080px.

### &#x20;
