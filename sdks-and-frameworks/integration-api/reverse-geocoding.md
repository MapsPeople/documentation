# Reverse Geocoding

#### Interface Description[​](https://docs.mapsindoors.com/api-reverse-geocoding#interface-description) <a href="#interface-description" id="interface-description"></a>

```
HTTP Get
Path: /{apiKey}/api/geocode
Returns: A list of Geodata objects
```

Decription: Given a latitude/longitude point on the map and a floor index, this endpoint will return a list of all geodata that intersects with this point.

Note:

* Venue and Building geodata will disregard the floor index and will be given based on the latitude/logitude alone.
* Floor and Room geodata will respect the floor index and will return if the latitude/logitude intersects AND the given floorindex matches
* If no matches where found, an empty list will be returned
* Floor indexes can be viewed in the CMS under the Building's detail page.
* For a geodata object of type `"floor"` its `baseTypeProperties.administrativeid` property contains the floor index.

Mandatory parameters:

* **lat** Latitude of the point to examine. Valid range: +/- 90
* **lng** Longitude of the point to examine. Valid range: +/- 180
* **floor** Floor index to match for floor and room geodata

Example:

Input values:

* **lat** 57.0579386
* **lng** 9.9502304
* **floor** 10

Output: A list of 4 geodata objects: a Venue, a Building, a Floor and a Room:

```
[
  {
    "id": "586ca9f1bc1f5702406442b6",
    "datasetId": "57e4e4992e74800ef8b69718",
    "baseType": "venue",
    "geometry": ...,
    "anchor": {
      "coordinates": [
        9.95033207518389,
        57.0589850525
      ],
      "type": "Point"
    },
    "aliases": [],
    "status": 3,
    "baseTypeProperties": {
      "defaultfloor": "0",
      "administrativeid": "Stigsborgvej",
      "graphid": "STIGSBORGVEJ_Graph"
    },
    "properties": {
      "name@en": "Aalborg Office",
      "name@da": "Aalborg Kontor"
    },
    "tileStyles": [
      {
        "displayName": "default",
        "style": "default"
      }
    ]
  },
  {
    "id": "586caf3dbc1f5702406442b9",
    "parentId": "586ca9f1bc1f5702406442b6",
    "datasetId": "57e4e4992e74800ef8b69718",
    "baseType": "building",
    "geometry": ...,
    "anchor": {
      "coordinates": [
        9.95071928922423,
        57.0590494749439
      ],
      "type": "Point"
    },
    "status": 3,
    "baseTypeProperties": {
      "administrativeid": "Stigsborgvej"
    },
    "properties": {
      "name@en": "Stigsborgvej",
      "name@da": "Stigsborgvej"
    }
  },
  {
    "id": "fadb5dbf31b442d1a5d6bb08",
    "parentId": "586caf3dbc1f5702406442b9",
    "datasetId": "57e4e4992e74800ef8b69718",
    "baseType": "floor",
    "geometry": ...,
    "status": 3,
    "baseTypeProperties": {
      "name": "1",
      "administrativeid": "10"
    },
    "properties": {}
  },
  {
    "id": "bf4aac447b1148e198f48d7d",
    "parentId": "fadb5dbf31b442d1a5d6bb08",
    "datasetId": "57e4e4992e74800ef8b69718",
    "externalId": "1.05.01",
    "baseType": "room",
    "displayTypeId": "Storage",
    "displaySetting": {
      "name": "default"
    },
    "geometry": ...,
    "anchor": {
      "coordinates": [
        9.9502304,
        57.0579386
      ],
      "type": "Point"
    },
    "aliases": [],
    "categories": [],
    "status": 3,
    "baseTypeProperties": {
      "administrativeid": "2120ABC7-A574-4950-A33B-E5F836EA91CF",
      "class": "Lager"
    },
    "properties": {
      "name@en": "IT storage",
      "name@da": "IT lager"
    }
  }
]
```
