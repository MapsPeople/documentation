# Getting Directions

Now we have a simple map with a floor selector where you can search for locations. When finishing this step you'll be able to create a directions between two points and change the transportation mode.

### Get Directions Between Two Locations[​](https://docs.mapsindoors.com/getting-started/android/v3/directions#get-directions-between-two-locations) <a href="#get-directions-between-two-locations" id="get-directions-between-two-locations"></a>

After having created our list of search results, we have a good starting point for creating directions between two Locations. Since our search only supports a single search, we will hardcode a Location's coordinate into our app, and use that as the basis for our Origin. Then we'll create a route, navigate to a view of the navigation details, and show a route on the map from the Origin to the Destination.

We have already created a point in the basic example, called `mUserLocation` to use as a starting point for directions on `MapsActivity`

* Java
* Kotlin

```
private Point mUserLocation = new Point(38.897389429704695, -77.03740973527613,0);
```

Now we will create a method that can generate a route for us with a Location (picked from the search list). Start by implementing `OnRouteResultListener` to your MapsActivity.

* Java
* Kotlin

```
public class MapsActivity extends FragmentActivity implements OnMapReadyCallback, OnRouteResultListener
```

Implement the `onRouteResult` method and create a method called `createRoute(MPLocation mpLocation)` on your `MapsActivity`.

Use this method to query the `MPRoutingProvider`, which generates a route between two coordinates. We will use this to query a route with our hardcoded `mUserLocation` and a point from a MPLocation.

To generate a route with the `MPLocation`, we start by creating an `onClickListener` on our search `ViewHolder` inside the `SearchItemAdapter`. In the method `onBindViewHolder` we will call our `createRoute` on the `MapsActivity` for our route to be generated.

* Java
* Kotlin

[SearchItemAdapter.java](https://github.com/MapsPeople/MapsIndoors-Getting-Started-Android/blob/master/app/src/main/java/com/example/mapsindoorsgettingstarted/SearchItemAdapter.java#L37-L40)

```
public void onBindViewHolder(ViewHolder holder, int position) {
...
holder.itemView.setOnClickListener(view -> {
        mMapActivity.createRoute(mLocations.get(position));
        //Clearing map to remove the location filter from our search result
        mMapActivity.getMapControl().clearMap();
    });
...
}
```

We start by implementing logic to our createRoute method to query a route through `MPRoutingProvider` and assign the onRouteResultListener to the activity. When we call the `createRoute` through our `onClickListener` we will receive a result through our `onRouteResult` implementation.

When we receive a result on our listener, we render the route through the `MPDirectionsRenderer`.

We create global variables of the `MPdirectionsRenderer` and `MPRoutingProvider` and create a getter to the `MPDirectionsRenderer` to access it from _fragments_ later on.

* Java
* Kotlin

[MapsActivity.java](https://github.com/MapsPeople/MapsIndoors-Getting-Started-Android/blob/master/app/src/main/java/com/example/mapsindoorsgettingstarted/MapsActivity.java#L218-L267)

```
void createRoute(MPLocation mpLocation) {
    if (mpRoutingProvider == null) {
        mpRoutingProvider = new MPRoutingProvider();
        mpRoutingProvider.setOnRouteResultListener(this);
    }
    mpRoutingProvider.query(mUserLocation, mpLocation.getPoint());
}

@Override
public void onRouteResult(@Nullable Route route, @Nullable MIError miError) {
    ...
    //Create the MPDirectionsRenderer if it has not been instantiated.
    if (mpDirectionsRenderer == null) {
        mpDirectionsRenderer = new MPDirectionsRenderer(this, mMap, mMapControl, i -> {
            //Listener call back for when the user changes route leg. (By default is only called when a user presses the RouteLegs end marker)
            mpDirectionsRenderer.setRouteLegIndex(i);
            mMapControl.selectFloor(mpDirectionsRenderer.getCurrentFloor());
        });
    }

    //Set the route on the Directions renderer
    mpDirectionsRenderer.setRoute(route);
    //Create a new instance of the navigation fragment
    mNavigationFragment = NavigationFragment.newInstance(route, this);
    //Add the fragment to the BottomSheet
    addFragmentToBottomSheet(mNavigationFragment);
    //As camera movement is involved run this on the UIThread
    runOnUiThread(()-> {
        //Starts drawing and adjusting the map according to the route
        mpDirectionsRenderer.initMap(true);
        ...
    });
}
```

See the full implementation of these methods here: [MapsActivity.java](https://github.com/MapsPeople/MapsIndoors-Getting-Started-Android/blob/master/app/src/main/java/com/example/mapsindoorsgettingstarted/MapsActivity.java#L218-L267) or [MapsActivity.kt](https://github.com/MapsPeople/MapsIndoors-Getting-Started-Android-Kotlin/blob/main/app/src/main/java/com/example/mapsindoorsgettingstartedkotlin/MapsActivity.kt#L185-L225)

Now we will implement logic to our `NavigationFragment` that we can put into our BottomSheet and show the steps for each route, as well as the time and distance it takes to travel the route.

Here we'll use a `viewpager` to allow the user to switch between each step, as well as display a "close" button so we are able to remove the route and the bottom sheet from the activity.

We will start by making a getter for our `MPDirectionsRenderer` that we store on `MapsActivity`:

* Java
* Kotlin

[MapsActivity.java](https://github.com/MapsIndoors/MapsIndoors-Getting-Started-Android/blob/master/app/src/main/java/com/example/mapsindoorsgettingstarted/MapsActivity.java#L127-133)

```
public MPDirectionsRenderer getMpDirectionsRenderer() {
    return mpDirectionsRenderer;
}
```

Inside the `NavigationFragment` we will implement logic to navigate through Legs of our Route.

* Java
* Kotlin

[NavigationFragment.java](https://github.com/MapsPeople/MapsIndoors-Getting-Started-Android/blob/master/app/src/main/java/com/example/mapsindoorsgettingstarted/NavigationFragment.java)

```
public class NavigationFragment extends Fragment {
    private Route mRoute;
    private MapsActivity mMapsActivity;
    ...
    @Override
    public void onViewCreated(@NonNull View view, @Nullable Bundle savedInstanceState) {
        RouteCollectionAdapter routeCollectionAdapter = new RouteCollectionAdapter(this);
        ViewPager2 mViewPager = view.findViewById(R.id.view_pager);
        mViewPager.setAdapter(routeCollectionAdapter);
        mViewPager.registerOnPageChangeCallback(new ViewPager2.OnPageChangeCallback() {
            @Override
            public void onPageSelected(int position) {
                super.onPageSelected(position);
                //When a page is selected call the renderer with the index
                mMapsActivity.getMpDirectionsRenderer().setRouteLegIndex(position);
                //Update the floor on mapcontrol if the floor might have changed for the routing
                mMapsActivity.getMapControl().selectFloor(mMapsActivity.getMpDirectionsRenderer().getCurrentFloor());
            }
        });

        ...
        //Button for closing the bottom sheet. Clears the route through directionsRenderer as well, and changes map padding.
        closeBtn.setOnClickListener(v -> {
            mMapsActivity.removeFragmentFromBottomSheet(this);
            mMapsActivity.getMpDirectionsRenderer().clear();
        });

        //Next button for going through the legs of the route.
        nextBtn.setOnClickListener(v -> {
            mViewPager.setCurrentItem(mViewPager.getCurrentItem() + 1, true);
        });

        //Back button for going through the legs of the route.
        backBtn.setOnClickListener(v -> {
            mViewPager.setCurrentItem(mViewPager.getCurrentItem() - 1, true);
        });

        //Describing the distance in meters
        distanceTxtView.setText("Distance: " + mRoute.getDistance() + " m");
        //Describing the time it takes for the route in minutes
        infoTxtView.setText("Time for route: " + TimeUnit.MINUTES.convert(mRoute.getDuration(), TimeUnit.SECONDS) + " minutes");
    }
    public class RouteCollectionAdapter extends FragmentStateAdapter {
        ...
    }
}
```

See the full implementation of `NavigationFragment` and the accompanying adapter here: [NavigationFragment.java](https://github.com/MapsPeople/MapsIndoors-Getting-Started-Android/blob/master/app/src/main/java/com/example/mapsindoorsgettingstarted/NavigationFragment.java#L31-L117) or [NavigationFragment.kt](https://github.com/MapsPeople/MapsIndoors-Getting-Started-Android-Kotlin/blob/main/app/src/main/java/com/example/mapsindoorsgettingstartedkotlin/NavigationFragment.kt#L18-L105)

We will then create a simple textview to describe each step of the Route Leg in the `RouteLegFragment` for the `ViewPager`:

* Java
* Kotlin

[RouteLegFragment.java](https://github.com/MapsPeople/MapsIndoors-Getting-Started-Android/blob/master/app/src/main/java/com/example/mapsindoorsgettingstarted/RouteLegFragment.java)

```
public class RouteLegFragment extends Fragment {
    private RouteLeg mRouteLeg;
    ...
    @Override
    public void onViewCreated(@NonNull View view, @Nullable Bundle savedInstanceState) {
        super.onViewCreated(view, savedInstanceState);
        //Assigning views
        TextView fromTxtView = view.findViewById(R.id.from_text_view);
        String stepsString = "";
        //A loop to write what to do for each step of the leg.
        for (int i = 0; i < mRouteLeg.getSteps().size(); i++) {
            RouteStep routeStep = mRouteLeg.getSteps().get(i);
            stepsString += "Step " + (i + 1) + routeStep.getManeuver() + "\n";
        }
        fromTxtView.setText(stepsString);
    }
}
```

See the full implementation of the fragment here: [RouteLegFragment.java](https://github.com/MapsPeople/MapsIndoors-Getting-Started-Android/blob/master/app/src/main/java/com/example/mapsindoorsgettingstarted/RouteLegFragment.java#L23-L57) or [RouteLegFragment.kt](https://github.com/MapsPeople/MapsIndoors-Getting-Started-Android-Kotlin/blob/main/app/src/main/java/com/example/mapsindoorsgettingstartedkotlin/RouteLegFragment.kt#L12-L44)

### Change Transportation Mode[​](https://docs.mapsindoors.com/getting-started/android/v3/directions#change-transportation-mode) <a href="#change-transportation-mode" id="change-transportation-mode"></a>

In MapsIndoors, the transportation mode is referred to as **travel mode**. There are four travel modes, **walking**, **bicycling**, **driving** and **transit** (public transportation). The travel modes generally applies for outdoor navigation. Indoor navigation calculations are based on **walking** travel mode.

To swap Travel Modes you set the Travel Mode before making a query for the route:

* Java
* Kotlin

```
void createRoute(MPLocation mpLocation) {
    //If MPRoutingProvider has not been instantiated create it here and assign the results call back to the activity.
    if (mpRoutingProvider == null) {
        mpRoutingProvider = new MPRoutingProvider();
        mpRoutingProvider.setOnRouteResultListener(this);
    }
    mpRoutingProvider.setTravelMode(TravelMode.WALKING);
    //Queries the MPRouting provider for a route with the hardcoded user location and the point from a location.
    mpRoutingProvider.query(mUserLocation, mpLocation.getPoint());
}
```

Expected result:

![An animation showing the desired behaviour of this tutorial](https://docs.mapsindoors.com/img/getting-started/android\_directions\_gif.gif)
