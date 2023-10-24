---
description: >-
  It is highly recommended first to read the guide on Booking to understand how
  booking functions in the MapsIndoors SDK.
---

# User Authenticated Booking

{% tabs %}
{% tab title="Java" %}


### User Authenticated Bookings in Java[​](https://docs.mapsindoors.com/user-authenticated-booking#user-authenticated-bookings-in-java) <a href="#user-authenticated-bookings-in-java" id="user-authenticated-bookings-in-java"></a>

By default, the `MPBookingService` performs anonymous bookings using a service account known to MapsIndoors. However, it is also possible to list, perform and cancel Bookings on behalf of a user. For the `MPBookingService` to work on behalf of a user, it must identify the tenant with a given tenant id (optional for single tenant setups) and prove user access with an access token. See the following example.

```java
MPBookingService mBookingService = MPBookingService.getInstance();

public void setAuthentication(String userToken, String tenantId) {
    MPBookingAuthConfig authConfig = new MPBookingAuthConfig.Builder(userToken).setProviderTenantId(tenantId).build();

    mBookingService.setAuthConfig(authConfig);
}
```

When the above configuration is in place, all following operations through the `MPBookingService` will be performed on behalf of a user. If the access token expires, the different Booking methods will result in errors and a new token must be obtained.

#### Obtaining a Tenant ID for User Bookings in Java[​](https://docs.mapsindoors.com/user-authenticated-booking#obtaining-a-tenant-id-for-user-bookings-in-java) <a href="#obtaining-a-tenant-id-for-user-bookings-in-java" id="obtaining-a-tenant-id-for-user-bookings-in-java"></a>

The tenant id is specific for each tenant / booking provider. If you don't know your tenant id, your IT administrator should be able to provide the information needed. Note that this is optional for single tenant setups and single tenant setups are the most common.

#### Obtaining an Access Token for User Bookings in Java[​](https://docs.mapsindoors.com/user-authenticated-booking#obtaining-an-access-token-for-user-bookings-in-java) <a href="#obtaining-an-access-token-for-user-bookings-in-java" id="obtaining-an-access-token-for-user-bookings-in-java"></a>

Obtaining an access token for working with Bookings on behalf of a user is outside of the scope of this guide. Usually an access token is obtained in a login flow in your own application.

> Note that the access token obtained from a MapsIndoors Single Sign-on flow cannot be used as access token for the `MPBookingService`. Single Sign-on access tokens are issued by MapsIndoors and not the underlying tenant. You need to login directly on your Booking tenant to get an access token that can be used for working with the Booking Service as an authenticated user.

### Disabling User Authenticated Bookings in Java[​](https://docs.mapsindoors.com/user-authenticated-booking#disabling-user-authenticated-bookings-in-java) <a href="#disabling-user-authenticated-bookings-in-java" id="disabling-user-authenticated-bookings-in-java"></a>

It is easy to disable the authentication, simply `null` the `AuthConfig` on the `MPBookingService`.

```java
MPBookingService bookingService = MPBookingService.getInstance();

bookingService.setAuthConfig(null);
```
{% endtab %}

{% tab title="Kotlin" %}


### User Authenticated Bookings in Kotlin[​](https://docs.mapsindoors.com/user-authenticated-booking#user-authenticated-bookings-in-kotlin) <a href="#user-authenticated-bookings-in-kotlin" id="user-authenticated-bookings-in-kotlin"></a>

By default, the `MPBookingService` performs anonymous bookings using a service account known to MapsIndoors. However, it is also possible to list, perform and cancel Bookings on behalf of a user. For the `MPBookingService` to work on behalf of a user, it must identify the tenant with a given tenant id (optional for single tenant setups) and prove user access with an access token. See the following example.

```kotlin
private val mBookingService: MPBookingService = MPBookingService.getInstance()

fun setAuthentication(accessToken: String, tenantId: String) {
    val authConfig = MPBookingAuthConfig.Builder(accessToken).setProviderTenantId(tenantId).build()

    mBookingService.setAuthConfig(authConfig)
}
```

When the above configuration is in place, all following operations through the `MPBookingService` will be performed on behalf of a user. If the access token expires, the different Booking methods will result in errors and a new token must be obtained.

#### Obtaining a Tenant ID for User Bookings in Kotlin[​](https://docs.mapsindoors.com/user-authenticated-booking#obtaining-a-tenant-id-for-user-bookings-in-kotlin) <a href="#obtaining-a-tenant-id-for-user-bookings-in-kotlin" id="obtaining-a-tenant-id-for-user-bookings-in-kotlin"></a>

The tenant id is specific for each tenant / booking provider. If you don't know your tenant id, your IT administrator should be able to provide the information needed. Note that this is optional for single tenant setups and single tenant setups are the most common.

#### Obtaining an Access Token for User Bookings in Kotlin[​](https://docs.mapsindoors.com/user-authenticated-booking#obtaining-an-access-token-for-user-bookings-in-kotlin) <a href="#obtaining-an-access-token-for-user-bookings-in-kotlin" id="obtaining-an-access-token-for-user-bookings-in-kotlin"></a>

Obtaining an access token for working with Bookings on behalf of a user is outside of the scope of this guide. Usually an access token is obtained in a login flow in your own application.

> Note that the access token obtained from a MapsIndoors Single Sign-on flow cannot be used as access token for the `MPBookingService`. Single Sign-on access tokens are issued by MapsIndoors and not the underlying tenant. You need to login directly on your Booking tenant to get an access token that can be used for working with the Booking Service as an authenticated user.

### Disabling User Authenticated Bookings in Kotlin[​](https://docs.mapsindoors.com/user-authenticated-booking#disabling-user-authenticated-bookings-in-kotlin) <a href="#disabling-user-authenticated-bookings-in-kotlin" id="disabling-user-authenticated-bookings-in-kotlin"></a>

It is easy to disable the authentication, simply `null` the `AuthConfig` on the `MPBookingService`.

```kotlin
val bookingService = MPBookingService.getInstance()

bookingService.setAuthConfig(null)
```
{% endtab %}
{% endtabs %}
