# Access with Python

#### Using the Integration API via Python[​](https://docs.mapsindoors.com/access-with-python#using-the-integration-api-via-python) <a href="#using-the-integration-api-via-python" id="using-the-integration-api-via-python"></a>

The Integration API can be access from python for automation of tasks such as updating data. In the example below we:

* Login using OAuth2 to the Mapspeople autorization server
* Define a GeoData object we want to change
* Send the updated GeoData to the integration API using the access token

> **Note** For this example to work you'll need to insert you own username and password in the second line of code

The code below is meant as an example, not final production code. Eg. storing username/password in code is not recommended; It's only done here to keep things short and easy to read. Also do note that the example here tries to change data in the read-only demo solution **which will fail** - it's just an example. Change the solution ID, username password and json content to something in your solution and it will work though.

With this in mind, here is the example:

```python
import http.client
import json

# First create a connection to the Mapsindoors Authorization server and get an access token using username/password
#
conn = http.client.HTTPSConnection("auth.mapsindoors.com")
payload = 'grant_type=password&client_id=client&username=example@example.com&password=your_password_here'
headers = { 'Content-Type': 'application/x-www-form-urlencoded' }

conn.request("POST", "/connect/token", payload, headers)
res = conn.getresponse()
response = res.read().decode("utf-8")

# Got a response back from Mapspeople. Check if everything is ok
#
if( res.status == 200):
  access_token = json.loads(response)["access_token"]
else :
  print ("Authorization failed. Response: " + str(response))
  quit()

print("Got an access token from Mapspeople")

#Lets create an updated geojson as payload to a PUT request
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
  'Authorization': 'bearer ' + access_token
}

print("Attempting to change a geodata object")
conn.request("PUT", "https://integration.mapsindoors.com/550c26a864617400a40f0000/api/geodata", payload, headers)

res = conn.getresponse()
response = res.read().decode("utf-8")

if( res.status != 204):
  print ("Update failed. Response: " + str(response))
  quit()
else:
  print ("Success. One geodata object was updated.")pyt
```
