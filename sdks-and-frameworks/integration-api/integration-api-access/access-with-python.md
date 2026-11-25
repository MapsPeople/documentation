# Access with Python

## Using the Integration API via Python​ <a href="#using-the-integration-api-via-python" id="using-the-integration-api-via-python"></a>

The Integration API can be access from python for automation of tasks such as updating data. In the example below we:

* Login using OAuth2 to the Mapspeople autorization server, with the script's own client ID and client secret
* Define a GeoData object we want to change
* Send the updated GeoData to the integration API using the access token

> **Note** For this example to work you'll need your own client ID and client secret in the environment variables `MAPSINDOORS_CLIENT_ID` and `MAPSINDOORS_CLIENT_SECRET`

#### Login

The script logs in with the client credentials flow: it sends its client ID and client secret to the authorization server. The payload is formatted like this:

```
grant_type=client_credentials&client_id=your_client&client_secret=your_secret
```

To get a client ID and create a client secret, read:

{% content-ref url="client-credentials-flow.md" %}
[client-credentials-flow.md](client-credentials-flow.md)
{% endcontent-ref %}

{% hint style="warning" %}
**Older method, being retired: username and password.** Scripts that send a MapsIndoors username and password (`grant_type=password` with `client_id=client`) still work for now, but this method is being retired. Use the client ID and client secret as shown here.
{% endhint %}

### Example script

The code below is meant as an example, not final production code. It reads the client secret from an environment variable, so the secret never sits in the code. Also do note that the example here tries to change data in the read-only demo solution **which will fail** - it's just an example. Change the solution ID and json content to something in your solution and it will work though.

With this in mind, here is the example:

```python
import http.client
import json
import os
import urllib.parse

# First create a connection to the Mapsindoors Authorization server and get an access token using the client
# credentials flow: the script's own client ID and client secret, read from environment variables
#
conn = http.client.HTTPSConnection("auth.mapsindoors.com")
payload = urllib.parse.urlencode({
    'grant_type': 'client_credentials',
    'client_id': os.environ['MAPSINDOORS_CLIENT_ID'],
    'client_secret': os.environ['MAPSINDOORS_CLIENT_SECRET'],
})
headers = {'Content-Type': 'application/x-www-form-urlencoded'}

conn.request("POST", "/connect/token", payload, headers)
res = conn.getresponse()
response = res.read().decode("utf-8")

# Got a response back from Mapspeople. Check if everything is ok
#
if res.status == 200:
    access_token = json.loads(response)["access_token"]
else:
    print(f"Authorization failed. Response: {response}")
    quit()

print("Got an access token from Mapspeople")

# Let's create an updated geojson as payload to a PUT request
#
payload = json.dumps([
    {
        "id": "c88c996b231746d096db5eaa",
        "parentId": "34c588fecbee45f199e8d67c",
        "datasetId": "550c26a864617400a40f0000",
        "baseType": "poi",
        "displayTypeId": "52c120dfae7e47269e930b80",
        "displaySetting": {
            "name": "default"
        },
        "geometry": {
            "coordinates": [
                9.95688874041662,
                57.0859076569953
            ],
            "type": "Point"
        },
        "anchor": {
            "coordinates": [
                9.95688874041662,
                57.0859076569953
            ],
            "type": "Point"
        },
        "aliases": [],
        "categories": [
            "5823246d07215b23a02e3cd9"
        ],
        "status": 3,
        "baseTypeProperties": {
            "administrativeid": "311a5837-395f-4e2b-9b88-c21b1b5c7833",
            "activefrom": "2015-01-16T07:09:00Z",
            "capacity": "0"
        },
        "properties": {
            "name@en": "Printing facilities1",
            "name@da": "Print new"
        }
    }
])

# Added our access token as Authorization to the server. Note: This needs 'bearer' in front of it.
#
headers = {
    'Content-Type': 'application/json',
    'Authorization': f'bearer {access_token}'
}

print("Attempting to change a geodata object")
conn.request("PUT", "https://integration.mapsindoors.com/550c26a864617400a40f0000/api/geodata", payload, headers)

res = conn.getresponse()
response = res.read().decode("utf-8")

if res.status != 204:
    print(f"Update failed. Response: {response}")
    quit()
else:
    print("Success. One geodata object was updated.")

```
