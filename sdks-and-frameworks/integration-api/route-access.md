# Route Access

#### Route Elements[​](https://docs.mapsindoors.com/api-route-access#route-elements) <a href="#route-elements" id="route-elements"></a>

A number of things can affect which routes the end users are given. Doors can be locked or there can we wait times along the way. This meta-data is contained in "route elements". A route element often represents a physical element in the real world that affects the routes such as doors and stairs.

The route elements defined can be seen in the CMS in Map -> Route Access

<figure><img src="../../.gitbook/assets/CleanShot 2023-08-31 at 08.31.59@2x.png" alt=""><figcaption></figcaption></figure>

Using the endpoint at /{apiKey}/api/routing/routeelements you can find and change the route elements in your solution.

At the moment 3 things can be modified:

* WaitTime (in seconds)
* OnewayDirection (Optional)
* Restrictions (optional)

**WaitTime**[**​**](https://docs.mapsindoors.com/api-route-access#waittime)

Wait time is set in seconds and affects how long it takes to walk past this point. This is given as a natural number and thus may not be negative (but may be 0 if no extra wait time should occur at this point).

**OnewayDirection**[**​**](https://docs.mapsindoors.com/api-route-access#onewaydirection)

Oneway direction is an [absolute\_bearing](https://en.wikipedia.org/wiki/Absolute\_bearing) and can be set to any number between 0 and 360 (degrees). Oneway is not mandatory, but can be used in situations where users can only walk in one direction. IF set - end users may only walk in the direction stated within the area (+/- 90 degrees)

**Restrictions**[**​**](https://docs.mapsindoors.com/api-route-access#restrictions)

With this setting you can restrict if users are allowed to go though this point. Restriction is not mandatory, but IF used it can used in two ways:

1\) It can be set to "locked" which implies that it's not possible to go though this point for anyone 2) It can be set to one or more App User Roles (refered by ID). App User Roles can be defined via the CMS in App Settings -> App Configuration:

<figure><img src="../../.gitbook/assets/CleanShot 2023-08-31 at 08.23.41@2x (1).png" alt=""><figcaption></figcaption></figure>

Notice that you can get a list of available user roles using this endpoint from the intregrationAPI: /{apiKey}/api/appUserRoles

An example of a route element from the demo dataset looks like this:

```
  {
    "id": "79edd6bb64724381bbf43923",
    "datasetId": "550c26a864617400a40f0000",
    "geometry": {
      "coordinates": [
        9.9577215,
        57.0858134
      ],
      "type": "Point"
    },
    "restrictions": [
      "locked"
    ],
    "waitTime": 0
  }
```
