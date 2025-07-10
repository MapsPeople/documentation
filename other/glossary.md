---
icon: book-open-reader
---

# Glossary

MapsIndoors, a versatile mapping platform by MapsPeople, offers detailed maps for both indoor and outdoor areas. It enables you to create search and navigation experiences tailored to your native users.

As a MapsIndoors customer, you gain access to our team of GeoData specialists, who will digitize your floor plans or blueprints and create a route network. This data is then developed into a "Solution" within the MapsIndoors system. You can manage your information using our Content Management System, the MapsIndoors CMS. Here, you can create unique "Locations" for each meeting room or desk space and establish various User Roles, granting different access permissions from visitors to executives.

You can also access our frameworks and SDKs for mobile and web development. These allow you to integrate MapsIndoors into your existing app or create a brand-new wayfinding app from scratch.&#x20;

#### **Solution** <a href="#solution" id="solution"></a>

A Solution is a digital map with a comprehensive set of configurations and data tailored to address the specific needs or requirements of an organization.

A Solution contains one or more Venues. Each Venue is made up of one or more Buildings. Each Building has one or more Floors. Each Floor has one or more Locations in the form of Rooms or stand-alone Points of Interest.

A Solution also contains a route network ( automatically or manually drawn ), and tools to manage and update the digital maps.

#### Venue​ <a href="#venue" id="venue"></a>

A Venue is a geographical area that typically includes one or more buildings. A Solution can encompass one or multiple venues.

#### Building​ <a href="#building" id="building"></a>

A Building occupies a space within a Venue and is composed of one or more floors.

#### Outdoor​ <a href="#outdoor" id="outdoor"></a>

Outdoor is the space inside the Venue Graph bounds but outside a Building.

#### Location / Place / POI​ <a href="#location--place--poi" id="location--place--poi"></a>

A Location is an item that provides information about what is situated at a specific geographical point. A location can represent anything, such as an office desk, a conference exhibitor, or a room feature (e.g., a fire hose, coffee machine, TV).

#### Room​ <a href="#room" id="room"></a>

A Room is a Location with polygon data. A Room might have one or more Doors connecting it with one or multiple other Rooms.

#### Area​ <a href="#area" id="area"></a>

An Area is a polygon like a Room, but it doesn't have Doors. An Area can encompass any portion of a room, floor, or building, as needed.

#### Tiles​ <a href="#tiles" id="tiles"></a>

Tiles are geographical drawings made by MapsIndoors overlaid on base maps (MapBox or Google Maps). The map is constructed by stitching together multiple tiles to cover the desired region. Tiles are served as .pngs.

#### Type​ <a href="#type" id="type"></a>

All Locations belong to a Type. Types are specific identifiers for a group of Locations, e.g. Unisex Toilet, Gold Sponsor, or Vegetarian Restaurant.

**Type Visibility​**

For Types, it is possible to control at which zoom level the Locations of a specific Type should be displayed.

Generally, higher zoom levels (i.e. 1-17) are good for displaying general features of the area e.g. Parking Lots and Toilets, while the lower zoom levels (i.e. 18-22) are better for gradually displaying more and more specific types, e.g. Meeting Rooms, Desks or Power Outlets.

#### Category​ <a href="#category" id="category"></a>

Categories can be used to group Locations in bundles regardless of their type. E.g. a Location of the type of Vegetarian Restaurant can be grouped with a Location of the type of Barbeque Restaurant in the Category "Restaurants".

**Marker / Icon​**

All Locations have an Icon displaying their presence on the map. Locations use the Icon from the Type they belong to unless it is overwritten on the Location itself. Icons can be uploaded in the CMS, or a set of standard icons from MapsIndoors can be used.

**Label​**

The Label is the text string next to the Icon on the map.

**Connector**​

A Connector is the point at which a person can get from one Floor to another in a Building. The Connectors can be an Elevator, Stairs, or Escalator.

#### Door​ <a href="#door" id="door"></a>

A Door connects two Rooms, or provides access from outside a Building to inside.

Automatically calculating the Route from an Origin to a Destination essentially means calculating the path from Door to Door between the Origin and Destination while avoiding all walls.

**Elevator Door​**

A specific type of Door that has the same properties as a regular Door, i.e. that it can be restricted to specific App User Roles or locked completely.

#### Network / Route Graph​ <a href="#network--route-graph" id="network--route-graph"></a>

A Network is a Graph instructing how to move around outdoors in a Venue or inside a Building. For Indoor spaces, the Graph is either manually created by the MapsPeople team or automatically generated using doors to map out how a person can move from one Door to another. For Outdoor spaces, the Graph is drawn manually by the MapsPeople Team.

**Node​**

The Network Graph consists of Edges connected by Nodes. Moving from one point to another on the Network Graph, a person is instructed to move on the Edges via the Nodes.&#x20;

**Edge / Way / Path​**

An edge is a line on which a person can be instructed to transport themselves. The Network Graph consists of Edges connected by Nodes. Edges are always straight lines, but Nodes can be used in quick succession to create the impression of a curved Edge.

**Venue Bounds​**

Bounds are used to indicate the geographical area a Venue or Building occupies. The Venue Bounds can be altered to fit points where it can conveniently connect to the base map network, in conjunction with the Graph Bounds. This is to ensure a smooth transition between the directions given by the base map and MapsIndoors.

**Graph Bounds**

Graph Bounds describe the area covered by the route graph. This may be smaller than the Venue Bounds.&#x20;

#### Entry Point / Entryway​ <a href="#entry-point--entryway" id="entry-point--entryway"></a>

Entry Points are specified points in a MapsIndoors Venue that enable a transition between a global or regional map and the local map in MapsIndoors. The Entry Point often specifies which travel modes are suitable for entering/exiting the Venue. \
\
There are four travel modes: _Walking_, _Bicycling_, _Driving,_ and _Transit_ (Public Transportation). As such, the Entry Point may be a bike shed for the Bicycle travel mode, a carpark for Driving, and a bus stop for Transit. As a consequence, it is often at the Entry Point that the Travel Mode changes from Bicycling, Driving or Transit to Walking. \
\
The selection of an entry point for transitioning between route networks is based on a combination of automatic calculation, estimation, and optimization.

#### Directions <a href="#directions" id="directions"></a>

Directions are the set of instructions for traveling from an Origin to a Destination.

You can get Directions for a Route using the four Travel Modes: "Walking", "Driving", "Biking" and "Public Transit".

For Directions where the Origin and Destination are both inside a Venue (as defined by the Venue Bounds), Walking is the only Travel Mode available.

For Directions where the Origin or Destination is outside a Venue, MapsIndoors will provide Directions inside the Venue and the base map will provide Directions outside the Venue.

**Route**&#x20;

A Route is the specific set of instructions getting from an Origin to a Destination with all of the other parameters like Travel Mode, public transit information, etc. outside of the response.

#### Route Leg​ <a href="#route-leg" id="route-leg"></a>

A Leg represents a logical subset of the journey from Origin to Destination. A Route will break into Legs when:

* Traveling from one-floor level to another.&#x20;
* Changing context, such as entering or exiting a building.
* Changing travel mode, for example, parking your car and continuing by foot.

#### Direction Step / Route Step​ <a href="#direction-step--route-step" id="direction-step--route-step"></a>

A Route Step can have different representations depending on where on a Route it is placed. A Step may represent yet another subset of the journey within a Leg. Furthermore, it may represent a required action and/or maneuver, such as traversing floors, changing directions (Left, Right etc.).&#x20;

A step will also contain textual instructions. Examples include “Make a right turn”, “Continue straight ahead”, “Take the elevator to Floor 4” and the like.

#### Turn-by-turn​ <a href="#turn-by-turn" id="turn-by-turn"></a>

"Turn-by-turn navigation" means the current Direction Step change when the physical location of the person navigating changes. The current Direction Step might instruct the person to "Make a right turn" and based on their location after they have completed that step, the current Direction Step changes automatically to "Take the elevator to Floor 4".

#### Bus/Tram/Train Stop​ <a href="#bustramtrain-stop" id="bustramtrain-stop"></a>

A "Public Transit"-only Entry Point inside the Venue is understood to be the point from which MapsIndoors starts the walking directions.

#### Parking Lot​ <a href="#parking-lot" id="parking-lot"></a>

A Parking Lot is a "Driving"- or "Biking"-specific Entry Point. It will provide a suggestion to park the car or bike at the Parking Lot closest to the Destination. It only displays a Parking Lot when requesting "Driving" or "Biking" directions from outside a Venue to a Location inside a Venue.

