# Synchronizing data for a subset of venues in MapsIndoors

__This article assumes that you have already read the [Getting Started guide.](../getting-started/README.md)__

In this article, we will discuss how to synchronize data for a subset of venues in MapsIndoors. There are multiple benefits to doing this, including performance and control over which data the user can search for.

Synchronizing data for a subset of venues reduces the time it takes to load the map, compared to synchronizing data for all venues. It also enables you to control which data the user can search for. For example, you may only want to synchronize data for venues located in a specific region.

To do this, you must not provide the MapsIndoors API key in the URL when loading the SDK. Instead, you set the MapsIndoors API key by calling the `mapsindoors.MapsIndoors.setMapsIndoorsApiKey('MAPSINDOORS_API_KEY');`` method before creating the MapsIndoors instance.

Here is an example of how to set which venues to synchronize data for, and then setting the MapsIndoors API key, to start the synchronization:

```javascript
// Add one or more venues to the list of venues to be synchronized.
mapsindoors.MapsIndoors.addVenuesToSync('FirstVenueId');
// Or
mapsindoors.MapsIndoors.addVenuesToSync(['FirstVenueId', 'SecondVenueId']);

// Set the MapsIndoors API key.
mapsindoors.MapsIndoors.setMapsIndoorsApiKey('MAPSINDOORS_API_KEY');
```

This will now synchronize data exclusively for these two venues, and only those venues.

You can also add one or more venues after the initial data synchronization:

```javascript
// Add one or more venues to the list of venues to be synchronized.
mapsindoors.MapsIndoors.addVenuesToSync('ThirdVenueId');
// Or
mapsindoors.MapsIndoors.addVenuesToSync(['ThirdVenueId', 'FourthVenueId']);
```

To remove the synchronized data for one or more venues:

```javascript
// Remove one or more venues from the list of venues to be synchronized.
mapsindoors.MapsIndoors.removeVenuesToSync('FirstVenueId');
// Or
mapsindoors.MapsIndoors.removeVenuesToSync(['FirstVenueId', 'ThridVenueId']);
```
