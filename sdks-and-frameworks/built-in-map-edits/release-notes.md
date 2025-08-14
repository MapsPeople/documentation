---
icon: sparkles
---

# Release notes

### \[1.1.0] - 2025-14-08

**Added**

* **Location Creation**: You can now create new locations using `solutionManager.createLocation()`.
* **Location Deletion**: Locations can now be deleted using the `delete()` method on a `LocationEditor` instance.
* **LocationEditor Events**:- New `save` and `delete` events are now dispatched upon successful location save and deletion, respectively.
* **Floor Management**: Added `setFloor()` and `getFloor()` methods to `LocationEditor`.
* **External ID Management**: Added `setExternalId()` and `getExternalId()` methods to `LocationEditor` for setting, updating, and retrieving a location's external ID. This allows linking MapsIndoors locations with external systems or identifiers.

#### Changed

* **Enhanced `MapsIndoorsEditor.getAvailableSolutions()`**: This method now returns a `Promise<Solution[]>` with a more clearly defined `Solution` interface, improving type safety and predictability.
* **Updated `SolutionManager.locationTypes`**: The `locationTypes` property now returns a `Map<string, Type>`, where `Type` is a newly defined interface, providing a more structured representation of location types.

### \[1.0.0] - 2025-04-01

#### Added

* **Real-time Visual Preview:** Users can now instantly see changes reflected in the visual preview as they make them.
* **Map Interaction:** Implemented drag-and-drop functionality to easily move locations on the map.
* **Synchronized Movement:** Connected 2D and 3D models now move together seamlessly, maintaining synchronization.
* **POI and Area Manipulation:** Users can now move and rotate points of interest (POIs) and areas (excluding rooms).
* **Location Metadata Editing:** Added the ability to update metadata, including names and descriptions for POIs, rooms, and areas.

