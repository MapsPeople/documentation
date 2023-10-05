---
description: Web v4
---

# Booking

This guide covers the different aspects of _Booking_ in the MapsIndoors JavaScript SDK. The concept of Booking in MapsIndoors implies that specific Locations in your MapsIndoors dataset is treated as Bookable resources. Typical bookable resources could be meeting rooms and workspaces.

A MapsIndoors dataset can only have bookable resources if an integration with a Booking provider exists. Current examples of Booking providers are _Google Calendar_ and _Microsoft Office 365_. These providers and more can be added and integrated to your MapsIndoors project by request.

The central service in the SDK managing Bookings is the Booking Service, `mapsindoors.services.BookingService`.

```
  const bookingService = mapsindoors.services.BookingService;
```

The Booking Service can help with the following tasks:

* [List Bookable Locations](https://docs.mapsindoors.com/booking#bookable-locations)
* [Work with Bookings](https://docs.mapsindoors.com/booking#bookings)
  * [Listing Bookings for a Location](https://docs.mapsindoors.com/booking#listing-bookings-for-a-location)
  * [Performing a Booking of a Location](https://docs.mapsindoors.com/booking#performing-a-booking-of-a-location)
  * [Cancelling a Booking of a Location](https://docs.mapsindoors.com/booking#cancelling-a-booking-of-a-location)

> By default, the `BookingService` performs anonymous Bookings using a service account known to MapsIndoors. However, it is also possible to list, perform and cancel Bookings [on behalf of a user](https://docs.mapsindoors.com/booking#user-authenticated-bookings).

### Bookable Locations for Web[​](https://docs.mapsindoors.com/booking#bookable-locations-for-web) <a href="#bookable-locations-for-web" id="bookable-locations-for-web"></a>

To determine whether or not a `Location` is bookable can be looked up using the `bookingService.getBookableLocationsUsingQuery()` method. Below is an example of querying for bookable Locations:

```
  const bookingService = mapsindoors.services.BookingService;
  const start = new Date();
  const end = new Date(start).setHours(start.getHours() + 1);

  const locations = await bookingService.getBookableLocations({
      startTime: start,
      endTime: end
  });
```

The above example creates a query for Locations that are bookable for a timespan between now and 1 hour ahead.

To check if a specific `Locaiton` is bookable it possible to parse the `Location` object as a paramter to the `getBookingsUsingQuery` function.

```
  const bookingService = mapsindoors.services.BookingService;
  const myMeetingRoom = await locationsService.getLocation('0c44207987174561a53fb00a');

  const start = new Date();
  const end = new Date(start).setHours(start.getHours() + 1);

  const bookings = await bookingService.getBookingsUsingQuery({
      location: myMeetingRoom,
      startTime: start,
      endTime: end
  });
```

### Bookings for Web[​](https://docs.mapsindoors.com/booking#bookings-for-web) <a href="#bookings-for-web" id="bookings-for-web"></a>

A Booking is a timeboxed event model referring to the resource being booked and the users participating in the booked event.

#### Listing Bookings for a Location for Web[​](https://docs.mapsindoors.com/booking#listing-bookings-for-a-location-for-web) <a href="#listing-bookings-for-a-location-for-web" id="listing-bookings-for-a-location-for-web"></a>

Before trying to book a Location for a given time, it is convenient to know in advance whether or not the Location is already booked for the given time.

It is possible to get a list of bookings using the `bookingService.getBookableLocationsUsingQuery()` method.

```
  const bookingService = mapsindoors.services.BookingService;
  const myMeetingRoom = await locationsService.getLocation('0c44207987174561a53fb00a');
  const start = new Date().setHours(new Date().getHours() -1);
  const end = new Date(start).setHours(start.getHours() + 24);

  const bookings = await bookingService.getBookingsUsingQuery({
    location: myMeetingRoom,
    startTime: start,
    endTime: end
  });
}
```

The above example creates a query for bookings that exists for a location with timespan between 1 hour ago and 24 hours ahead.

#### Performing a Booking of a Location for Web[​](https://docs.mapsindoors.com/booking#performing-a-booking-of-a-location-for-web) <a href="#performing-a-booking-of-a-location-for-web" id="performing-a-booking-of-a-location-for-web"></a>

It is possible execute a booking creation request using the `bookingService.performBooking()` method which takes a booking object as input. If the booking is successfully performed, the booking will return in the block with an assigned `bookingId`.

```
  const bookingService = mapsindoors.services.BookingService;
  const start = new Date();
  const end = new Date(start).setHours(start.getHours() + 1);
  const bookingRequest = new bookingService.MPBooking({
    locationId: '0c44207987174561a53fb00a',
    title: 'Meeting',
    description: 'Meeting description',
    participants: ["myemail@email.com"],
    startTime: start,
    endTime: end
  });

  const myBooking = await bookingService.performBooking(bookingRequest);
```

In the above example a `Booking` object is created and several properties are assigned:

* The ID of related Location object
* A title for the booking
* A description for the booking.
* Participants for the Event being created through the booking
* Start and end time

Depending on the Booking provider, the participants will receive invites for an event created by this Booking request.

#### Cancelling a Booking of a Location for Web[​](https://docs.mapsindoors.com/booking#cancelling-a-booking-of-a-location-for-web) <a href="#cancelling-a-booking-of-a-location-for-web" id="cancelling-a-booking-of-a-location-for-web"></a>

It is possible to cancel a created `Booking` using the `bookingService.cancelBooking()` method which takes an existing `Booking` object as input.

```
  const bookingService = mapsindoors.services.BookingService;
  const myMeetingRoom = await locationsService.getLocation('0c44207987174561a53fb00a');

  const start = new Date();
  const end = new Date(start).setHours(start.getHours() + 1);

  const bookings = await bookingService.getBookingsUsingQuery({
      location: myMeetingRoom,
      startTime: start,
      endTime: end
  });

  if(bookings.length > 0) {
    await bookingService.cancelBooking(bookings[0]);
  }
```
