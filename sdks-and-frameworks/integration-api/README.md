# Integration API

The MapsIndoors Integration API offers an alternative to changing your Mapsindoors data via the [CMS](https://cms.mapsindoors.com/). From this API you can get, add, change and delete either directly via third party tools like [Postman](https://www.postman.com/) or via the provided Swagger frontend.

You can access data through the Integration API using a range of endpoints. The endpoints are described in the Swagger interface definition: [https://integration.mapsindoors.com/doc](https://integration.mapsindoors.com/doc/index.html)

In Swagger, each `GET` method is pre-loaded with all mandatory fields needed to get a live example of data. Click the "_Try it out_" button in Swagger to see the example data.

> **NOTE:** ⚠️ Only HTTPS is supported. There is a rate limit of 10 requests per second per Solution.

You can access the Integration API in various ways, for more on accessing it see [the API Login section](https://docs.mapsindoors.com/api-login)

### Example Use Cases[​](https://docs.mapsindoors.com/api#example-use-cases) <a href="#example-use-cases" id="example-use-cases"></a>

* A conference hall might have a list of vendors that will be presenting on a given date. To ensure easy navigation back and forth between the map and the information page, you can create a lookup table, fetching the MapsIndoors External ID's with `GET /{apiKey}/api/geodata`, and the conference hall's own database. The Integration API would allow this functionality to be easily implemented.
* Some of our clients have used the Integration API to create their own CMS, for example, using `POST /{apiKey}/api/geodata` to create new desk locations in the Solution for their corporate offices.
* You can use `PUT /{apiKey}/api/displaytypes` to edit multiple Location Types at once. For example, if you want to have some Location Types only show up at certain times of day, or when other conditions are met.
* An airport might want the various routes to change based on estimated wait times in queues. This can be done by using the Integration API to connect to a live data feed of client's positioning in the airport, and using `GET /{apiKey}/api/routing/routeelements` and `PUT /{apiKey}/api/routing/routeelements`, setting a given Route Element to "Blocked" if too many people are there.

The last example about airport wait times will be expanded upon briefly, giving slighly more detailed explanations on how to achieve such an implementation.

* The API calls can read or update the backend content, whereas the SDK only reads it. Therefore, the act of making the API calls are not part of the app, but part of a seperate backend service outside of MapsIndoors.
* Use `GET /{apiKey}/api/routing/routeelements` to fetch the list of route elements used in your solution, and their information structure. This should return a JSON file like this, with appropriate values instead of `string` or `0`:

```json
[
  {
    "id": "string",
    "datasetId": "string",
    "externalId": "string",
    "geometry": {
      "type": 0
    },
    "restrictions": [
      "string"
    ],
    "onewayDirection": 0,
    "waitTime": 0
  }
]
```

* Use an integration with a third-party sensor system to detect the amount of people present throughout the airport, on the paths of the given routes fetched earlier.
* Use `PUT /{apiKey}/api/routing/routeelements` to update the information of the route elements, changing the `restrictions` parameter to `locked` if there are too many people on a given route. An example of this request body could be:

```json
[
  {
    "id": "string",
    "datasetId": "string",
    "externalId": "string",
    "geometry": {
      "type": 0
    },
    "restrictions": [
      "locked"
    ],
    "onewayDirection": 0,
    "waitTime": 0
  }
]
```

The other information needed, such as the ID's, can be found in the `GET` call made earlier.

* Next time the SDK fetches information, it will load some routes as `locked` or "Blocked". This will cause the route generation to avoid these specific paths, helping to alleviate the congestion.

#### Login and Credentials[​](https://docs.mapsindoors.com/api#login-and-credentials) <a href="#login-and-credentials" id="login-and-credentials"></a>

First, log in to the service to get an `access token` to access the data. To get an access token you will need to use the Mapsindoors Autorization API. We use an [OAuth2 get token approach](https://auth0.com/docs/api/authentication#get-token) for this.

The Auth API supports multiple ways to log in. The most common way is with your MapsIndoors username and password. If you need to sign in with other providers, please [contact support](https://mapspeople.com/support).

> **NOTE:** ⚠️ To obtain an access token do a POST call to: [https://auth.mapsindoors.com/connect/token](https://auth.mapsindoors.com/connect/token)

No matter what login method you use, you will always need to use the following content-type header when talking to the Auth API:

```http
Content-Type: application/x-www-form-urlencoded
```

**Log in with MapsIndoors Username/Password**[**​**](https://docs.mapsindoors.com/api#log-in-with-mapsindoors-usernamepassword)

To log in with your MapsIndoors login, send them with the `grant_type` set to `password`.

Use the following key/value set:

```http
grant_type: password
client_id: client
username: <your username>
password: <your password>
```

Replacing `<your username>` and `<your password>` with your own credentials, and leaving `grant_type` and `client_id` as stated above.

The body of the request must end up containing a query string like this: `grant_type=password&client_id=client&username=<your username>&password=<your password>`

An example on how to login using curl (replace username and password):

```bash
curl -H "Content-Type: application/x-www-form-urlencoded" -X POST https://auth.mapsindoors.com/connect/token -d "grant_type=password&client_id=client&username=example@example.com&password=youpassword"
```

**When You Are Authenticated**[**​**](https://docs.mapsindoors.com/api#when-you-are-authenticated)

If you sent valid credentials to the Auth API, you will receive a response like this:

```json
{
    "access_token": "eyJhbGciOiJ...vmERrovsg",
    "expires_in": 86400,
    "token_type": "Bearer",
    "scope": "integration"
}
```

You will need the value from the key `access_token` for all your requests to the Integration API by adding the `Authorization` header like this:

```http
authorization: Bearer eyJhbGciOiJ...vmERrovsg
```

> **NOTE:** ⚠️ The access token is valid for 24 hours. After that you will need to reauthenticate, following the same steps as explained above.

With the access token you can now make further calls to the Integration API such as making changes to your geodata. Here is an example on how to use the auth token for a Mapsindoors API call. This examples will call the Integration API and delete the geodata object with ID: "123456789012345678901234".

```bash
curl -H "Content-Type: application/json" -H "Accept: */*" -H "Authorization: Bearer eyJhbG... " -X DELETE https://integration.mapsindoors.com/550c26a864617400a40f0000/api/geodata -d "[\"123456789012345678901234\"]
```
