---
description: iOS v4
---

# Booking

This guide covers the different aspects of _Booking_ in the MapsIndoors iOS SDK. The concept of Booking in MapsIndoors implies that specific Locations in your MapsIndoors dataset is treated as Bookable resources. Typical bookable resources could be meeting rooms and workspaces.

A MapsIndoors dataset can only have bookable resources if an integration with a Booking provider exists. Current examples of Booking providers are _Google Calendar_ and _Microsoft Office 365_. These providers and more can be added and integrated to your MapsIndoors project by request.

The central service in the SDK managing Bookings is the Booking Service.

```swift
MPMapsIndoors.shared.bookingService
```

The Booking Service can help with the following tasks:

* [List Bookable Locations](https://docs.mapsindoors.com/booking#bookable-locations)
* [Work with Bookings](https://docs.mapsindoors.com/booking#bookings)
  * [Listing Bookings for a Location](https://docs.mapsindoors.com/booking#listing-bookings-for-a-location)
  * [Performing a Booking of a Location](https://docs.mapsindoors.com/booking#performing-a-booking-of-a-location)
  * [Cancelling a Booking of a Location](https://docs.mapsindoors.com/booking#cancelling-a-booking-of-a-location)

> By default, the `MPBookingService` performs anonymous Bookings using a service account known to MapsIndoors. However, it is also possible to list, perform and cancel Bookings [on behalf of a user](https://docs.mapsindoors.com/booking#user-authenticated-bookings).

### Bookable Locations[​](https://docs.mapsindoors.com/booking#bookable-locations) <a href="#bookable-locations" id="bookable-locations"></a>

To determine whether or not a Location is bookable can be looked up using the `MPMapsIndoors.shared.bookingService. bookableLocationsUsing(query: MPBookableQuery)` method. Below is an example of querying for bookable Locations:

```swift
let startTime = Date()
let endTime = startTime.advanced(by: 60*60)
let bookableQuery = MPBookableQuery(startTime: startTime, endTime: endTime)
let bookingService = MPMapsIndoors.shared.bookingService

if let locations = try? await bookingService.bookableLocationsUsing(query: bookableQuery) {
    
}
```

The above example creates a query for Locations that are bookable for a timespan between now and 1 hour ahead.

It is also possible to check a location statically using `MPLocation.isBookable`, but please note that this information is not a dynamic property reflecting the current bookable state from the Booking Service. If `MPLocation.isBookable` is true it means that the Location has a potentially bookable resource known by the integrated Booking provider, but still it might be booked for a specific time.

#### Performing a Booking of a Location[​](https://docs.mapsindoors.com/booking#performing-a-booking-of-a-location) <a href="#performing-a-booking-of-a-location" id="performing-a-booking-of-a-location"></a>

It is possible execute a booking creation request using the `MPMapsIndoors.shared.bookingService.performBooking(MPBooking)` method which takes a booking object as input. If the booking is successfully performed, the booking will return in the block with an assigned `bookingId`.

```swift
do {
    let booking = MPBooking()
    booking.location = bookableLocation
    booking.title = "Meeting"
    booking.startTime = Date()
    booking.participantIds = ["myemail@email.com"]
    booking.endTime = booking.startTime!.addingTimeInterval(60*60)
    try await MPMapsIndoors.shared.bookingService.performBooking(booking) 
} catch {
    let alert = UIAlertController(title: "Ooops!", message: "\(error)", preferredStyle: .alert)
    alert.addAction(UIAlertAction(title: "OK", style: .default, handler: nil))
    self.present(alert, animated: true)
}
```

In the above example a Booking object is created and several properties are assigned:

* The related Location object
* A Title for the Booking
* Participants for the Event being created through the Booking
* Start and end time

Depending on the Booking provider, the participants will receive invites for an event created by this Booking request.

#### Cancelling a Booking of a Location[​](https://docs.mapsindoors.com/booking#cancelling-a-booking-of-a-location) <a href="#cancelling-a-booking-of-a-location" id="cancelling-a-booking-of-a-location"></a>

It is possible to cancel a created Booking using the `MPBookingService.cancel()` method which takes an existing Booking object as input.

```swift
do {
    if (try await MPMapsIndoors.shared.bookingService.cancelBooking(booking)) != nil {
        let alert = UIAlertController(title: "Booking was cancelled!", message: "Booking was successfully cancelled!", preferredStyle: .alert)
        alert.addAction(UIAlertAction(title: "OK", style: .default, handler: nil))
        present(alert, animated: true)
    }
} catch {
    let alert = UIAlertController(title: "Ooops!", message: "\(error)", preferredStyle: .alert)
    alert.addAction(UIAlertAction(title: "OK", style: .default, handler: nil))
    present(alert, animated: true)
}
```

### Booking in Practice[​](https://docs.mapsindoors.com/booking#booking-in-practice) <a href="#booking-in-practice" id="booking-in-practice"></a>

In this tutorial we will create a Booking experience using the Booking Service in MapsIndoors. You will learn how to list bookable Locations, list Bookings for a Location and perform new Bookings using the Booking Service, `MPBookingService`.

Please note that a MapsIndoors dataset can only have bookable resources if an integration with a booking provider exists. Current examples of booking providers are _Google Calendar_ and _Microsoft Office 365_. These providers, and more, can be added and integrated to your MapsIndoors project by request. It is a prerequisite for this tutorial that the API key used refers to a dataset containing bookable Locations.

### Listing Bookable Locations[​](https://docs.mapsindoors.com/booking#listing-bookable-locations) <a href="#listing-bookable-locations" id="listing-bookable-locations"></a>

We will start by listing bookable Locations. Create a class `BookableLocationsController` inheriting from `UITableViewController`.

```swift
class BookableLocationsController: UITableViewController {
```

Create a private property that should hold our bookable Locations.

```swift
private var bookableLocations = [MPLocation]()
```

In your `viewDidAppear()` method,

1. Initialise a `MPBookableQuery` object with a timespan for your potential Booking.
2. Call `getBookableLocations()` on the `MPBookingService` instance.
3. Assign the returned locations to your `bookableLocations` property.

```swift
override func viewWillAppear(_ animated: Bool) {
    super.viewWillAppear(animated)

    self.navigationItem.rightBarButtonItem = UIBarButtonItem(title: "Auth", style: .plain, target: self, action: #selector(openAuthConfig))
    self.navigationItem.rightBarButtonItem = UIBarButtonItem(title: "Login", style: .plain, target: self, action: #selector(openLogin))

    Task {
        await refresh()
    }
}
```

add a refresh function:

```swift
func refresh() async {
    let startTime = Date()
    let endTime = startTime.advanced(by: 60*60)
    let bookableQuery = MPBookableQuery(startTime: startTime, endTime: endTime)

    let bookingService = MPMapsIndoors.shared.bookingService

    if let locations = try? await bookingService.locationsConfiguredForBooking(bookableQuery) {
        locationsConfiguredForBooking = locations
        tableView.reloadData()
    }

    if let locations = try? await bookingService.bookableLocationsUsing(query: bookableQuery) {
        bookableLocations = locations
        tableView.reloadData()
    }
}
```

In `numberOfSections(in:)` add:

```swift
override func numberOfSections(in tableView: UITableView) -> Int {
    return 2
}
```

In `tableView(_:numberOfRowsInSection:)` , return the size of `bookableLocations`.

```swift
override func tableView(_ tableView: UITableView, numberOfRowsInSection section: Int) -> Int {
    if section == 0 {
        return bookableLocations.count
    } else {
        return locationsConfiguredForBooking.count
    }
}
```

In `tableView(_:cellForRowAt:)`, create a `UITableViewCell` with the current Locations name as the text for the label.

```swift
override func tableView(_ tableView: UITableView, cellForRowAt indexPath: IndexPath) -> UITableViewCell {
    let cell = UITableViewCell()
    var list = locationsConfiguredForBooking
    if indexPath.section == 0 {
        list = bookableLocations
    }
    let location = list[indexPath.row]
    cell.textLabel?.text = location.name
    return cell
}
```

In `tableView(_:didSelectRowAt:)`, get the relevant `MPLocation` for the `indexPath`. Initialise a `BookingsController` which we will implement next. Assign the selected Location to a `bookableLocation` property on `BookingsController` and push the controller to the navigation stack.

```swift
override func tableView(_ tableView: UITableView, didSelectRowAt indexPath: IndexPath) {
    let bookingsVC = BookingsController()
    var list = locationsConfiguredForBooking
    if indexPath.section == 0 {
        list = bookableLocations
    }
    let location = list[indexPath.row]
    bookingsVC.bookableLocation = location
    navigationController?.pushViewController(bookingsVC, animated: true)
}
```

### Listing Bookings for a Location[​](https://docs.mapsindoors.com/booking#listing-bookings-for-a-location) <a href="#listing-bookings-for-a-location" id="listing-bookings-for-a-location"></a>

Create a new controller, `BookingsController` inheriting again from `UITableViewController`. This controller will list the Bookings for a locations within a timespan, as well as give access to creating new and editing bookings.

```swift
class BookingsController: UITableViewController {
```

Create a public property `bookableLocation` that will hold the Location we want to book.

```swift
var bookableLocation : MPLocation?
```

Create a private property `bookings` that can hold the Location's bookings.

```swift
private var bookings = [MPBooking]()
```

In your `viewDidLoad()` method, initialise a `UIBarButtonItem` with the title `Book`targeting `newBooking` which we will create later. Add the button to the `navigationItem`.

```swift
let button = UIBarButtonItem(title: "Book", style: .plain, target: self, action: #selector(newBooking))
self.navigationItem.rightBarButtonItem = button
```

Also in your `viewDidLoad()` method, initialise a `MPBookingsQuery`with the `MPLocation` stored in `bookableLocation` and a timespan, in this example 24 hours starting one hour ago.

```swift
let bookingsQuery = MPBookingsQuery()
bookingsQuery.location = bookableLocation
bookingsQuery.startTime = Date().advanced(by: -60*60)
bookingsQuery.endTime = bookingsQuery.startTime?.advanced(by: 24*60*60)
```

Lastly in your `viewDidLoad()` method, call `bookingsUsing(query: bookingsQuery)`with the `bookingsQuery` we just created. Store the returned Bookings in our `bookings` property.

```swift
Task {
    if let bookings = try await MPMapsIndoors.shared.bookingService.bookingsUsing(query: bookingsQuery) {
        self.bookings = bookings
        tableView.reloadData()
    }
}
```

In `numberOfSections(in:)`, return 1 since we only need one section.

```swift
override func numberOfSections(in tableView: UITableView) -> Int {
    return 1
}
```

In `tableView(_:numberOfRowsInSection:)`, return the size of `bookings`.

```swift
override func tableView(_ tableView: UITableView, numberOfRowsInSection section: Int) -> Int {
    return bookings.count
}
```

In `tableView(_:cellForRowAt:)`, create a `UITableViewCell` with the current Booking title as the text for the label.

```swift
override func tableView(_ tableView: UITableView, cellForRowAt indexPath: IndexPath) -> UITableViewCell {
    let cell = UITableViewCell()
    let booking = bookings[indexPath.row]
    cell.textLabel?.text = booking.title
    return cell
}
```

In `tableView(_:didSelectRowAt:)`, get the relevant `MPBooking` for the `indexPath` and call `editBooking()` with that Booking.

```swift
override func tableView(_ tableView: UITableView, didSelectRowAt indexPath: IndexPath) {
    let booking = bookings[indexPath.row]
    editBooking(booking: booking)
}
```

Create a method `editBooking(booking:)`. In this mehod, initialise a `BookingController` which we will implement next. Assign the selected booking to the `BookingController` and push the controller to the navigation stack.

```swift
func editBooking(booking:MPBooking) {
    let bookingVC = BookingController()
    bookingVC.booking = booking
    navigationController?.pushViewController(bookingVC, animated: true)
}
```

Create an Objective-C exposed method `newBooking()` which will be use by our `UIBarButtonItem` created in `viewDidLoad()`. In the `newBooking()` mehod, initialise a new `MPBooking` instance and provide some default values for the Booking. Call the newly created `editBooking(booking:)` with the Booking instance.

```swift
@objc func newBooking() {
    let booking = MPBooking()
    booking.location = bookableLocation
    booking.title = "Meeting"
    booking.startTime = Date()
    booking.participantIds = ["myemail@email.com"]
    booking.endTime = booking.startTime!.addingTimeInterval(60*60)
    editBooking(booking: booking)
}
```

### Editing, Performing and Cancelling Bookings[​](https://docs.mapsindoors.com/booking#editing-performing-and-cancelling-bookings) <a href="#editing-performing-and-cancelling-bookings" id="editing-performing-and-cancelling-bookings"></a>

We need a third controller to display, edit and perform an actual Booking.

We will create an enum model to keep track on the different parts of the `MPBooking` model displayed through the view controller.

```swift
enum BookingRow : Int {
    case title = 0
    case description = 1
    case start = 2
    case end = 3
    case id = 4
    case count = 5
}
```

Create `BookingController` inheriting once again from `UITableViewController`.

```swift
class BookingController: UITableViewController {
```

Create a public property `booking` that will hold our Booking.

```swift
var booking = MPBooking()
```

Create a method `updateButtons()` that will either place a `book button` if there is no `bookingId` on the Booking, which means it was created locally, or place a `cancel button` if a `bookingId` exist for the Booking, which means it was selected from a list of Bookings fetched from the `MPBookingService`.

```swift
private func updateButtons() {
    if booking.bookingId != nil {
        self.navigationItem.rightBarButtonItem = UIBarButtonItem(title: "Cancel Booking", style: .plain, target: self, action: #selector(cancel))
    } else {
        self.navigationItem.rightBarButtonItem = UIBarButtonItem(title: "Create Booking", style: .plain, target: self, action: #selector(book))
    }
}
```

In your `viewDidLoad()` method, just call the `updateButtons()` method.

```swift
override func viewDidLoad() {
    updateButtons()
}
```

Create an Objective-C exposed method `book()` which will be use by our `UIBarButtonItem` inserted in `viewDidLoad()`. In the `book()` mehod, call `perform(booking)` on the `MPBookingService` instance with our Booking object as input. If all goes well and we have a Booking returned in the block, we assign this new Booking to our `booking` propery and refresh our views. If not, we assume that we have an error, and show this in an alert controller.

```swift
@objc private func book() async {
    do {
        if let booking = try await MPMapsIndoors.shared.bookingService.performBooking(booking) {
            self.booking = booking
            updateButtons()
            tableView.reloadData()
        }
    } catch {
        let alert = UIAlertController(title: "Ooops!", message: "\(error)", preferredStyle: .alert)
        alert.addAction(UIAlertAction(title: "OK", style: .default, handler: nil))
        self.present(alert, animated: true)
    }
}
```

Create another Objective-C exposed method `cancel()` which will be use by our `UIBarButtonItem` inserted in `viewDidLoad()`. In the `cancel()` mehod, call `cancel(booking)` on the `MPBookingService` instance with our Booking object as input. If all goes well and we have a Booking returned in the block, we assign this new Booking to our `booking` propery and refresh our views. If not, we assume that we have an error, and show this in an alert controller.

```swift
@objc private func cancel() async {
        do {
            if (try await MPMapsIndoors.shared.bookingService.cancelBooking(booking)) != nil {
                let alert = UIAlertController(title: "Booking was cancelled!", message: "Booking was successfully cancelled!", preferredStyle: .alert)
                alert.addAction(UIAlertAction(title: "OK", style: .default, handler: nil))
                present(alert, animated: true)
            }
        } catch {
            let alert = UIAlertController(title: "Ooops!", message: "\(error)", preferredStyle: .alert)
            alert.addAction(UIAlertAction(title: "OK", style: .default, handler: nil))
            present(alert, animated: true)
        }
    }
```

In `numberOfSections(in:)`, return 1 since we only need one section.

```swift
override func numberOfSections(in tableView: UITableView) -> Int {
    return 1
}
```

In `tableView(_:numberOfRowsInSection:)`, return the value of `BookingRow.count.rawValue`.

```swift
override func tableView(_ tableView: UITableView, numberOfRowsInSection section: Int) -> Int {
    return BookingRow.count.rawValue
}
```

In `tableView(:cellForRowAt indexPath:)`, create a `UITableViewCell` and create a switch control structure by initialising a `BookingRow` enum value from `indexPath.row`. Based on the cases, populate the `textLabel` with `title`, `bookingDescription`, `startTime`, `endTime` and `bookingId` from your `MPBooking` instance.

```swift
override func tableView(_ tableView: UITableView, cellForRowAt indexPath: IndexPath) -> UITableViewCell {
    let cell = UITableViewCell.init(style: .subtitle, reuseIdentifier: nil)
    switch BookingRow.init(rawValue: indexPath.row)  {
    case .title:
        cell.textLabel?.text = booking.title ?? ""
        cell.detailTextLabel?.text = "Title"
    case .description:
        cell.textLabel?.text = booking.bookingDescription ?? ""
        cell.detailTextLabel?.text = "Description"
    case .start:
        cell.textLabel?.text = "\(booking.startTime ?? Date())"
        cell.detailTextLabel?.text = "Start time"
    case .end:
        cell.textLabel?.text = "\(booking.endTime ?? Date().addingTimeInterval(60*60))"
        cell.detailTextLabel?.text = "End time"
    case .id:
        cell.textLabel?.text = booking.bookingId ?? ""
        cell.detailTextLabel?.text = "Booking id"
    default : ()
    }
    return cell
}
```

In `tableView(_:cellForRowAt:)`, create a switch control structure again by initialising a `BookingRow` enum value from `indexPath.row`. Based on the cases, either initialise and present `FieldEditController` or `DatePickerController` which we will implement next.

```swift
override func tableView(_ tableView: UITableView, didSelectRowAt indexPath: IndexPath) {
    weak var wself = self
    switch BookingRow.init(rawValue: indexPath.row) {
    case .title:
        let fieldEditVC = FieldEditController()
        fieldEditVC.title = "Edit Title"
        fieldEditVC.beginEdit(initialValue: booking.title) { (value) in
            wself?.booking.title = value
            wself?.tableView.reloadData()
        }
        present(fieldEditVC, animated: true, completion: nil)
    case .description:
        let fieldEditVC = FieldEditController()
        fieldEditVC.title = "Edit Description"
        fieldEditVC.beginEdit(initialValue: booking.bookingDescription) { (value) in
            wself?.booking.bookingDescription = value
            wself?.tableView.reloadData()
        }
        present(fieldEditVC, animated: true, completion: nil)
    case .start:
        let dateEditVC = DatePickerController()
        dateEditVC.title = "Edit Start Date"
        dateEditVC.beginEdit(initialValue: booking.startTime) { (value) in
            wself?.booking.startTime = value
            wself?.tableView.reloadData()
        }
        present(dateEditVC, animated: true, completion: nil)
    case .end:
        let dateEditVC = DatePickerController()
        dateEditVC.title = "Edit End Date"
        dateEditVC.beginEdit(initialValue: booking.endTime) { (value) in
            wself?.booking.endTime = value
            wself?.tableView.reloadData()
        }
        present(dateEditVC, animated: true, completion: nil)
    default : ()
    }
}
```

Creating UI for editing text and dates is outside the scope of this tutorial. But since we need it for creating the actual Booking, you are advised to just insert the following 3 controllers into your code.

First a controller `EditController` for arranging the presented editing view skeleton.

```swift
class EditController: UIViewController {
    let doneButton = UIButton()
    let titleLabel = UILabel()
    override var title: String? { didSet { titleLabel.text = title } }
    let sw = UIStackView()
    func spacer() -> UIView {
        return UIView()
    }
    override func viewDidLoad() {
        sw.addArrangedSubview(doneButton)
        sw.addArrangedSubview(titleLabel)
        sw.spacing = 40
        sw.alignment = .center
        sw.axis = .vertical
        let backgroundView = UIView(frame: CGRect(x: 0,y: 0,width: 3000,height: 3000))
        backgroundView.backgroundColor = UIColor.systemBackground
        backgroundView.translatesAutoresizingMaskIntoConstraints = false
        sw.insertSubview(backgroundView, at: 0)
        view = sw

        doneButton.setTitle("Done", for: .normal)
        doneButton.setTitleColor(.link, for: .normal)
        doneButton.addTarget(self, action: #selector(close), for: .touchUpInside)
    }
    @objc func close() {
        self.dismiss(animated: true, completion: nil)
    }
}
```

Secondly, a controller `DatePickerController` inheriting `EditController` presenting and managing the date picker.

```swift
class DatePickerController: EditController {

    private let datePicker = UIDatePicker()
    var didEdit : ((_ value:Date) -> Void)?
    func beginEdit(initialValue:Date?, didEdit: @escaping ((Date) -> Void)) {
        datePicker.date = initialValue ?? Date()
        self.didEdit = didEdit
    }

    override func viewDidLoad() {

        super.viewDidLoad()

        datePicker.addTarget(self, action: #selector(didEditDate), for: .allEvents)

        sw.addArrangedSubview(datePicker)
        sw.addArrangedSubview(spacer())

    }

    @objc func didEditDate() {
        didEdit?(datePicker.date)
    }
}
```

Lastly, a controller `FieldEditController` inheriting `EditController` presenting and managing the text field.

```swift
class FieldEditController: EditController, UITextFieldDelegate {

    private let textField = UITextField()
    private var didEdit: ((_ value:String) -> Void)?
    func beginEdit(initialValue:String?, didEdit: @escaping ((String) -> Void)) {
        textField.text = initialValue ?? ""
        self.didEdit = didEdit
    }

    override func viewDidLoad() {

        super.viewDidLoad()

        sw.addArrangedSubview(textField)
        sw.addArrangedSubview(spacer())

        textField.becomeFirstResponder()
        textField.delegate = self

    }

    func textFieldDidEndEditing(_ textField: UITextField) {
        didEdit?(textField.text ?? "")
    }
}
```

This concludes the tutorial about Booking in the MapsIndoors iOS SDK. Depending on your dataset you should not be far from a working Booking experience where you can list Bookable Locations, list Bookings and create new Bookings.
