# Additional Location Details

The **Additional Location Details** feature in the CMS provides a solution for customers who need an easy way to configure specific details about their locations. These details can be added as properties to locations and currently support the following types:

* **Link**
* **Phone Number**
* **E-mail**
* **Opening Hours**

**Configuring Additional Location Details**

When selecting an additional location detail (e.g., a **Link**), you will be presented with a configuration view.

<figure><img src="../../.gitbook/assets/image (64).png" alt=""><figcaption><p>Additional location details in the CMS</p></figcaption></figure>

The CMS determines the type of data sent for each entry. When adding a new entry, it's important to note that the **Detail Key** field can only be edited during the initial creation.

* **URL** – Defines where users will be redirected when clicking the button. Based on the implementation, the link will open in a new tab.
* **Display Text** – The text label shown instead of the actual value (for URL, phone number or email). For example, show "Google" instead of www.google.com.
* **Show in App** – Controls whether the component is visible in the MapsIndoors Web App / Map Template.
* **Display Icon** – Specifies the icon displayed alongside the display text.
* **Detail Key** – Serves as a unique identifier for the component within the data.

<figure><img src="../../.gitbook/assets/image (65).png" alt=""><figcaption><p>Link Additional Detail form in the CMS</p></figcaption></figure>

**Ordering and Display in the MapsIndoors Web App**

The **MapsIndoors Web App** displays these contact action buttons in the same order they appear in the CMS (provided that “Show in App” is enabled). To adjust the order, use the up and down buttons next to the edit options.

<figure><img src="../../.gitbook/assets/image (67).png" alt=""><figcaption><p>Multiple Additional Details that will be shown as Contact Action Buttons in the map template</p></figcaption></figure>

To add **Opening Hours**, use the **Additional Details** button under **Location Details**. Here, you can input operating hours for each weekday and mark specific days as "Closed" if e.g. the shop is closed all day.

<figure><img src="../../.gitbook/assets/image (68).png" alt=""><figcaption><p>Opening hours in the CMS</p></figcaption></figure>
