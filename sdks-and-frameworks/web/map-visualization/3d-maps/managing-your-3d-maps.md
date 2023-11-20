# Managing your 3D Maps

Your 3D maps are managed and customised through the use of Display Rules. For a more extensive and detailed explanation on Display Rules, [please read this article covering the topic](https://docs.mapsindoors.com/display-rules). The following information can also be found in the articles detailing Display Rules, but 3D-specific information will be covered in this article.

#### 3D Walls[​](https://docs.mapsindoors.com/managing-3d-maps#3d-walls) <a href="#3d-walls" id="3d-walls"></a>

The MapsIndoors CMS gives you the option of displaying your map in 3D. This is achieved by ensuring that any walls present in your solution are displayed as an extrusion, giving the user the appearance of a 3D map. The appearance of the walls can be customized to match your desired visual identity.

1. **Visibility** - Controls whether the 3D walls are visible on the map.
   * The system will accept a Boolean here, so either `true` or `false`.
2. **Zoom from** - Sets the minimum Zoom Level at which the 3D walls are visible.
   * The value should be a number between 1 and 22, with 1 being very far away, and 22 being very close (22 not available for all Solutions). In a general use case, most users will only need values between 15 and 22.
   * If you are developing using the JavaScript SDK for Google Maps, the value must be an integer. If you are developing for Android or iOS, or using a different map provider, the value may be fractional.
3. **Zoom to** - Sets the maximum Zoom Level at which the 3D walls are visible.
   * The value should be a number between 1 and 22, with 1 being very far away, and 22 being very close (22 not available for all Solutions). In a general use case, most users will only need values between 15 and 22.
   * If you are developing using the JavaScript SDK for Google Maps, the value must be an integer. If you are developing for Android or iOS, or using a different map provider, the value may be fractional.
4. **Wall color** - Controls the color of the 3D walls.
   * In the CMS, you can select a color using the color picker displayed when clicking the color input field.
   * If setting the color in-app, the value provided must be in 6-digit HEX code (eg. #3071D9).
5. **Wall height** - Controls the height of the 3D walls, measured in meters.

#### 3D Room Extrusion[​](https://docs.mapsindoors.com/managing-3d-maps#3d-room-extrusion) <a href="#3d-room-extrusion" id="3d-room-extrusion"></a>

In addition to the ability to extrude all walls, you can additionally extrude specific rooms or areas, for example, if you wish to highlight a specific meeting room where an important meeting is taking place.

1. **Visibility** - Controls whether the extrusion is visible on the map.
   * The system will accept a Boolean here, so either `true` or `false`.
2. **Zoom from** - Sets the minimum Zoom Level at which the extrusion is visible.
   * The value should be a number between 1 and 22, with 1 being very far away, and 22 being very close (22 not available for all Solutions). In a general use case, most users will only need values between 15 and 22.
   * If you are developing using the JavaScript SDK for Google Maps, the value must be an integer. If you are developing for Android or iOS, or using a different map provider, the value may be fractional.
3. **Zoom to** - Sets the maximum Zoom Level at which the extrusion is visible.
   * The value should be a number between 1 and 22, with 1 being very far away, and 22 being very close (22 not available for all Solutions). In a general use case, most users will only need values between 15 and 22.
   * If you are developing using the JavaScript SDK for Google Maps, the value must be an integer. If you are developing for Android or iOS, or using a different map provider, the value may be fractional.
4. **Extrusion color** - Controls the color of the extrusion.
   * In the CMS, you can select a color using the color picker displayed when clicking the color input field.
   * If setting the color in-app, the value provided must be in 6-digit HEX code (eg. #3071D9).
5. **Extrusion height** - Controls the height of the room extrusion, measured in meters.

#### 3D models[​](https://docs.mapsindoors.com/managing-3d-maps#3d-models) <a href="#3d-models" id="3d-models"></a>

3D models are a way of including models on the map, and customising their appearance. They are uploaded using the Media Library.

1. **Visibility** - Controls whether the 3D model is visible on the map.
   * The system will accept a Boolean here, so either `true` or `false`.
2. **Zoom from** - Sets the minimum Zoom Level at which the 3D model is visible.
   * The value should be a number between 1 and 22, with 1 being very far away, and 22 being very close (22 not available for all Solutions). In a general use case, most users will only need values between 15 and 22.
   * If you are developing using the JavaScript SDK for Google Maps, the value must be an integer. If you are developing for Android or iOS, or using a different map provider, the value may be fractional.
3. **Zoom to** - Sets the maximum Zoom Level at which the 3D model is visible.
   * The value should be a number between 1 and 22, with 1 being very far away, and 22 being very close (22 not available for all Solutions). In a general use case, most users will only need values between 15 and 22.
   * If you are developing using the JavaScript SDK for Google Maps, the value must be an integer. If you are developing for Android or iOS, or using a different map provider, the value may be fractional.
4. **3D Model** - Open the Media Library, where you can choose which 3D model to display.
   * The Media Library is a tool to select the displayed model from either a pre-loaded selection of models, or for you to upload your own.
   * In-app, you can provide a URL to a desired model.
5. **X-axis rotation** - Controls the rotation of the model on the X-axis.
   * This value is the rotation on a given axis measured in degrees, and can be any value between 0 and 360.
6. **Y-axis rotation** - Controls the rotation of the model on the Y-axis.
   * This value is the rotation on a given axis measured in degrees, and can be any value between 0 and 360.
7. **Z-axis rotation** - Controls the rotation of the model on the Z-axis.
   * This value is the rotation on a given axis measured in degrees, and can be any value between 0 and 360.
8. **Scale** - Control the scale of the model. Can be used to make the model larger or smaller on the map.
   * This value is a multiplier in relation to the original size of the uploaded model. The ability to deisgnate specific measurements will be added in a future update.
