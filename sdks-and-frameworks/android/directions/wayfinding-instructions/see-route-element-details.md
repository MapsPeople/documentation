---
description: Android v4
---

# See Route Element Details

In this tutorial we will request a route and list the route parts. A MapsIndoors route is made of one or more legs, each containing one or more steps.

Start by creating a `BaseAdapter` implementation with a `MPRoute` property, and include all the required functions. The adapter also takes a `CoroutineScope`, such as the `lifecycleScope` of the activity showing the list, which it uses to update the list on the main thread.

```kotlin
class RouteListAdapter(private val context: Context, private val scope: CoroutineScope) : BaseAdapter() {
    private var route: MPRoute? = null

    override fun getCount(): Int {
        TODO("Not yet implemented")
    }

    override fun getItem(position: Int): Any {
        TODO("Not yet implemented")
    }

    override fun getItemId(position: Int): Long {
        TODO("Not yet implemented")
    }

    override fun getView(position: Int, convertView: View?, parent: ViewGroup?): View {
        TODO("Not yet implemented")
    }
}
```

Inside the `init` section, setup a directions service, call the directions service and save the route result to your `route` property

```kotlin
init {
    val origin = MPPoint(57.057917, 9.950361, 0.0)
    val destination = MPPoint(57.058038, 9.950509, 1.0)
    val service = MPDirectionsService()
    service.setRouteResultListener { result, error ->
        error?.let { Log.e("RouteListAdapter", it.toString()) }
        val route = result?.routes?.firstOrNull() ?: return@setRouteResultListener
        // The result can arrive on a background thread, so update the list from the main thread
        scope.launch {
            this@RouteListAdapter.route = route
            notifyDataSetChanged()
        }
    }
    service.query(origin, destination)
}
```

Override the `getCount` function to return the number of steps

```kotlin
override fun getCount(): Int = route?.collapsedSteps?.size ?: 0
```

Override the `getView` function to create your views that should be displayed in the list

```kotlin
override fun getView(position: Int, convertView: View?, parent: ViewGroup?): View {
    // Inflate your view, in this example we will just use a TextView
    val view = TextView(context)

    view.text = route?.let {
        "step #$position: ${it.collapsedSteps[position].htmlInstructions}"
    } ?: "No Route"

    return view
}
```

Override `getItem` and `getItemId` to let click events work correctly

```kotlin
override fun getItem(position: Int): MPRouteStep =
    route?.collapsedSteps?.getOrNull(position) ?: error("Cannot select step that does not exist")

override fun getItemId(position: Int): Long = 0L
```

Now you can add the adapter to your `ListView` and show the route elements

```kotlin
val routeAdapter = RouteListAdapter(this, lifecycleScope)
listView.adapter = routeAdapter
```
