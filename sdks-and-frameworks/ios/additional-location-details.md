
# Additional Location Details

MapsIndoors locations can have a variety of additional details, such as phone numbers, websites, emails, and opening hours. These details can be managed in the CMS, read [this article](products/cms/additional-location-details.md) for how to set them up or edit them.

---

## Accessing Additional Details in Code

Each `MPLocation` object contains an `additionalDetails` property, which is a list of detail objects. Each detail has a type, value, key, and other fields. You can iterate through these details and handle them according to their type.

{% code title="Print all details example" overflow="wrap" lineNumbers="true" %}
```swift
func printDetails(location: MPLocation) {
    // Get and iterate over the list of additional details from the location
    for detail in location.additionalDetails ?? [] {
        // Only print active details
        guard detail.active ?? false else { continue }
        
        let string = switch detail.detailType?.type {
        case .text:
            // Text details (e.g. description)
            "\(location.name) additional information: \(detail.value ?? "nil")"
        case .phone:
            // Phone details (all details have a key for identification)
            "\(location.name)'s phone number (\(detail.key ?? "nil")): \(detail.value ?? "nil")"
        case .url:
            // URL details (details can have user friendly display text)
            "\(location.name)'s website: \(detail.value ?? "nil") (label: \(detail.displayText ?? "nil"))"
        case .email:
            // Email details (details can be supplied with an icon URL)
            "\(location.name)'s email: \(detail.value ?? "nil") (icon: \(detail.icon ?? "nil"))"
        case .openingHours:
            // Opening hours (has a special field 'openingHours' that is only used for this)
            "\(location.name) opening hours: \(detail.openingHours?.description ?? "No opening hours")"
        case .none:
            "Unknown detail type for \(location.name)"
        case .some(_):
            "Unknown detail type for \(location.name)"
        }
        print(string)
    }
}
```
{% endcode %}

> **Note:** A single location can have multiple details of the same type. Use unique keys to distinguish them.

### Filtering and Printing Specific Details

Your locations can have multiple details, in this case the location has multiple phone numbers: customer service and sales with the respective keys `customerService` and `sales`, you can filter and print them by key:

{% code title="Print specific phone numbers example" overflow="wrap" lineNumbers="true" %}
```swift
func printPhoneNumbers(location: MPLocation) {
    // Get the list of additional details
    guard let details = location.additionalDetails else { return }
    
    // Filter details of type "phone", and print messages for each custom key in your data
    details.filter { $0.detailType?.type == .phone  }.forEach { detail in
        switch detail.key {
        case "customerService":
            print("Customer Service: \(detail.value ?? "N/A")")
        case "sales":
            print("Sales: \(detail.value ?? "N/A")")
        default:
            print("Phone (\(detail.key ?? "N/A")): \(detail.value ?? "N/A")")
        }
    }
}
```
{% endcode %}