---
description: iOS v4
---

# User Authenticated Booking

It is highly recommended to first read the [guide on Booking](https://docs.mapsindoors.com/booking/) to get an an understanding of how booking functions in the MapsIndoors SDK.

### User-Authenticated Bookings for iOS[​](https://docs.mapsindoors.com/user-authenticated-booking#user-authenticated-bookings-for-ios) <a href="#user-authenticated-bookings-for-ios" id="user-authenticated-bookings-for-ios"></a>

By default, the `MPBookingService` performs anonymous bookings using a service account known to MapsIndoors. However, it is also possible to list, perform and cancel Bookings on behalf of a user. For the `MPBookingService` to work on behalf of a user, it must identify the tenant with a given tenant id (optional for single tenant setups) and prove user access with an access token. See the following example.

```swift
MPMapsIndoors.shared.authToken = "YOUR AUTH TOKEN"
MPMapsIndoors.shared.bookingService.authenticationConfig = MPBookingAuthConfig(accessToken: "YOUR AUTH TOKEN")
MPMapsIndoors.shared.bookingService.authenticationConfig?.tenantId = "YOUR TENANT ID"
```

When the above configuration is in place, all following operations through the `MPBookingService` will be performed on behalf of a user. If the access token expires, the different Booking methods will result in errors and a new token must be obtained.

#### Obtaining a Tenant ID for User Bookings for iOS[​](https://docs.mapsindoors.com/user-authenticated-booking#obtaining-a-tenant-id-for-user-bookings-for-ios) <a href="#obtaining-a-tenant-id-for-user-bookings-for-ios" id="obtaining-a-tenant-id-for-user-bookings-for-ios"></a>

The tenant id is specific for each tenant / Booking provider. If you don't know your tenant id, your IT administrator should be able to provide the information needed. Note that this is optional for single tenant setups and single tenant setups are the most common.

#### Obtaining an Access Token for User Bookings for iOS[​](https://docs.mapsindoors.com/user-authenticated-booking#obtaining-an-access-token-for-user-bookings-for-ios) <a href="#obtaining-an-access-token-for-user-bookings-for-ios" id="obtaining-an-access-token-for-user-bookings-for-ios"></a>

Obtaining an access token for working with Bookings on behalf of a user is outside of the scope of this guide. Usually an access token is obtained in a login flow in your own application.

> Note that the access token obtained from a [MapsIndoors Single Sign-on flow](https://docs.mapsindoors.com/sso/) cannot be used as access token for the `MPBookingService`. Single Sign-on access tokens are issued by MapsIndoors and not the underlying tenant. You need to login directly on your Booking tenant to get an access token that can be used for working with the Booking Service as an authenticated user.

#### Disabling User Authenticated Bookings for iOS[​](https://docs.mapsindoors.com/user-authenticated-booking#disabling-user-authenticated-bookings-for-ios) <a href="#disabling-user-authenticated-bookings-for-ios" id="disabling-user-authenticated-bookings-for-ios"></a>

Disabling User Authenticated Bookings is as simple as setting the `authenticationConfig` to `nil`:

```swift
MPMapsIndoors.shared.bookingService.authenticationConfig = nil
```

After disabling user authenticated Bookings, all following operations will be treated as anonymous Booking operations.
