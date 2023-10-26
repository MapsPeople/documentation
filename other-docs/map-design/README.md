---
description: Best practices for amazing looking maps
cover: ../../.gitbook/assets/3D Day View.jpg
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
    visible: true
---

# Map Design

{% hint style="success" %}
All our solutions come with a **default design system**, which has been carefully crafted to look amazing in most scenarios. [Take a look.](https://www.mapsindoors.com/design)
{% endhint %}

## Basic Styling

Use our CMS to do basic styling using display rules. Here is a rundown of the available handles; you can tweak them to whatever you like.

### Icon

<figure><img src="../../.gitbook/assets/CleanShot 2023-10-26 at 12.53.55@2x.png" alt=""><figcaption></figcaption></figure>

* Use **.svg** file format for the best resolution
* **Flatten and outline** the SVG in a vector software like Figma before uploading

### Polygon

<figure><img src="../../.gitbook/assets/CleanShot 2023-10-26 at 12.57.49@2x.png" alt=""><figcaption></figcaption></figure>

* Use a **HEX code** to change the **fill and stroke** color

### 2D Model

<figure><img src="../../.gitbook/assets/CleanShot 2023-10-26 at 13.01.01@2x.png" alt=""><figcaption></figcaption></figure>

* Upload a **.png** image for the best results
* Keep the file size **as low as possible** for best performance

### 3D Walls

<figure><img src="../../.gitbook/assets/CleanShot 2023-10-26 at 13.03.01@2x.png" alt=""><figcaption></figcaption></figure>

* Add walls around the area polygon
* **Height**, **color,** and **zoom level visibility** are customizable

### 3D Extrusion

<figure><img src="../../.gitbook/assets/CleanShot 2023-10-26 at 13.04.39@2x.png" alt=""><figcaption></figcaption></figure>

* Extrude the entire area polygon upwards
* **Height**, **color,** and **zoom level visibility** are customizable

### 3D Model

<figure><img src="../../.gitbook/assets/CleanShot 2023-10-26 at 13.05.25@2x.png" alt=""><figcaption></figcaption></figure>

* Use **.glb** file format, and try to optimize the **poly count** to as low a number as possible
* A good scalable file size to aim for is **25kb - 250kb**
* Use **real measurements** for the easiest placement **(meters)**

***

## Advanced Styling

### How to elevate a 3D model

The model's origin point will be placed on the ground, so to create the illusion that a model is up in the air, **move the model inside your 3D software without moving the origin point.**

<figure><img src="../../.gitbook/assets/CleanShot 2023-10-26 at 13.19.21@2x.png" alt=""><figcaption></figcaption></figure>
