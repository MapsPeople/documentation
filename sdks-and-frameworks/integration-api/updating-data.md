# Updating Data

The Integration API changes existing data with PUT. Every PUT endpoint takes an array of objects, even when you only change one object, and every object needs its `id`.

The PUT endpoints differ in what happens to a field you leave out of the body. Some keep the stored value, others clear it.

## Read, modify, write

This pattern is safe on every PUT endpoint:

1. Read the objects with the GET of the same endpoint.
2. Change only the fields you want to change.
3. Send the whole objects back with the PUT.

Every GET returns the fields its PUT reads, so sending back what you read never clears a field by accident.

{% hint style="warning" %}
Do not build a PUT body from scratch with only the fields you want to change. On most endpoints, a field you leave out is cleared.
{% endhint %}

## Fields left out, per endpoint

* `PUT /{apiKey}/api/geodata` keeps the top-level fields you leave out. A field you send replaces the stored value whole. Send `properties` and `baseTypeProperties` complete, as read from the GET, even when you change only one value in them.
* `PUT /{apiKey}/api/categories` replaces every field. A field you leave out is cleared.
* `PUT /{apiKey}/api/displaytypes` replaces every field except `properties`, which is merged per language. A property template you leave out is deleted, and its values are removed from locations.
* `PUT /{apiKey}/api/routing/routeelements` replaces `waitTime`, `restrictions` and `onewayDirection`. See [Route Access](route-access.md) for how to open and close route elements.

The [OpenAPI Specification](openapi-specification.md) describes each endpoint in detail.
