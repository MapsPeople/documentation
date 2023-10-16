---
description: Web v4
---

# External IDs

The ExternalID is a reference from your real-life data to a piece of MapsIndoors geodata.

In a large venue like a conference hall, headquarter, or university, every room will have a unique ID like `1.234AB` or `HALL_A` in a naming scheme that makes sense to that organisation.

In MapsIndoors, we create all Rooms, Buildings, and Venues with an internal id that is a unique identifier, and it will not change. However, you might need to change the ID for a particular room in your physical building. It might be a large meeting room that is now split in two smaller rooms and one of them keeps that original ID. The ExternalID should then reflect your naming scheme, and not concern itself with the internal random identifier our database handed out to any of your rooms, as they will now be two new ones.

Previously, regardless of the type of geodata, we called it `RoomID`. This is less than optimal when a `RoomID` refers to a Building, or even a whole Venue, hence the change.

### Advanced Uses of ExternalID[​](https://docs.mapsindoors.com/external-id#advanced-uses-of-externalid) <a href="#advanced-uses-of-externalid" id="advanced-uses-of-externalid"></a>

You can also use the ExternalID as an identifier for an external system of your choosing. If you have a queue monitoring system and want to display some regularly updated statuses on a piece of geodata in MapsIndoors, you can use the ExternalID as the common denominator between the systems.

There are many ways you can utilise the power of ExternalID as a reference point for one of your systems, and we recommend looking at the [Integration API documentation](https://docs.mapsindoors.com/api/) and [getting in touch](https://resources.mapspeople.com/contact-us) to hear more about your options with this feature.

## Getting Locations by External ID

A method on MapsIndoors is available to retrieve locations by their externalId. This is only for exact matches and is not a part of the general search implementations, so must be specifically implemented.

The function on all 3 platforms functions similarly, returning an Array or List of Locations that match the supplied External ID strings.

Running the below snippet will return an Array of `MPLocations`.

```
locationsService.getLocationsByExternalId(externalIds)
```
