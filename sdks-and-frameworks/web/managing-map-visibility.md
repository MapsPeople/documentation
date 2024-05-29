# Managing map visibility

With MapsIndoors, you have a range of options for controlling how content is styled on the map, and how your users can interact with it. This guide takes you through some of these options, to help you find the best approach.

### Visibility and rendering

The options you have for controlling visibility and rendering on the map are the following:

* Setting "Active From" and "Active To" dates
* Setting Restrictions to "Closed for all"
* Display Rules: Set Visibility and Opacity values

They generally fall into two categories:

1. The data is sent to the SDK, but not rendered on the map
2. The data is not sent to the SDK

In the first case, you can change the data to become **not** visible. It works like this:

* In the Display Rule, you can set the "General Visibility" to `false`, and nothing of the Location will render; neither the icon, nor the label, polygon, 2D Model, 3D Walls, 3D Extrusions or 3D Models (all of these parts of the Location's Display Rule can of course also be controlled individually)
* At runtime, either based on user input or other logic in your app, you can change the visilbity to `true`, rendering it visible on the map

For the second case, the SDK effectively doesn't even know it exists:

* If you set an Active From and Active To date, and request the data outside of that date range, the data is not sent to the SDK
* If you set the Restriction on a Location to "Closed for all", or an App User Role that you then don't request the Solution data with, it's also not available to the SDK

### Selectable and searchable

Besides the rendering options related to Display Rules and Restrictions, you can also control whether a Location is "Selectable" and "Searchable".

Setting a Location to be Not Selectable, it is still rendered on the map with all of the visible parts of it, but you can not select it to see more details and get directions to it. In practice, you would likely use "Not Selectable" for office decoration like plants or a statue — elements that help create a great-looking environment on your map.&#x20;

All of these Locations should also have Searchable turned off, given they are for decoration purposes. In practice, you _can_ set them to be Searchable, but you end up with a list of search results full of Locations you should not navigate to. Turn off "Searchable" on your "Not Selectable" Locations to avoid problems.

Other times, you want to keep the Locations selectable on the map, but maybe not take up space in the search result lists. For those cases, turn off Searchable.
