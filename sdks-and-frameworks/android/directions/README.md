---
description: Android V4
---

# Directions

As a key element in the MapsIndoors platform, we offer API's for effeciently calculating and displaying the most optimal routes from anywhere in the world to any Location inside a Building in MapsIndoors. In the case of travelling internally at a Venue, this calculation can be done on a local map provided by MapsIndoors. In the case of travelling between Venues or from outdoors to indoors, MapsIndoors provide a seamless journey outline from a specified _Origin_ through automatically selected _Entry Points_ at the edge of your Venues to the specified _destination_.

In order to provide a route between Venues, MapsIndoors integrate with external and global map providers. Our preferred provider is Google Maps.

The central components that utilize a Directions experience is the [Directions Service](https://docs.mapsindoors.com/directions-service/) and the [Directions Renderer](https://docs.mapsindoors.com/directions-renderer/). But before we get to the fun part, let's examine some key concepts first.

### Entry Points[​](https://docs.mapsindoors.com/directions-intro#entry-points) <a href="#entry-points" id="entry-points"></a>

Entry Points are specified points in a MapsIndoors Venue that enable a transition between a global or regional map and the local map in MapsIndoors. The Entry Point often specify which travel modes are suitable for entering/exiting the Venue. There are four travel modes: _Walking_, _Bicycling_, _Driving_ and _Transit_ (Public Transportation). As such, the Entry Point may be a bike shed for the Bicycling travel mode, a carpark for Driving and a bus stop for Transit. As a consequence, it is often at the Entry Point that the Travel Mode changes from Bicycling, Driving or Transit to Walking. The selection of an entry point for transitioning between route networks is based on a combination of automatic calculation, estimation and optimisation.

### The Route Model[​](https://docs.mapsindoors.com/directions-intro#the-route-model) <a href="#the-route-model" id="the-route-model"></a>

When requesting Routes in MapsIndoors Directions Service The Route model in MapsIndoors is seperated into Legs and these Legs are again seperated into Steps.

#### The Route Leg Model[​](https://docs.mapsindoors.com/directions-intro#the-route-leg-model) <a href="#the-route-leg-model" id="the-route-leg-model"></a>

A Leg represents a logical subset of the journey from Origin to Destination. A Route will break into Legs when:

* Travelling from one floor level to another.
* Changing context, such as entering or exiting a building.
* Changing travel mode, for example parking your car and continuing by foot.

If you examine the illustration above, you will see that the blue line representing the Route have been marked with blue circles where the Route would be seperated into Legs.

#### The Route Step Model[​](https://docs.mapsindoors.com/directions-intro#the-route-step-model) <a href="#the-route-step-model" id="the-route-step-model"></a>

A Route Step can have different representations depending on where on a Route it is placed. A Step may represent yet another subset of the journey within a Leg. Furthermore, it may represent a required action and/or maneuver, such as traversing floors, changing directions (Left, Right etc.). A step will also contain textual instructions. Examples include “Make a right turn”, “Continue straight ahead”, “Take the elevator to Floor 4” and the like.
