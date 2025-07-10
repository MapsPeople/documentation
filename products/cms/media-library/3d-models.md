---
cover: ../../../.gitbook/assets/3dmodels.jpg
coverY: 0
layout:
  width: default
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
    visible: true
---

# 3D Models

3D models in your MapsIndoors solution are uploaded and managed through the Media Library, just like 2D models and icons. However, due to the nature of 3D models, there are a few additional steps to manage, particularly when uploading your 3D model. 3D models can be uploaded to the Media Library in the `.glb` file format.

## Making performant 3D models

To make your models performant on your maps, you need to follow a few guidelines. Here's a quick guide to exporting proper models from Blender (a 3D modelling software).

### Fewer vertices result in smoother performance

To optimize performance, reduce polygons in your models to minimize data and machine power usage.

<figure><img src="../../../.gitbook/assets/CleanShot 2025-07-10 at 11.08.31.png" alt=""><figcaption><p>Workstation model. 50kb, 1300 verts.</p></figcaption></figure>

### Ensure proper .glb export

Here is a guide on which materials to include or exclude in your model exports for better compatibility with Mapbox. The parameters listed below should not deviate from their default settings:

<figure><img src="../../../.gitbook/assets/CleanShot 2025-07-10 at 11.12.04 (1).png" alt=""><figcaption><p>Always use "Principled BSDF" shader</p></figcaption></figure>

## Set Preview Image​ <a href="#set-preview-image" id="set-preview-image"></a>

<figure><img src="../../../.gitbook/assets/CleanShot 2025-07-10 at 10.56.56.png" alt=""><figcaption></figcaption></figure>

When you upload a 3D model to the Media Library, you'll need to set a _preview image_. This opens a viewport where you can move and rotate the 3D model to determine what image will display as a preview in both the Media Library and other CMS locations.

1. The widget you use to move your model. In this image, it is set to "Move", but will look differently when set to "Rotate".
2. Toggle between "Move" and "Rotate".
3. Zoom your viewport.
4. Enter values for rotation instead of using the widget.
5. Reset either the camera position, model rotation or model placement to the default values.
