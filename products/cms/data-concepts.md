---
description: >-
  The MapsIndoors Content Management System (CMS) enables you to add, edit, and
  maintain your data within the MapsIndoors platform.
cover: broken-reference
coverY: 0
layout:
  cover:
    visible: false
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

# Data Concepts

Your data is structured in a hierarchy where the top level is your Solution which can contain multiple Venues, which can have numerous Buildings, and so on. The data types are in hierarchical order:

* **Solution**
  * A Solution is the topmost level of your data structure. It encompasses all the Venues, Buildings, and Locations you need for your MapsIndoors implementation. It is possible to have more than one Solution, but for the vast majority of use cases, you will just have one.
* **Venues**
  * A Venue is the second level of data. You can have multiple Venues in one Solution. For example, a large company might have various offices across the country. Each of these could be a Venue under the same Solution. A Venue can consist of one or more Buildings. For example, a sports stadium might be many Buildings, but it is all considered the same Venue.
* **Buildings**
  * A Building is a collection of Floors. Inside a Venue, you can have one or more Buildings and an Outside area. Outside is categorized together with Buildings because it is part of what comprises a Venue.
* **Floors**
  * Floors are the levels that exist within a Building. A Building has at least one Floor.
* **Locations**
  * There are three kinds of Locations: Rooms, Areas, and Points of Interest (POIs).
    * A Room is a multi-point polygon representing the walls of the physical Room, like a meeting room or a restroom.
    * A POI is a point on the map that can be added and moved in the CMS. POIs do not have any polygon data as it consists of only one point. Some examples of POIs are locations not available in Floor plan drawings, such as coffee machines, food trucks, or temporary event structures.
    * An Area is a multi-point polygon that can also be added and moved in the CMS. It is used to indicate that a section of a place has a particular significance, or if you want to display something like a desk on the map. Areas don't have walls, which is what makes them different from Rooms data-wise.
