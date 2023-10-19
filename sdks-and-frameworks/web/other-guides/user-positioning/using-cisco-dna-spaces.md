# Using Cisco DNA Spaces

### MapsIndoors & CiscoDNA for Web[​](https://docs.mapsindoors.com/cisco-dna#mapsindoors--ciscodna-for-web) <a href="#mapsindoors--ciscodna-for-web" id="mapsindoors--ciscodna-for-web"></a>

MapsIndoors is a dynamic mapping platform from MapsPeople that can provide maps of your indoor and outdoor localities and helps you create search and navigation experiences for your local users. CiscoDNA is Cisco’s newest digital and cloud-based IT infrastructure management platform. Among many other things, CiscoDNA can pinpoint the physical and geographic position of devices connected wirelessly to the local IT network.

### User Positioning in MapsIndoors for Web[​](https://docs.mapsindoors.com/cisco-dna#user-positioning-in-mapsindoors-for-web) <a href="#user-positioning-in-mapsindoors-for-web" id="user-positioning-in-mapsindoors-for-web"></a>

In order to show a user's position on an indoor map with MapsIndoors, a Position Provider must be implemented. The MapsIndoors JavaScript SDK does not provide a default Position Provider but relies on 3rd party positioning software to create this experience. In an outdoor environment, this Position Provider can be a wrapper of the browser's native Geolocation API.

### Code Sample[​](https://docs.mapsindoors.com/cisco-dna#code-sample) <a href="#code-sample" id="code-sample"></a>

> Please note that the following code sample assumes that you have already succesfully implemented MapsIndoors into your application.

The JavaScript SDK doesn't have a built-in interface like the Android and iOS SDKs. However, by following these steps, you should be able to achieve the same functionality.

The first step is to create the class `CiscoPositioningService`, and the constructor for it.

```javascript
class CiscoPositioningService {
    /**
     * @param {string} args.clientIp - The local IP address of the device
     * @param {string} args.tenantId - The Cisco tenant id.
     * @param {number} [args.pollingInterval=1000] - The interval that the position will be polled from the backend.
     * @param {string} [args.region="eu"] - The Cisco app region.
     */
    constructor(args = {}) {
        if (!args.clientIp)
            throw new TypeError('Invalid argument: "clientIp"');

        if (!args.tenantId)
            throw new TypeError('Invalid argument: "tenantId"');

        this._pollingInterval = 1000;
        this._tenantId = args.tenantId;
        this._successCallbacks = new Map();
        this._errorCallbacks = new Map();
        this._deviceId = '';

        args.region = args.region || 'eu';

        this.pollingInterval = args.pollInterval;

        fetch(`https://ciscodna.mapsindoors.com/${this._tenantId}/api/ciscodna/devicelookup?clientIp=${args.clientIp}&region=${args.region}`)
            .then(this._errorHandler)
            .then(res => res.json())
            .then(({ deviceId }) => {
                this._deviceId = deviceId;
                this._startPolling();
            }).catch(err => {
                console.error(err.message);
            });
    }
```

Next step is to create `watchPosition` and `clearWatch`, to watch for the positioning updates the system recieves.

```javascript
    watchPosition(successCallback, errorCallback) {
        const watchId = Symbol();
        if (!(successCallback instanceof Function))
            throw new TypeError('Invalid argument: "successCallback"');

        if (errorCallback instanceof Function) {
            this._errorCallbacks.set(watchId, errorCallback);
        }

        this._successCallbacks.set(watchId, successCallback);

        if (!this._interval) {
            this._startPolling();
        }

        return watchId;
    }

    clearWatch(watchId) {
        this._successCallbacks.delete(watchId);
        this._errorCallbacks.delete(watchId);

        if (this._successCallbacks.size === 0) {
            this._stopPolling();
        }
    }

    getCurrentPosition(successCallback, errorCallback) {
        fetch(`https://ciscodna.mapsindoors.com/${this._tenantId}/api/ciscodna/${this._deviceId}`)
            .then(this._errorHandler)
            .then(res => res.json())
            .then(data => {
                this._successCallbacks.forEach(cb => cb.call(null, data));
            }).catch(err => {
                this._errorCallbacks.forEach(cb => cb.call(null, err));
            });
    }
```

The next step is to create some functions that manage how often the system retrieves an update, or polls, from the Cisco DNA setup.

```javascript
    set pollingInterval(value) {
        if (!isNaN(value) && this._pollingInterval !== value) {
            this._pollingInterval = value;
            this._stopPolling();
            this._startPolling();
        }
    }

    get pollingInterval() {
        return this._pollingInterval;
    }


    /**
     * @private
     */
    _startPolling() {
        if (!this._interval && this._deviceId > '' && this._successCallbacks.size > 0) {
            this._interval = window.setInterval(() => {
                this.getCurrentPosition(response => {
                    this._successCallbacks.forEach(callback => callback(response));
                },
                    error => {
                        this._errorCallbacks.forEach(callback => callback(err));
                    });

            }, this._pollingInterval);
        }
    }

    /**
     * @private
     */
    _stopPolling() {
        if (this._interval) {
            window.clearInterval(this._interval);
            this._interval = null;
        }
    }
```

Lastly, an error handler is implemented.

```javascript
    /**
     * @private
     */

    _errorHandler(response) {
        if (!response.ok) {
            const contentType = response.headers.get('content-type');
            if (contentType && contentType.indexOf('application/json') !== -1) {
                return response.json().then(({ message }) => {
                    throw new Error(message);
                });
            } else {
                let statusText;
                switch (response.status) {
                    case 400:
                        statusText = 'The client IP is invalid.';
                        break;
                    case 404:
                        statusText = 'Device not found.';
                        break;
                    case 403:
                        statusText = 'The TenantId supplied is not authorized to access the device at the location.'
                        break;
                    default:
                        statusText = 'Unknown error.';
                }

                throw new Error(statusText);
            }
        }

        return response;
    }
} // end class
```

Once the class is created, it can then be used, for example, in the following way - Keep in mind that you cannot fetch the client/device IP address from the browser, an option to get around this could be a seperate service that returns the IP address:

```javascript
const map = mapView.getMap();
let watchId;

mapsindoors.services.SolutionsService.getSolution('57e4e4992e74800ef8b69718').then(solution => {
    if (solution.positionProviderConfigs && solution.positionProviderConfigs.ciscodna) {
        const tenantId = solution.positionProviderConfigs.ciscodna.ciscoDnaSpaceTenantId;
        const region = solution.positionProviderConfigs.ciscodna.ciscoDnaSpaceTenantRegion || 'usa';
        const clientIp = '10.0.0.134';
        const cps = new CiscoPositioningService({ clientIp, tenantId, region });

        watchId = cps.watchPosition(function (data) {
            console.log(data);
            map.setCenter({ lat: data.latitude, lng: data.longitude });
        }, function (err) {
            console.log(err);
        })
    }
});

const floorSelector = document.createElement('div');
new mapsindoors.FloorSelector(floorSelector, mi);
map.controls[google.maps.ControlPosition.RIGHT_TOP].push(floorSelector);
```
