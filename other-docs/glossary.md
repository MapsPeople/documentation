# Glossary

MapsIndoors is a mapping platform designed to help users navigate your venues and buildings using a digital solution, either on their own smartphone or a kiosk that you provide at certain locations.

As a customer purchasing MapsIndoors, you get access to our team of GeoData specialists. They will convert your floor plans or building blueprints into digital information, alongside a route network, which will then be created as a "Solution" in the MapsIndoors system. You can manage your information through our content management system, the MapsIndoors CMS. Here you can create "Locations", making each meeting room or desk-space unique and you can create different User Roles, providing different access permissions from visitors to executives.

You also gain access to our SDKs for both Android and iOS for mobile developers and a JavaScript SDK for web development. Using these SDKs, you can integrate MapsIndoors into your own app or create a whole new wayfinding app from scratch. This Documentation site gives you information on the various features present and how to use them.

#### Solution[​](https://docs.mapsindoors.com/glossary#solution) <a href="#solution" id="solution"></a>

A Solution is a project for a MapsIndoors Customer.

A Solution contains one or more Venues. Each Venue is made up of one or more Buildings. Each Building has one or more Floors. Each Floor has one or more Locations in the form of Rooms or stand-alone Points of Interest.

A Solution also contains the Map tiles, and the Route Graph (which is either manually or automatically drawn).

#### Tiles[​](https://docs.mapsindoors.com/glossary#tiles) <a href="#tiles" id="tiles"></a>

Tiles are the drawings made by MapsIndoors overlaid on the Google Maps map.

Tiles are served as .pngs.

#### Venue[​](https://docs.mapsindoors.com/glossary#venue) <a href="#venue" id="venue"></a>

A Venue is a geographical area that (usually) contains one or more Buildings.

#### Outdoor[​](https://docs.mapsindoors.com/glossary#outdoor) <a href="#outdoor" id="outdoor"></a>

The space inside the Venue Graph bounds that is not inside a Building.

#### Building[​](https://docs.mapsindoors.com/glossary#building) <a href="#building" id="building"></a>

A Building covers an area within a Venue. A Building consists of one or more Floors.

#### Connector[​](https://docs.mapsindoors.com/glossary#connector) <a href="#connector" id="connector"></a>

A Connector is the point at which a person can get from one Floor to another in a Building.

The Connectors can be an Elevator, Stairs or Escalator.

#### Location / Place / POI[​](https://docs.mapsindoors.com/glossary#location--place--poi) <a href="#location--place--poi" id="location--place--poi"></a>

A Location is an item with data about what is located at that geographical point. A Location can be anything; an office desk, a conference exhibitor, or a room feature (firehose, coffee machine, TV etc.).

Location, Place and POI ("Point of Interest") are used interchangeably in everyday speech. MapsIndoors relies on Locations as the word to describe a geographical point.

See also Rooms.

#### Room[​](https://docs.mapsindoors.com/glossary#room) <a href="#room" id="room"></a>

A Room is a Location with polygon data.

A Room might have one or more Doors connecting it with another or multiple other Rooms.

#### Area[​](https://docs.mapsindoors.com/glossary#area) <a href="#area" id="area"></a>

An Area is a polygon like a Room, but it doesn't have Doors. It is the third kind of Location besides Room and POI. An Area can cover as little or as much of a Room, Floor or Building as makes sense.

#### Door[​](https://docs.mapsindoors.com/glossary#door) <a href="#door" id="door"></a>

A Door is a connection between two Rooms, or from outside a Building and into it.

Automatically calculating the Route from an Origin to a Destination in reality means calculating how to get from Door to Door between the Origin and Destination while avoiding all Walls.

**Elevator Door**[**​**](https://docs.mapsindoors.com/glossary#elevator-door)

A specific type of Door that has the same properties as a regular Door, i.e. that it can be restricted to specific App User Roles or locked completely.

**Building Entrance**[**​**](https://docs.mapsindoors.com/glossary#building-entrance)

A Building Entrance is a Door at the outer edge of a Building.

#### Category[​](https://docs.mapsindoors.com/glossary#category) <a href="#category" id="category"></a>

Categories can be used to group Locations in bundles regardless of their type. E.g. a Location of the type Vegetarian Restaurant can be grouped with a Location of the type Barbeque Restaurant in the Category "Restaurants".

#### Type[​](https://docs.mapsindoors.com/glossary#type) <a href="#type" id="type"></a>

All Locations belong to a Type. Types are specific identifiers for a group of Locations, e.g. Unisex Toilet, Gold Sponsor, or Vegetarian Restaurant.

**Type Visibility**[**​**](https://docs.mapsindoors.com/glossary#type-visibility)

For Types, it is possible to control at which zoom level the Locations of a specific Type will be displayed.

Generally, higher zoom levels (i.e. 1-17) are good for displaying general features of the area e.g. Parking Lots and Toilets while the lower zoom levels (i.e. 18-22) are better for gradually displaying more and more specific types, e.g. Meeting Rooms, Desks or Power Outlets.

**Marker / Icon**[**​**](https://docs.mapsindoors.com/glossary#marker--icon)

All Locations have an Icon displaying its presence on the map.

Locations use the Icon from the Type they belong to, unless it is overwritten on the Location itself.

Icons can be uploaded in the CMS, or a set of standard icons from MapsIndoors can be used.

**Label**[**​**](https://docs.mapsindoors.com/glossary#label)

The Label is the text string next to the Icon on the map.

#### Network / Route Graph[​](https://docs.mapsindoors.com/glossary#network--route-graph) <a href="#network--route-graph" id="network--route-graph"></a>

A Network is the Graph instructing how to move around outdoors in a Venue or inside a Building.

Inside a Building, the Network Graph is either drawn manually by the MapsPeople Team, or automatically using Doors to determine how a person can move inside a Room from one Door to any other.

Outdoors, the Network Graph is drawn manually by the MapsPeople Team.

**Node**[**​**](https://docs.mapsindoors.com/glossary#node)

The Network Graph consists of Edges connected by Nodes. Moving from one point to another on the Network Graph, a person is instructed to move on the Edges via the Nodes.

As Edges are always straight lines, Nodes are also used in quick succession to create the impression of a curved Edge.

**Edge / Way / Path**[**​**](https://docs.mapsindoors.com/glossary#edge--way--path)

An edge is a line on which a person can be instructed to transport themselves. The Network Graph consists of Edges connected by Nodes.

Edges are always straight lines, but Nodes can be used in quick succession to create the impression of a curved Edge.

**Venue / Graph / Building Bounds**[**​**](https://docs.mapsindoors.com/glossary#venue--graph--building-bounds)

Bounds are used to indicate the geographical area a Venue or Building occupies.

The Venue Bounds can be altered to fit points where it can conveniently connect to the Google Maps network, in conjunction with the Graph Bounds. This is to ensure a smooth transition between the directions given by Google Maps and MapsIndoors.

Graph Bounds describe the area covered by the route graph. This may be smaller than the Venue Bounds.

The Building Bounds are used to accurately display the area a Building occupies in the world.

#### Directions[​](https://docs.mapsindoors.com/glossary#directions) <a href="#directions" id="directions"></a>

Directions is the set of instructions for travelling from an Origin to a Destination.

You can get Directions for a Route using the four Travel Modes: "Walking", "Driving", "Biking" and "Public Transit".

For Directions where the Origin and Destination are both inside a Venue (as defined by the Venue Bounds), Walking is the only Travel Mode available.

For Directions where the Origin or Destination are outside a Venue, MapsIndoors will provide Directions inside the Venue and Google Maps will provide Directions outside the Venue.

The main difference between Directions and Routes are the scope of what is included in the responses. Getting Directions response includes multiple routes to choose from, information about the Travel Mode, traffic, and the like. A Route is the specific set of instructions getting from an Origin to a Destination with all of the other parameters like Travel Mode, public transit information etc. outside of the response.

#### Route Leg[​](https://docs.mapsindoors.com/glossary#route-leg) <a href="#route-leg" id="route-leg"></a>

A Leg represents a logical subset of the journey from Origin to Destination. A Route will break into Legs when:

* Travelling from one floor level to another. \{% if 'web/v4/' not in page.url %\}
* Changing context, such as entering or exiting a building.
* Changing travel mode, for example parking your car and continuing by foot. \{% endif %\}

If you examine the illustration above, you will see that the blue line representing the Route have been marked with blue circles where the Route would be seperated into Legs.

#### Direction Step / Route Step[​](https://docs.mapsindoors.com/glossary#direction-step--route-step) <a href="#direction-step--route-step" id="direction-step--route-step"></a>

A Route Step can have different representations depending on where on a Route it is placed. A Step may represent yet another subset of the journey within a Leg. Furthermore, it may represent a required action and/or maneuver, such as traversing floors, changing directions (Left, Right etc.). A step will also contain textual instructions. Examples include “Make a right turn”, “Continue straight ahead”, “Take the elevator to Floor 4” and the like.

#### Turn-by-turn[​](https://docs.mapsindoors.com/glossary#turn-by-turn) <a href="#turn-by-turn" id="turn-by-turn"></a>

"Turn-by-turn navigation" means the current Direction Step change when the physical location of the person navigating changes. The current Direction Step might instruct the person to "Make a right turn" and based on their location after they have completed that step, the current Direction Step changes automatically to "Take the elevator to Floor 4".

#### Entry Point / Entryway[​](https://docs.mapsindoors.com/glossary#entry-point--entryway) <a href="#entry-point--entryway" id="entry-point--entryway"></a>

Entry Points are specified points in a MapsIndoors Venue that enable a transition between a global or regional map and the local map in MapsIndoors. The Entry Point often specify which travel modes are suitable for entering/exiting the Venue. There are four travel modes: _Walking_, _Bicycling_, _Driving_ and _Transit_ (Public Transportation). As such, the Entry Point may be a bike shed for the Bicycling travel mode, a carpark for Driving and a bus stop for Transit. As a consequence, it is often at the Entry Point that the Travel Mode changes from Bicycling, Driving or Transit to Walking. The selection of an entry point for transitioning between route networks is based on a combination of automatic calculation, estimation and optimisation.

#### Bus/Tram/Train Stop[​](https://docs.mapsindoors.com/glossary#bustramtrain-stop) <a href="#bustramtrain-stop" id="bustramtrain-stop"></a>

A "Public Transit"-only Entry Point inside the Venue is understood to be the point from which MapsIndoors starts the walking directions.

#### Parking Lot[​](https://docs.mapsindoors.com/glossary#parking-lot) <a href="#parking-lot" id="parking-lot"></a>

A Parking Lot is a "Driving"- or "Biking"-specific Entry Point. It will provide a suggestion to park the car or bike at the Parking Lot closest to the Destination. It only displays a Parking Lot when requesting "Driving" or "Biking" directions from outside a Venue to a Location inside a Venue.

#### Selection Highlight Color[​](https://docs.mapsindoors.com/glossary#selection-highlight-color) <a href="#selection-highlight-color" id="selection-highlight-color"></a>

To illustrate which piece of geodata is selected, we use a highlight color that is the highest possible contrast to the surroundings: Pink. You can read more about the [background for our choice on our MapsIndoors product blog](https://blog.mapspeople.com/new-selection-highlight-color).
