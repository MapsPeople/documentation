# Custom Floor Selector

## How to implement a custom floor selector

To implement a custom floor selector we expect you to already have a View with a Map using MapControl. [Show a map](getting-started/show-a-map.md#show-a-map-with-mapsindoors)

Start by creating a class, in this example named; `CustomFloorSelector`. Extend the class with `FrameLayout` and implement `MPFloorSelectorInterface` as well as its properties and methods.

```kotlin
class CustomFloorSelector(context: Context, attrs: AttributeSet?) : FrameLayout(context, attrs), MPFloorSelectorInterface {
    override val view: View?
        get() = TODO("Not yet implemented")

    override val isAutoFloorChangeEnabled: Boolean
        get() = TODO("Not yet implemented")

    override fun setOnFloorSelectionChangedListener(listener: OnFloorSelectionChangedListener?) {
        TODO("Not yet implemented")
    }

    override fun setList(floors: List<MPFloor>?) {
        TODO("Not yet implemented")
    }

    override fun show(show: Boolean, animated: Boolean) {
        TODO("Not yet implemented")
    }

    override fun setSelectedFloor(floor: MPFloor) {
        TODO("Not yet implemented")
    }

    override fun setSelectedFloorByZIndex(zIndex: Int) {
        TODO("Not yet implemented")
    }

    override fun zoomLevelChanged(newZoomLevel: Float) {
        TODO("Not yet implemented")
    }

    override fun setUserPositionFloor(zIndex: Int) {
        TODO("Not yet implemented")
    }
}
```

We also need to implement a simple view to show the current floor and allow the user to select another floor. We will do this by creating a simple `Button` that when clicked creates a drop down allowing the user to select any floor.

```xml
<?xml version="1.0" encoding="utf-8"?>
<androidx.constraintlayout.widget.ConstraintLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="40dp"
    android:layout_height="wrap_content"
    xmlns:app="http://schemas.android.com/apk/res-auto">
    <Button
        android:id="@+id/custom_floor_selector_btn"
        app:layout_constraintStart_toStartOf="parent"
        app:layout_constraintTop_toTopOf="parent"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="1"/>
</androidx.constraintlayout.widget.ConstraintLayout>
```

We can now start implementing logic into our `CustomFloorSelector` class. We will start by implementing the methods from the `MPFloorSelectorInterface`. We start by adding a `MutableMap<Int, MPFloor>` to the class to keep track of the current floors. We populate the list when `setList` is called. We add a `OnFloorSelectionChangedListener`, so that we can notify when floors have changed in the UI. For the `show` we will ignore the animated bool, to keep it simple and we just toggle the visibility for this example. We will ignore the `zoomLevelChanged` method in this example, but it allows you to implement logic in the custom floor selector based on the current zoom level. For this example we will allow automatic floor changes through `isAutoFloorChangeEnabled`, the `setUserPositionFloor` can be used to keep track of the current floor of the user, if using any type of indoor positioning service, together with MapsIndoors. For this example, we will save the user location, but will not add any logic based on it.

```kotlin
// Inside CustomFloorSelector
private val floorMap = mutableMapOf<Int, MPFloor>()
private var onFloorSelectionChangedListener: OnFloorSelectionChangedListener? = null
private var userFloorIndex: Int? = null

override fun setList(floors: List<MPFloor>?) {
    floorMap.clear()
    floors?.forEach {
        floorMap[it.floorIndex] = it
    }
}

override fun setOnFloorSelectionChangedListener(listener: OnFloorSelectionChangedListener?) {
    onFloorSelectionChangedListener = listener
}

override fun show(show: Boolean, animated: Boolean) {
    visibility = if (show) View.VISIBLE else View.GONE
}

override fun zoomLevelChanged(newZoomLevel: Float) {
    // ignore
}

override val isAutoFloorChangeEnabled: Boolean = true

override fun setUserPositionFloor(zIndex: Int) {
    userFloorIndex = zIndex
}
```

For the remaining methods we need to modify the UI, so we will start implementing the UI first. We inflate the view and hold a reference to it in the `view` property. Inside the `init` we will also create a reference to the `Button` that we implemented. The `floorButton` needs to open a dropdown when clicked we will do this with a `PopupMenu` that is ordered by the floor index. When a `MenuItem` is clicked we use the itemId to retrieve the correct floor from the floor map. When that is retrieved we update the `floorButton` text, for the now selected floor, as well as calling `onFloorSelectionChanged` with the selected floor. For the map to update and reflect the action.

```kotlin
// Inside CustomFloorSelector
private val floorButton: Button
private var selectedFloor: MPFloor? = null

override val view: View = inflate(context, R.layout.custom_floor_selector, this)

init {
    floorButton = findViewById(R.id.custom_floor_selector_btn)

    floorButton.setOnClickListener { button ->
        val popup = PopupMenu(context, button)
        floorMap.values.forEach { floor ->
            popup.menu.add(0, floor.floorIndex, floor.floorIndex, floor.displayName)
        }
        popup.setOnMenuItemClickListener { menuItem ->
            floorMap[menuItem.itemId]?.let { floor ->
                floorButton.text = floor.displayName
                onFloorSelectionChangedListener?.onFloorSelectionChanged(floor)
            }
            popup.dismiss()
            true
        }
        popup.show()
    }
}

override fun setSelectedFloor(floor: MPFloor) {
    selectedFloor = floor
    floorButton.text = floor.displayName
}

override fun setSelectedFloorByZIndex(zIndex: Int) {
    selectedFloor = floorMap[zIndex]
    floorButton.text = floorMap[zIndex]?.displayName
}
```

Now all that is left is adding this view component to your layout containing the map and adding it to your `MPMapConfig` when creating `MapControl`. Here is an example layout where the floor selector is added to a layout.

```xml
<?xml version="1.0" encoding="utf-8"?>
<androidx.coordinatorlayout.widget.CoordinatorLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="match_parent">
    <androidx.fragment.app.FragmentContainerView
        android:id="@+id/map_fragment"
        android:name="com.mapsindoors.testapp.MapFragment"
        android:layout_width="match_parent"
        android:layout_height="match_parent"/>
    <com.mapsindoors.testapp.CustomFloorSelector
        android:id="@+id/custom_floor_selector"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"/>
</androidx.coordinatorlayout.widget.CoordinatorLayout>
```

{% tabs %}
{% tab title="Google Maps" %}
```kotlin
private fun initMapControl(view: View) {
    val floorSelector = findViewById<CustomFloorSelector>(R.id.custom_floor_selector)
    val mapConfig = MPMapConfig.Builder(this, googleMap, getString(R.string.google_maps_key), view, true)
        .setFloorSelector(floorSelector)
        .build()
    MapControl.create(mapConfig) { mapControl, error ->
        // ...
    }
}
```
{% endtab %}

{% tab title="Mapbox" %}
```kotlin
private fun initMapControl() {
    val floorSelector = findViewById<CustomFloorSelector>(R.id.custom_floor_selector)
    val mapConfig = MPMapConfig.Builder(this, mapboxMap, mapView, getString(R.string.mapbox_access_token), true)
        .setFloorSelector(floorSelector)
        .build()
    MapControl.create(mapConfig) { mapControl, error ->
        // ...
    }
}
```
{% endtab %}
{% endtabs %}

You have now succesfully implemented a custom floor selector into your own app.
