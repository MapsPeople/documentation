
# Additional Location Details

MapsIndoors locations can have a variety of additional details, such as phone numbers, websites, emails, and opening hours. These details can be managed in the CMS, read [this article](products/cms/additional-location-details.md) for how to set them up or edit them.

This guide shows how to access and display these details in your Android app, both in code and in a Jetpack Compose UI.

---

## Accessing Additional Details in Code

Each `MPLocation` object contains an `additionalDetails` property, which is a list of detail objects. Each detail has a type, value, key, and other fields. You can iterate through these details and handle them according to their type.

{% code title="Print all details example" overflow="wrap" lineNumbers="true" %}
```kotlin
fun printDetails(location: MPLocation) {
    // Get the list of additional details from the location
    val details = location.additionalDetails
    if (details.isNullOrEmpty()) return // No details to print

    for (detail in details) {
        // Only print active details
        if (detail.active != true) continue

        // Print a message depending on the detail type
        print(
            when (detail.detailType) {
                MPDetailType.Text ->
                    // Text details (e.g. description)
                    "${location.name} additional information: ${detail.value}"
                MPDetailType.Phone ->
                    // Phone details (all details have a key for identification)
                    "${location.name}'s phone number (${detail.key}): ${detail.value}"
                MPDetailType.URL ->
                    // URL details (details can have user friendly display text)
                    "${location.name}'s website: ${detail.value} (label: ${detail.displayText})"
                MPDetailType.Email ->
                    // Email details (details can be supplied with an icon URL)
                    "${location.name}'s email: ${detail.value} (icon: ${detail.icon})"
                MPDetailType.OpeningHours ->
                    // Opening hours (has a special field 'openingHours' that is only used for this)
                    "${location.name} opening hours: ${detail.openingHours?.toString()}"
                null ->
                    // Unknown detail type
                    "Unknown detail type for ${location.name}"
            }
        )
    }
}
```
{% endcode %}

> **Note:** A single location can have multiple details of the same type. Use unique keys to distinguish them.

### Filtering and Printing Specific Details

Your locations can have multiple details, in this case the location has mutile phone numbers: customer service and sales with the respective keys `customerService` and `sales`, you can filter and print them by key:

{% code title="Print specific phone numbers example" overflow="wrap" lineNumbers="true" %}
```kotlin
fun printPhoneNumbers(location: MPLocation) {
    // Get the list of additional details
    val details = location.additionalDetails
    if (details.isNullOrEmpty()) return

    // Filter for phone number details
    details.filter { it.detailType == MPDetailType.Phone }.forEach { phoneDetail ->
        // Print a message based on the key
        when (phoneDetail.key) {
            "customerService" -> print("Reach Customer Service: ${phoneDetail.value}")
            "sales" -> print("Reach Sales: ${phoneDetail.value}")
            else -> print("Reach ${phoneDetail.displayText} number: ${phoneDetail.value}")
        }
    }
}
```
{% endcode %}

---

## Displaying Additional Details in Jetpack Compose UI

You can present additional details in your app's UI using Jetpack Compose. Below are two examples: one for showing all phone numbers, and one for showing all details in a single list.

### Example: Show All Phone Numbers

{% code title="PhoneNumbersList composable" overflow="wrap" lineNumbers="true" %}
```kotlin
@Composable
fun PhoneNumbersList(location: MPLocation) {
    // Filter for phone number details
    val phoneDetails = location.additionalDetails
        ?.filter { it.detailType == MPDetailType.Phone }
        .orEmpty()

    if (phoneDetails.isEmpty()) {
        // Show a message if there are no phone numbers
        Text("No phone numbers available")
        return
    }

    // Display each phone number in a column
    Column {
        phoneDetails.forEach { detail ->
            // Choose a label based on the key
            val label = when (detail.key) {
                "customerService" -> "Customer Service"
                "sales" -> "Sales"
                else -> detail.displayText ?: "Other"
            }
            // Show the label and value
            Text("$label: ${detail.value}")
        }
    }
}
```
{% endcode %}

---

### Example: Show All Details in a Single UI

You can display all available details for a location in a single composable. This example shows phone numbers, emails, URLs, text, and opening hours in a unified list, with comments explaining each step:

{% code title="LocationDetailsList composable" overflow="wrap" lineNumbers="true" %}
```kotlin
@Composable
fun LocationDetailsList(location: MPLocation) {
    // Get all active details for the location
    val details = location.additionalDetails.orEmpty().filter { it.active == true }

    if (details.isEmpty()) {
        // Show a message if there are no details
        Text("No details available")
        return
    }

    // Display all details in a column, sorted by type for grouping
    Column {
        details.sortedBy { it.detailType?.ordinal ?: 0 }.forEach { detail ->
            when (detail.detailType) {
                MPDetailType.Phone -> {
                    // Show phone numbers with a label based on the key
                    val label = when (detail.key) {
                        "customerService" -> "Customer Service"
                        "sales" -> "Sales"
                        else -> detail.displayText ?: "Other Phone"
                    }
                    Text("$label: ${detail.value}")
                }
                MPDetailType.Email -> {
                    // Show email address
                    Text("Email: ${detail.value}")
                }
                MPDetailType.URL -> {
                    // Show website with display text if available
                    val label = detail.displayText ?: "Website"
                    Text("$label: ${detail.value}")
                }
                MPDetailType.Text -> {
                    // Show generic text info
                    Text("Info: ${detail.value}")
                }
                MPDetailType.OpeningHours -> {
                    // Show opening hours, or N/A if missing
                    Text("Opening hours: ${detail.openingHours?.toString() ?: "N/A"}")
                }
                else -> {
                    // Fallback for unknown types
                    Text("Other: ${detail.value}")
                }
            }
        }
    }
}
```
{% endcode %}

This composable will show all the different types of additional details for a location in a single list, making it easy to present all relevant information to the user.
