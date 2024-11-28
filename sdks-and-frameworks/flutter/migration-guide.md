---
description: This page describes how to migrate between major versions
---

# Migration Guide

## Migrating from v3 to v4

### Color `String` to `Color`

All references to colors in the SDK have been changed from using a color string (which can be ambiguous in whether `aRGB` or `RGBa` is meant to be used) to using Dart `Color` instead.

{% tabs %}
{% tab title="Before" %}
```dart
final displayrule = await getMainDisplayRule();
displayrule.setLabelStyleTextColor("#FF00FF00"); // Red or transparent Purple?
```
{% endtab %}

{% tab title="After" %}
```dart
final displayrule = await getMainDisplayRule();
displayrule.setLabelStyleTextColor(Colors.red); // Definitely red
```
{% endtab %}
{% endtabs %}

### Label position

The default label position has been changed from right of the Marker to underneath the marker.

It will not be possible with the 4.0.0 to programatically change the label positioning, but this feature will become available in 4.1.0.

### `ClearWayType`

`ClearWayType` has been deprecated with the introduction of multiple `wayType` categories, going forward you should use `ClearAvoidWayType` instead.



{% tabs %}
{% tab title="Before" %}
```dart
final service = MPDirectionsService();
service.addAvoidWayType(MPHighway.steps.name);
service.addExcludeWayType(MPHighway.ladder.name);
service.clearWayType(); // What was cleared?
```
{% endtab %}

{% tab title="After" %}
```dart
final service = MPDirectionsService();
service.addAvoidWayType(MPHighway.steps.name);
service.addExcludeWayType(MPHighway.ladder.name);
service.clearAvoidWayType(); // AvoidWayTypes were cleared!
```
{% endtab %}
{% endtabs %}



