---
description: >-
  On this page you will gain knowledge of how to style marker labels using
  Display Rules. NB: the stylings show on this page are only available when
  using MapsIndoors together with the Mapbox Maps SDK.
---

# Label styling through Display Rules

{% hint style="success" %}
This feature is available as of SDK version 4.3.0
{% endhint %}

{% hint style="info" %}
To get an overview of what Display Rules are and can be used for, read the [Display Rules](../../../products/cms/display-rules.md) page first.
{% endhint %}

## Basic styling

There are a number of fields in Display Rules that can be used to style the label:

{% tabs %}
{% tab title="Kotlin" %}
```kotlin
MapsIndoors.getMainDisplayRule()?.let { dr ->
    // The color of the label as a hex string
    val labelTextColor: String? = dr.labelTextColor
    // The size of the label in pixels
    val labelTextSize: Int? = dr.labelTextSize
    // The opacity of the label as a value between 0 and 1
    val labelTextOpacity: Float? = dr.labelTextOpacity
    // The color of the label's halo
    val labelHaloColor: String? = dr.labelHaloColor
    // The width of the halo in pixels, this value should not be more than 1/4 of the LabelSize
    val labelHaloWidth: Int? = dr.labelHaloWidth
    // The distance at which the halo begins to blur, this should not be larger than HaloWidth
    val labelHaloBlur: Int? = dr.labelHaloBlur
}
```
{% endtab %}

{% tab title="Java" %}
```java
MPDisplayRule dr = MapsIndoors.getMainDisplayRule();
if (dr != null) {
    // The color of the label as a hex string
    String labelTextColor = dr.getLabelTextColor();
    // The size of the label in pixels
    Integer labelTextSize = dr.getLabelTextSize();
    // The opacity of the label as a value between 0 and 1
    Float labelTextOpacity = dr.getLabelTextOpacity();
    // The color of the label's halo
    String labelHaloColor = dr.getLabelHaloColor();
    // The width of the halo in pixels, this value should not be more than 1/4 of the LabelSize
    Int labelHaloWidth = dr.getLabelHaloWidth();
    // The distance at which the halo begins to blur, this should not be larger than HaloWidth
    Int labelHaloBlur = dr.getLabelHaloBlur(); 
}
```
{% endtab %}
{% endtabs %}

In addition to these styling fields there are also fields that determine when to display the label. If you want a full list of the fields available for configuration on Display Rules, [look here](../../../products/cms/display-rules.md).

All examples have been using the `demo` solution and all changes have been applied to the Main Display Rule.

### Styling the label text

With these fields it is possible to style the label to fit your design. In the following example we create a function that changes the styling on a label by doubling the size of the text, and changing the text color to green ("#00FF00").

{% tabs %}
{% tab title="Kotlin" %}
```kotlin
fun styleLabelText(dr: MPDisplayRule) {
    dr.labelTextSize = dr.labelTextSize?.times(2)
    dr.labelTextColor = "#00FF00"
}
```
{% endtab %}

{% tab title="Java" %}
```java
// Some code
```
{% endtab %}
{% endtabs %}

If we apply this function to a Display Rule, it could look like this:

<figure><img src="../../../.gitbook/assets/android_label_dr_text_example.png" alt=""><figcaption><p>An example of large green text labels</p></figcaption></figure>

### Styling the label halo

It is also possible to style the halo, but the halo is a bit harder to properly style. the `haloWidth` and `haloBlur` fields have immense impact on the feel of the labels, so make sure to try out many possible configurations! In this example we change the text size to be `24` to get a bigger range out of the halo. Here we want a soft halo, so we make the `haloBlur` larger than the `haloWidth`:

{% tabs %}
{% tab title="Kotlin" %}
```kotlin
fun styleLabelHalo(dr: MPDisplayRule) {
    dr.labelTextSize = 24
    dr.labelHaloWidth = 1
    dr.labelHaloColor = "#000040"
    dr.labelHaloBlur = 3
}
```
{% endtab %}

{% tab title="Java" %}
```java
// Some code
```
{% endtab %}
{% endtabs %}

If applied it might look like this:

<figure><img src="../../../.gitbook/assets/android_label_dr_halo_example.png" alt=""><figcaption><p>An example of a soft "blue" halo on large text</p></figcaption></figure>

### Styling text and halo

With the knowledge of styling the text and label we can now coordinate the styling of the entire label, and create a style that utilizes both. In this example we have a function that modifies the label to be twice as large as it was set in the CMS, and make the text white, with a thin outline halo.

{% tabs %}
{% tab title="Kotlin" %}
```kotlin
fun styleLabel(dr: MPDisplayRule) {
    dr.labelTextSize = dr.labelTextSize?.times(2)
    dr.labelTextColor = "#FFFFFF"
    dr.labelHaloWidth = 1
    dr.labelHaloColor = "#000000"
    dr.labelHaloBlur = 0
}
```
{% endtab %}

{% tab title="Java" %}
```java
// Some code
```
{% endtab %}
{% endtabs %}

If applied it might look like this:

<figure><img src="../../../.gitbook/assets/android_label_dr_label_example.png" alt=""><figcaption><p>An example of labels with white text and a black outline</p></figcaption></figure>

## Flat labels

By changing the `labelType` field from the default `floating` to `flat`, it becomes possible to align the label with the map instead of the camera.

Flat labels are best shown by disabling the icon, letting the text become centered on the location. Here is an example of a function that changes to type of a label to `flat` and hides the icon to center the label:

{% tabs %}
{% tab title="Kotlin" %}
```kotlin
fun setLabelToFlat(dr: MPDisplayRule) {
    dr.isIconVisible = false
    dr.labelType = MPLabelType.FLAT
}
```
{% endtab %}

{% tab title="Java" %}
```java
void setLabelToFlat(MPDisplayRule dr) {
    dr.setIconVisible(false);
    dr.setLabelType(MPLabelType.FLAT);
}
```
{% endtab %}
{% endtabs %}

Now the label will be drawn "below" the marker, flat on the ground.

<figure><img src="../../../.gitbook/assets/android_label_dr_set_flat_example.png" alt=""><figcaption><p>An example of flat labels without icons</p></figcaption></figure>

It is possible to change the rotation of the label by modifying the `bearing` field. This field is measured in degrees where 0° is pointing north and 90° is pointing east. Here is an example where the type is changed to flat and the bearing of the label is changed to a new value:

{% tabs %}
{% tab title="Kotlin" %}
```kotlin
fun changeLabelBearing(dr: MPDisplayRule, newBearing: Float) {
    dr.labelType = MPLabelType.FLAT
    dr.labelBearing = newBearing
}
```
{% endtab %}

{% tab title="Java" %}
```java
void changeLabelBearing(MPDisplayRule dr, Float newBearing) {
    dr.setLabelType(MPLabelType.FLAT);
    dr.setLabelBearing(newBearing);
}
```
{% endtab %}
{% endtabs %}

If applied with a bearing of 45° it might look like this:

<figure><img src="../../../.gitbook/assets/android_label_dr_label_bearing_example.png" alt=""><figcaption><p>An example of flat labels rotated to 45° north</p></figcaption></figure>

A `flat` label is styleable, just like a `floating` label:

{% tabs %}
{% tab title="Kotlin" %}
```kotlin
fun styleFlatLabel(dr: MPDisplayRule) {
    dr.isIconVisible = false
    dr.labelType = MPLabelType.FLAT
    dr.labelBearing = 45.0f
    dr.labelTextColor = "#FF0000"
    dr.labelTextSize = 100
    dr.labelHaloColor = "#FFFF00"
    dr.labelHaloWidth = 3
}
```
{% endtab %}

{% tab title="Java" %}
```java
void styleFlatLabel(MPDisplayRule dr) {
    dr.setIconVisible(false);
    dr.setLabelType(MPLabelType.FLAT);
    dr.setLabelBearing(45.0f);
    dr.setLabelTextColor("#FF0000");
    dr.setLabelTextSize(100);
    dr.setLabelHaloColor("#FFFF00");
    dr.setLabelHaloWidth(3);
}
```
{% endtab %}
{% endtabs %}

If applied the labels might look like this:

<figure><img src="../../../.gitbook/assets/android_label_dr_flat_example.png" alt=""><figcaption><p>An example of labels being flat and fully customized with display rules</p></figcaption></figure>

You now have knowledge of how to style labels and create flat labels when using MapsIndoors on Android, use it wisely.
