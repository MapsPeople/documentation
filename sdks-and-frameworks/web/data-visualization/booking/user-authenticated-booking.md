# User Authenticated Booking

<mark style="background-color:red;">Missing intro about User Authenticated Booking as a concept</mark>

Before commencing on user authenticated booking, we recommend reading the guide on Booking first to understand how booking functions in the MapsIndoors SDK.

### User Authenticated Bookings[​](https://docs.mapsindoors.com/user-authenticated-booking#user-authenticated-bookings-for-web) <a href="#user-authenticated-bookings-for-web" id="user-authenticated-bookings-for-web"></a>

By default, the `BookingService` performs anonymous bookings using a service account known to MapsIndoors. However, it is also possible to list, perform and cancel bookings on behalf of a user. For the `BookingService` to work on behalf of a user, it must identify the tenant with a given tenant id (optional for single tenant setups) and prove user access with an access token. See the following example:

```
  const bookingService = mapsindoors.services.BookingService;
  const bookingAuthenticationConfig = new bookingService.AuthenticationConfig('some-user-access-token', `some-tenant-id`);
  bookingService.setAuthenticationConfig(bookingAuthenticationConfig);
}
```

When the configuration above is in place, all following operations through the `BookingService` will be performed on behalf of a user. If the access token expires, the different booking methods will result in errors and a new token must be obtained.

#### Obtaining a Tenant ID for User Bookings[​](https://docs.mapsindoors.com/user-authenticated-booking#obtaining-a-tenant-id-for-user-bookings-for-web) <a href="#obtaining-a-tenant-id-for-user-bookings-for-web" id="obtaining-a-tenant-id-for-user-bookings-for-web"></a>

The tenant id is specific for each tenant / booking provider. If you don't know your tenant id, your IT administrator should be able to provide the information needed. Note that this is optional for single tenant setups and single tenant setups are the most common. <mark style="background-color:red;">Ville det give mening at forklare "single tenant setups" lidt? Med et eksempel?</mark>

#### Obtaining an Access Token for User Bookings [​](https://docs.mapsindoors.com/user-authenticated-booking#obtaining-an-access-token-for-user-bookings-for-web) <a href="#obtaining-an-access-token-for-user-bookings-for-web" id="obtaining-an-access-token-for-user-bookings-for-web"></a>

Obtaining an access token for working with bookings on behalf of a user is usually obtained in a login flow in your own application.

> Note that the access token obtained from a MapsIndoors Single Sign-on flow cannot be used as access token for the `BookingService`. Single Sign-on access tokens are issued by MapsIndoors and not the underlying tenant. You need to login directly on your Booking tenant to get an access token that can be used for working with the Booking Service as an authenticated user.

#### Disabling User Authenticated Bookings[​](https://docs.mapsindoors.com/user-authenticated-booking#disabling-user-authenticated-bookings-for-web) <a href="#disabling-user-authenticated-bookings-for-web" id="disabling-user-authenticated-bookings-for-web"></a>

Disabling user authenticated bookings is as simple as calling the `setAuthenticationConfig` with `null` as the argument:

```
bookingService.setAuthenticationConfig(null);
}
```

After disabling user authenticated bookings, all following operations will be treated as anonymous booking operations.
