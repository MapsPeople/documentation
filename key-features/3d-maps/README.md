---
cover: ../../.gitbook/assets/3dghighlightimage (1).jpg
coverY: 0
layout:
  cover:
    visible: true
    size: hero
  title:
    visible: true
  description:
    visible: true
  tableOfContents:
    visible: true
  outline:
    visible: true
  pagination:
    visible: false
---

# 3D Maps

Starting with the V4 versions of our SDKs, MapsIndoors has introduced the functionality to have your map viewed in 3D, with the appropriate navigational features, rather than just a flat 2D image.

### Requirements[​](https://docs.mapsindoors.com/3d-maps#requirements) <a href="#requirements" id="requirements"></a>

Regardless of your app platform, you need to use the **Mapbox** map provider in order to use this functionality. You also need to be using V4 of our MapsIndoors SDK's, the specific minimum version depending on the platform.

* Android SDK v4.0.0
* Web SDK v4.18.0
* iOS SDK v4.2.6

If you fulfill the above requirements, you can contact your MapsPeople representative to have the 3D map functionality enabled for your Solution(s).

### Why 3D Maps?[​](https://docs.mapsindoors.com/3d-maps#why-3d-maps) <a href="#why-3d-maps" id="why-3d-maps"></a>

3D indoor mapping solutions, such as MapsIndoors, can provide companies with a multitude of benefits by creating a detailed and interactive representation of their indoor spaces. One of the primary advantages of using 3D maps is the ability to enhance the overall user experience for employees, visitors, and customers. These interactive maps can help users effortlessly navigate complex facilities such as corporate campuses, shopping malls, airports, or hospitals. By offering a user-friendly interface and intuitive visual aids, 3D maps can facilitate wayfinding, reduce confusion, and improve overall satisfaction for anyone interacting with the indoor environment.

In addition to improving user experience, 3D indoor mapping solutions like MapsIndoors can significantly streamline various operations within a company, leading to increased efficiency and cost savings. With the integration of real-time data, facility managers can optimize space utilization according to the needs of the company. Furthermore, 3D maps can be integrated with other select enterprise systems supported by MapsIndoors, such as asset tracking and user positioning, to create a comprehensive and connected ecosystem that enhances the overall functionality and productivity of the organization. The value of using 3D maps for indoor mapping solutions is multifaceted and can ultimately contribute to a more efficient, user-friendly, and well-managed working environment.

### Best Practices[​](https://docs.mapsindoors.com/3d-maps#best-practices) <a href="#best-practices" id="best-practices"></a>

While exact best practices will depend on your specific solution and implementation, we can provide some general pointers of things to consider when developing your solution to work with MapsIndoors 3D maps. Most of these will be specifically pertaining to the inclusion of 3D models in your solution, not to utilising 3D walls and 3D room extrusions.

* Use .gltf file format
* Ideally 25-100kb size. You go can higher if you want, just be aware that it impacts load time.
* Keep your models as low-poly as possible.
* Consider whether all your locations need a 3D model.
* Where possible, consider reusing the same model, instead of uploading 3 different models with only minor variations.
