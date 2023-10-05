---
description: >-
  The MapsIndoors Content Management System (CMS) enables you to add, edit, and
  maintain your data within the MapsIndoors platform.
cover: ../../.gitbook/assets/Frame 47317.jpg
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

# CMS

Your data is structured in a hierarchy where the top level is your Solution which can contain multiple Venues, which can have numerous Buildings, and so on. The data types are in hierarchical order:

* **Solution**
  * A Solution is the topmost level of your data structure. It encompasses all the Venues, Buildings, and Locations you need for your MapsIndoors implementation. It is possible to have more than one Solution, but for the vast majority of use cases, you will just have one.
* **Venues**
  * A Venue is the second level of data. You can have multiple Venues in one Solution. For example, a large company might have various offices across the country. Each of these could be a Venue but under the same Solution. A Venue can consist of one or more Buildings. For example, a sports stadium might be many Buildings, but it is all considered the same Venue.
* **Buildings**
  * A Building is a collection of Floors. Inside a Venue, you can have one or more Buildings and an Outside area. Outside is categorized together with Buildings because it is part of what comprises a Venue.
* **Floors**
  * Floors are the levels that exist within a Building. A Building has at least one Floor.
* **Locations**
  * There are three kinds of Locations: Rooms, Areas, and Points of Interest (POIs).
    * A Room is a multi-point polygon representing the walls of the physical Room, like a meeting room or a restroom.
    * A POI is a point on the Map that can be added and moved in the CMS. POIs do not have any polygon data as it consists of only one point. Some examples of POIs are locations not available in Floor plan drawings, such as coffee machines, food trucks, or temporary event structures.

## Pages

The CMS has two main pages, "Map" and "Solution Details." The hierarchical structure is like this:

* **Map** - The main page where you manage your data on the map.
* **Solution Details** - A "behind-the-scenes" page where you administrate things such as Categories, Types, Visibility, etc.
  * **Types** - Defines "types" of Locations. Types work as specific identifiers for a group of Locations such as "Canteen," "Bathroom," etc.
  * **Categories** - Categories are a way to group or organize Locations for easier browsing in your application. Categories can be used to group Locations in bundles regardless of their Type.
  * **Type Visibility** - Defines the Zoom Levels for which the Locations of each Type will appear on the Map.
  * **Buildings** - An overview of the Buildings present in your Venue.
  * **Venues** - An overview of the Venues present in your Solution.
  * **App Settings** - A page with various settings concerning your app.
    * **App Configuration** - Settings to configure your app.
    * **API Keys** - API Keys used by your Solution.
    * **Booking Provider** - Settings for the booking provider you use for your Solution.
    * **Position Provider** - Settings for the position provider you use for your Solution.
    * **Webex** - Settings for your Cisco Webex Integration.

## User roles

In the CMS, there are different levels of users, which affects what you have access to. For example, an "Admin-level user can access Solution-level settings, whereas an "Editor" primarily can create and edit Locations on the Map. This documentation is written with an **Admin** user level in mind.

* **Administrator** - Administrators have full access to all menu points and options shown in the list above.
* **Editor** - Editors have more limited access than Administrators, being limited to creating new Locations alongside editing and deleting existing Locations. This gives you access to the following tools:
  * **Add POI** - Creates a Point of Interest by clicking on the Map. Afterwards, it opens an editor where Location details can be adjusted.
  * **Add Area** - Creates an Area by clicking on the Map to create corners of a polygon. Afterwards, it opens an editor where Area details can be adjusted.
  * All Display Filter options.
  * The following Location Editor Options:
    * **Type** - Locations must have a Type applied, which can be set in the Location details editor. When creating a new Location, some settings are inherited from the selected Type, e.g., Name and Icon. You can always change the inherited settings to something else if necessary.
    * **Name & Description** - Type in the name of your Location and a Description. Entering it in the default language is mandatory, but you also have the option to enter it in alternative languages.
    * **Categories** - Add which, if any, Categories this Location belongs to.
    * **External ID** - You can define an External ID that a Location should use alongside its internal ID.
    * **Area** - Choose the rotation of the Area the Location covers.
    * **Search** - Other search terms can be searched and still return this Location, even if it does not match the Name, Type, or Category.
    * **Restrictions** - Determine which, if any, App User Role Restrictions this Location should be subject to.
    * **Visibility** - If your Location is only displayed and searchable for a given period, you can define that here.
    * **Image** - Assign an image to be used for this Location.
    * **Custom Properties** - MapsIndoors supports Custom Properties, defined by key-value pairs.
    * **Details** - Select which Building and Floor this Location should belong to.
