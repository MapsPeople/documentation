# Custom Floor Selector

## How to implement a custom floor selector

To implement a custom floor selector we expect you to already have a View with a Map using MapControl. [Show a map](https://docs.mapsindoors.com/getting-started/android/v4/map#show-a-map-with-mapsindoors)

Start by creating a class, in this example named; `CustomFloorSelector`. Extend the class with `FrameLayout` and implement `MPFloorSelectorInterface` as well as it's methods.

```kotlin
    class CustomFloorSelector(private val context: Context, attrs: AttributeSet): MPFloorSelectorInterface, FrameLayout(context, attrs)  {
        override fun getView(): View? {
            TODO("Not yet implemented")
        }

        override fun setOnFloorSelectionChangedListener(listener: OnFloorSelectionChangedListener?) {
            TODO("Not yet implemented")
        }

        override fun setList(floors: MutableList<MPFloor>?) {
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

        override fun isAutoFloorChangeEnabled(): Boolean {
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

We can now start implementing logic into our `CustomFloorSelector` class. We will start by implementing the methods from the `MPFloorSelectorInterface`. We start by adding a `HashMap<Int, MPFloor>` to the class to keep track of the current floors. We populate the list when `setList` is called. We add a `OnFloorSelectionChangedListener`, so that we can notify when floors have changed in the UI. For the `show` we will ignore the animated bool, to keep it simple and we just toggle the visibility for this example. We will ignore the zoomLevelChanged method in this example, but it allows you to implement logic in the custom floor selector based on the current zoom level. For this example we will allow AutoFloorChanges, the `setUserPositionFloor` can be used to keep track of the current floor of the user, if using any type of indoor positioning service, together with MapsIndoors. For this example, we will save the user location, but will not add any logic based on it.

```kotlin
class CustomFloorSelector(private val context: Context, attrs: AttributeSet): MPFloorSelectorInterface, FrameLayout(context, attrs)  {
    private var floorMap: HashMap<Int, MPFloor> = HashMap()
    private var mOnFloorSelectionChangedListener: OnFloorSelectionChangedListener? = null

    ...
    override fun setList(floors: MutableList<MPFloor>?) {
        floorMap.clear()
        floors?.forEach {
            floorMap[it.floorIndex] = it
        }
    }

    override fun setOnFloorSelectionChangedListener(listener: OnFloorSelectionChangedListener?) {
        mOnFloorSelectionChangedListener = listener
    }

    override fun show(show: Boolean, animated: Boolean) {
        visibility = if (show) View.VISIBLE else View.GONE
    }

    override fun zoomLevelChanged(newZoomLevel: Float) {
        //ignore
    }

    override fun isAutoFloorChangeEnabled(): Boolean {
        return true
    }

    override fun setUserPositionFloor(zIndex: Int) {
        mUserFloorIndex = zIndex
    }
    ...
}
```

For the remaining methods we need to modify the UI, so we will start implementing the UI first. Inside the `init` we will inflate the view and store and hold a reference to it, to return inside the `getView`method. We will also create a reference to the `Button` that we implemented. The `floorBtn` needs to open a dropdown when clicked we will do this with a `PopupMenu` that is ordered by the floor index. When a `MenuItem` is clicked we use the itemId to retrieve the correct floor from the floor map. When that is retrieved we update the `floorBtn` text, for the now selected floor, as well as calling `onFloorSelectionChanged` with the selected floor. For the map to update and reflect the action.

```kotlin
class CustomFloorSelector(private val context: Context, attrs: AttributeSet): MPFloorSelectorInterface, FrameLayout(context, attrs)  {
    ...
    
    private var floorBtn: Button? = null
    private var mView: View? = null
    private var mSelectedFloor: MPFloor? = null
    ...

    init {
        mView = inflate(context, R.layout.custom_floor_selector, this)
        floorBtn = findViewById(R.id.custom_floor_selector_btn)

        floorBtn?.setOnClickListener { it ->
            val popup = PopupMenu(context, it)
            floorMap.values.forEach {
                popup.menu.add(0,it.floorIndex, it.floorIndex, it.displayName)
            }
            popup.show()
            popup.setOnMenuItemClickListener { menuItem ->
                val floor = floorMap[menuItem.itemId]
                floor?.let {
                    floorBtn?.text = it.displayName
                    mOnFloorSelectionChangedListener?.onFloorSelectionChanged(it)
                }
                popup.dismiss()
                return@setOnMenuItemClickListener true
            }
        }
    }

    override fun getView(): View? {
        return mView
    }

    override fun setSelectedFloor(floor: MPFloor) {
        mSelectedFloor = floor
        floorBtn?.text = floor.displayName
    }

    override fun setSelectedFloorByZIndex(zIndex: Int) {
        mSelectedFloor = floorMap[zIndex]
        floorBtn?.text = floorMap[zIndex]?.displayName
    }
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

```kotlin
fun initMapControl() {
    floorSelector = findViewById(R.id.custom_floor_selector)
    val mapConfig = MPMapConfig.Builder(
            requireActivity(),
            mMap,
            mMapView,
            getString(R.string.mapbox_api_key),
            true).setFloorSelector(floorSelector).build()
    MapControl.create(mapConfig) { mapControl: MapControl?, miError: MIError? ->
    }
}
```

You have now succesfully implemented a custom floor selector into your own app.
