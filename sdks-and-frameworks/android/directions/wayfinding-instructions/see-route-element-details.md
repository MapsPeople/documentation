---
description: Android v4
---

# See Route Element Details

In this tutorial we will request a route and list the route parts. A MapsIndoors route is made of one or more legs, each containing one or more steps.

Start by creating a `BaseAdapter` implementation with a `MPRoute` property, and include all the required functions.

```kotlin
class RouteListAdapter(private val context: Context) : BaseAdapter() {
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
    service.setRouteResultListener { route, error ->
        route?.let {
            this.route = it
            notifyDataSetChanged()
        }
        error?.let {
            Log.e("RouteListAdapter", it.toString())
        }
    }
    service.query(origin, destination)
}
```

Override the `getCount` function to return the number of steps

```kotlin
override fun getCount(): Int {
    return route?.collapsedSteps?.size ?: 0
}
```

Override the `getView` function to create your views that should be displayed in the list

```kotlin
override fun getView(position: Int, convertView: View?, parent: ViewGroup?): View {

    //inflate you view, in this example we will just use a textView
    val view = TextView(context)

    view.text = route?.let {
        "step #$position: ${route?.collapsedSteps?.get(position)?.htmlInstructions}"
    } ?: "No Route"

    return view
}
```

Override `getItem` and `getItemId` to let click events work corretly

```kotlin
override fun getItem(position: Int): MPRouteStep {
    return route?.collapsedSteps?.get(position) ?: throw IllegalStateException("Cannot select step that does not exist")
}

override fun getItemId(position: Int): Long {
    return 0L
}
```

Now you can add the adapter to your `ListView` and show the route elements

```kotlin
val routeAdapter = RouteListAdapter()
listview.adapter = routeAdapter
```
