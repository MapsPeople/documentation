# Prerequisites

MapsIndoors is built on top of Google Maps and on this page, you'll create a Google Maps API key with the relevant APIs enabled, retrieve a MapsIndoors API key and install the necessary dependencies to get started building your own app.

### Get Your Google Maps API key[​](https://docs.mapsindoors.com/getting-started/android/v3/prerequisites#get-your-google-maps-api-key) <a href="#get-your-google-maps-api-key" id="get-your-google-maps-api-key"></a>

First, you need to [setup at a new project in the Google Cloud Console](https://developers.google.com/maps/gmp-get-started) (**Please note:** You are going to need a Google Billing Account for this step, so go ahead and [create one](https://cloud.google.com/billing/docs/how-to/manage-billing-account#create\_a\_new\_billing\_account) if you haven't already). When the project is created, the following APIs and the specific SDK you plan to use must be enabled from the [Maps API Library Page](https://console.cloud.google.com/apis/library?filter=category:maps).

* Google Maps Distance Matrix API
* Google Maps Directions API
* Google Places API Web Service
* Maps SDK for Android/iOS - if you're developing an app for Android/iOS respectively _**OR**_ Maps JavaScript API if you're developing a web application.

When the above 3 APIs and the relevant SDK are enabled, you can retrieve the API key from the [Credentials page](https://console.cloud.google.com/project/\_/apiui/credential). On the Credentials page, click _Create credentials_ > _API key_.

### Get Your MapsIndoors API key[​](https://docs.mapsindoors.com/getting-started/android/v3/prerequisites#get-your-mapsindoors-api-key) <a href="#get-your-mapsindoors-api-key" id="get-your-mapsindoors-api-key"></a>

If you are not a customer yet, you can use this demo MapsIndoors API key `d876ff0e60bb430b8fabb145` to follow this guide, or you can [contact MapsPeople](https://resources.mapspeople.com/contact-us) to get your building drawings processed and hosted by us to receive a unique API key. For the purpose of this guide, both methods will work.

### Work with MapsIndoors SDK behind a Firewall[​](https://docs.mapsindoors.com/getting-started/android/v3/prerequisites#work-with-mapsindoors-sdk-behind-a-firewall) <a href="#work-with-mapsindoors-sdk-behind-a-firewall" id="work-with-mapsindoors-sdk-behind-a-firewall"></a>

If you need to work with MapsIndoors SDK behind a firewall, you might need to [allowlist some IP-addresses](https://docs.mapsindoors.com/firewall/).
