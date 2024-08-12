---
description: Work with MapsIndoors SDK Behind a Firewall
---

# MapsIndoors SDK Firewall

If you need to work with MapsIndoors SDK from behind a firewall, you might need to allowlist the following IP addresses:

| Service                 | URL                          | IP                  | Port | Regions                                             | Other                                        |
|-------------------------|------------------------------|---------------------|------|-----------------------------------------------------|----------------------------------------------|
| Auth API/Login          | auth.mapsindoors.com         | 34.147.83.241       | 443  | Europe                                              |                                              |
| Booking API             | booking.mapsindoors.com      | 34.147.83.241       | 443  | Europe                                              |                                              |
| Integration API         | integration.mapsindoors.com  | 34.147.83.241       | 443  | Europe                                              |                                              |
| Live data API           | liveapi.mapsindoors.com      | 34.147.83.241       | 443  | Europe                                              |                                              |
| Manager API             | v2.mapsindoors.com           | 34.147.83.241       | 443  | Europe                                              |                                              |
| Livedata MQ Websocket   | livedata-mq.mapsindoors.com  | 34.147.83.241       | 8084 | Europe                                              |                                              |
| Map Template            | map.mapsindoors.com          | 35.190.36.244       | 443  | Europe                                              |                                              |
| Client API              | api.mapsindoors.com          | 35.190.6.186        | 443  | US Central, US East, EU West, Asia Southeast, Australia Southeast |                              |
| Assets                  | app.mapsindoors.com          | CDN non static IP   | 443  | North America, Europe, Asia, Middle East, and Africa | ip-ranges.amazonaws.com/ip-ranges.json       |
| CMS                     | cms.mapsindoors.com          | CDN non static IP   | 443  | North America, Europe, Asia, Middle East, and Africa | ip-ranges.amazonaws.com/ip-ranges.json       |
| Media API               | media.mapsindoors.com        | 34.104.35.183       | 443  | Europe                                               |        |
| Tiles CDN               | tiles.mapsindoors.com        | 34.126.226.211      | 443  | Europe                                               |        |
Additionally, to work with Mapbox or Google Maps from behind a firewall, you might need to allow the addresses listed in the following documents:

* [https://docs.mapbox.com/help/troubleshooting/firewalls/](https://docs.mapbox.com/help/troubleshooting/firewalls/)
* [https://developers.google.com/maps/domains](https://developers.google.com/maps/domains)
