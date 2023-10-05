---
description: >-
  All notable changes to this project will be documented in this file. The
  format is based on Keep a Changelog, and this project adheres to Semantic
  Versioning.
---

# MI Components

All notable changes to this project will be documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.0.0/), and this project adheres to [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

### \[12.1] - 2023-01-25[​](https://docs.mapsindoors.com/changelogs/components#121---2023-01-25) <a href="#121---2023-01-25" id="121---2023-01-25"></a>

#### Added[​](https://docs.mapsindoors.com/changelogs/components#added) <a href="#added" id="added"></a>

* Support for French (AZERTY) and German (QWERTZ) layout keyboards.

### \[12.0.1] - 2022-12-12[​](https://docs.mapsindoors.com/changelogs/components#1201---2022-12-12) <a href="#1201---2022-12-12" id="1201---2022-12-12"></a>

#### Changed[​](https://docs.mapsindoors.com/changelogs/components#changed) <a href="#changed" id="changed"></a>

* Upgrading various dependencies to the latest versions.

### \[12.0.0] - 2022-12-08[​](https://docs.mapsindoors.com/changelogs/components#1200---2022-12-08) <a href="#1200---2022-12-08" id="1200---2022-12-08"></a>

#### Changed[​](https://docs.mapsindoors.com/changelogs/components#changed-1) <a href="#changed-1" id="changed-1"></a>

* **mi-map-mapbox**: The Floor Selector is going to be shown all the time, without map interaction.
* **mi-map-googlemaps**: The Floor Selector is going to be shown all the time, without map interaction. The attribute value of `myPositionControlPosition` and `floorSelectorControlPosition` should now be strings corresponding to value of `google.maps.ControlPosition`.

### \[11.15.1] - 2022-08-15[​](https://docs.mapsindoors.com/changelogs/components#11151---2022-08-15) <a href="#11151---2022-08-15" id="11151---2022-08-15"></a>

#### Fixed[​](https://docs.mapsindoors.com/changelogs/components#fixed) <a href="#fixed" id="fixed"></a>

* **mi-dropdown**: In Safari dropdown will be closed when clicked outside of the dropdown.

### \[11.15.0] - 2022-08-11[​](https://docs.mapsindoors.com/changelogs/components#11150---2022-08-11) <a href="#11150---2022-08-11" id="11150---2022-08-11"></a>

#### Added[​](https://docs.mapsindoors.com/changelogs/components#added-1) <a href="#added-1" id="added-1"></a>

* **mi-column**: `monospace` prop for setting the font-family to monospace.

#### Fixed[​](https://docs.mapsindoors.com/changelogs/components#fixed-1) <a href="#fixed-1" id="fixed-1"></a>

* **mi-data-table**: Adjustments of row heights to a more sensible, smaller height.

### \[11.14.0] - 2022-04-25[​](https://docs.mapsindoors.com/changelogs/components#11140---2022-04-25) <a href="#11140---2022-04-25" id="11140---2022-04-25"></a>

#### Fixed[​](https://docs.mapsindoors.com/changelogs/components#fixed-2) <a href="#fixed-2" id="fixed-2"></a>

* **mi-data-table**: Now sorts numeric values as well as strings.
* **mi-data-table**: The checkbox in the header of the table now has the correct state when selected rows are deleted.

#### Added[​](https://docs.mapsindoors.com/changelogs/components#added-2) <a href="#added-2" id="added-2"></a>

* **route-instructions-step**: Now supports escalators.

### \[11.13.2] - 2022-04-21[​](https://docs.mapsindoors.com/changelogs/components#11132---2022-04-21) <a href="#11132---2022-04-21" id="11132---2022-04-21"></a>

#### Fixed[​](https://docs.mapsindoors.com/changelogs/components#fixed-3) <a href="#fixed-3" id="fixed-3"></a>

* Use font-family property from `midt` in all components

### \[11.13.0] - 2022-04-01[​](https://docs.mapsindoors.com/changelogs/components#11130---2022-04-01) <a href="#11130---2022-04-01" id="11130---2022-04-01"></a>

#### Added[​](https://docs.mapsindoors.com/changelogs/components#added-3) <a href="#added-3" id="added-3"></a>

* **mi-dropdown**: Is now aware of its position in the viewport and will adjust the placement of the dropdown accordingly.

### \[11.12.2] - 2022-03-29[​](https://docs.mapsindoors.com/changelogs/components#11122---2022-03-29) <a href="#11122---2022-03-29" id="11122---2022-03-29"></a>

#### Fixed[​](https://docs.mapsindoors.com/changelogs/components#fixed-4) <a href="#fixed-4" id="fixed-4"></a>

* **mi-dropdown**: Is now capable of sorting numeric values.
* **mi-dropdown**: When navigating the list using arrow up or down arrow keys, the currently highlighted item is kept in view by scrolling the list.

### \[11.12.1] - 2022-03-24[​](https://docs.mapsindoors.com/changelogs/components#11121---2022-03-24) <a href="#11121---2022-03-24" id="11121---2022-03-24"></a>

#### Fixed[​](https://docs.mapsindoors.com/changelogs/components#fixed-5) <a href="#fixed-5" id="fixed-5"></a>

**mi-tabs**: Now re-renders upon receiving new content.

### \[11.12.0] - 2022-03-23[​](https://docs.mapsindoors.com/changelogs/components#11120---2022-03-23) <a href="#11120---2022-03-23" id="11120---2022-03-23"></a>

#### Added[​](https://docs.mapsindoors.com/changelogs/components#added-4) <a href="#added-4" id="added-4"></a>

**mi-data-table**: Now supports n-depth object traversal so you can dot into objects when binding in the view.

### \[11.11.2] - 2022-03-07[​](https://docs.mapsindoors.com/changelogs/components#11112---2022-03-07) <a href="#11112---2022-03-07" id="11112---2022-03-07"></a>

#### Fixed[​](https://docs.mapsindoors.com/changelogs/components#fixed-6) <a href="#fixed-6" id="fixed-6"></a>

**mi-dropdown**: Prevent the dropdown component to interfere with other scrollable elements.

### \[11.11.1] - 2022-02-10[​](https://docs.mapsindoors.com/changelogs/components#11111---2022-02-10) <a href="#11111---2022-02-10" id="11111---2022-02-10"></a>

#### Added[​](https://docs.mapsindoors.com/changelogs/components#added-5) <a href="#added-5" id="added-5"></a>

**mi-dropdown**: A `disabled` attribute was added.

#### Fixed[​](https://docs.mapsindoors.com/changelogs/components#fixed-7) <a href="#fixed-7" id="fixed-7"></a>

**mi-dropdown**: Would throw an error when the `filterable` property wasn't set.

### \[11.10.3] - 2022-01-18[​](https://docs.mapsindoors.com/changelogs/components#11103---2022-01-18) <a href="#11103---2022-01-18" id="11103---2022-01-18"></a>

#### Added[​](https://docs.mapsindoors.com/changelogs/components#added-6) <a href="#added-6" id="added-6"></a>

**mi-dropdown**: Now has support for showing items with icons in the header when single selecting.

### \[11.10.1] - 2022-01-18[​](https://docs.mapsindoors.com/changelogs/components#11101---2022-01-18) <a href="#11101---2022-01-18" id="11101---2022-01-18"></a>

#### Fixed[​](https://docs.mapsindoors.com/changelogs/components#fixed-8) <a href="#fixed-8" id="fixed-8"></a>

* **mi-data-table**: It's now possible to select rows when adding data to the table by setting the tables `selected` property.

### \[11.10.0] - 2022-01-14[​](https://docs.mapsindoors.com/changelogs/components#11100---2022-01-14) <a href="#11100---2022-01-14" id="11100---2022-01-14"></a>

#### Added[​](https://docs.mapsindoors.com/changelogs/components#added-7) <a href="#added-7" id="added-7"></a>

* **mi-dropdown**: A `button-icon` part attribute to allow external styling of the icon img element.

### \[11.9.0] - 2021-12-16[​](https://docs.mapsindoors.com/changelogs/components#1190---2021-12-16) <a href="#1190---2021-12-16" id="1190---2021-12-16"></a>

#### Added[​](https://docs.mapsindoors.com/changelogs/components#added-8) <a href="#added-8" id="added-8"></a>

* **mi-dropdown**: Now has the option to display user-specified text when hovering an `mi-dropdown-item` by setting the `title` attribute.

#### Fixed[​](https://docs.mapsindoors.com/changelogs/components#fixed-9) <a href="#fixed-9" id="fixed-9"></a>

* **mi-dropdown**: Tabbing to the clear button and pressing Enter would clear the input field and select the highlighted item instead of just clearing the input field.
* **mi-dropdown**: `mi-dropdown-item`s with icons were not filterable.

### \[11.8.1] - 2021-12-01[​](https://docs.mapsindoors.com/changelogs/components#1181---2021-12-01) <a href="#1181---2021-12-01" id="1181---2021-12-01"></a>

#### Changed[​](https://docs.mapsindoors.com/changelogs/components#changed-2) <a href="#changed-2" id="changed-2"></a>

* **mi-notification**: Enums and interfaces is now exposed.

### \[11.8.0] - 2021-11-17[​](https://docs.mapsindoors.com/changelogs/components#1180---2021-11-17) <a href="#1180---2021-11-17" id="1180---2021-11-17"></a>

#### Added[​](https://docs.mapsindoors.com/changelogs/components#added-9) <a href="#added-9" id="added-9"></a>

* **mi-dropdown**: The items within the content window now truncate long strings, and hovering over items will now show the full text.

### \[11.7.1] - 2021-11-10[​](https://docs.mapsindoors.com/changelogs/components#1171---2021-11-10) <a href="#1171---2021-11-10" id="1171---2021-11-10"></a>

#### Fixed[​](https://docs.mapsindoors.com/changelogs/components#fixed-10) <a href="#fixed-10" id="fixed-10"></a>

* **mi-dropdown**: Fuzzy search now correctly shows the items that match the input query the most.

### \[11.7.0] - 2021-11-05[​](https://docs.mapsindoors.com/changelogs/components#1170---2021-11-05) <a href="#1170---2021-11-05" id="1170---2021-11-05"></a>

#### Fixed[​](https://docs.mapsindoors.com/changelogs/components#fixed-11) <a href="#fixed-11" id="fixed-11"></a>

* **mi-dropdown**: The clear button in the input field is now hidden and untabable when there's no input string.

### \[11.6.2] - 2021-11-02[​](https://docs.mapsindoors.com/changelogs/components#1162---2021-11-02) <a href="#1162---2021-11-02" id="1162---2021-11-02"></a>

#### Fixed[​](https://docs.mapsindoors.com/changelogs/components#fixed-12) <a href="#fixed-12" id="fixed-12"></a>

* **mi-dropdown**: Searching for items now uses a score to show the items that match the search query.

### \[11.6.1] - 2021-11-02[​](https://docs.mapsindoors.com/changelogs/components#1161---2021-11-02) <a href="#1161---2021-11-02" id="1161---2021-11-02"></a>

#### Fixed[​](https://docs.mapsindoors.com/changelogs/components#fixed-13) <a href="#fixed-13" id="fixed-13"></a>

* **mi-column**: Styling issue that would cause columns with a fixed width to resize when changing the table width.

### \[11.6.0] - 2021-10-29[​](https://docs.mapsindoors.com/changelogs/components#1160---2021-10-29) <a href="#1160---2021-10-29" id="1160---2021-10-29"></a>

#### Added[​](https://docs.mapsindoors.com/changelogs/components#added-10) <a href="#added-10" id="added-10"></a>

* **mi-column**: `alignContent` attribute for setting the alignment of the column's content.
* **mi-column**: `width` attribute for setting a fixed width of the column.

#### Fixed[​](https://docs.mapsindoors.com/changelogs/components#fixed-14) <a href="#fixed-14" id="fixed-14"></a>

* **mi-data-table**: Styling issue for none-sortable columns that caused extra padding to be applied.

### \[11.5.2] - 2021-10-28[​](https://docs.mapsindoors.com/changelogs/components#1152---2021-10-28) <a href="#1152---2021-10-28" id="1152---2021-10-28"></a>

#### Fixed[​](https://docs.mapsindoors.com/changelogs/components#fixed-15) <a href="#fixed-15" id="fixed-15"></a>

* **mi-dropdown**: Using the cursor to select an item was not possible.

### \[11.5.1] - 2021-10-28[​](https://docs.mapsindoors.com/changelogs/components#1151---2021-10-28) <a href="#1151---2021-10-28" id="1151---2021-10-28"></a>

#### Fixed[​](https://docs.mapsindoors.com/changelogs/components#fixed-16) <a href="#fixed-16" id="fixed-16"></a>

* **mi-dropdown**: Now shows the selected item again.

### \[11.5.0] - 2021-10-28[​](https://docs.mapsindoors.com/changelogs/components#1150---2021-10-28) <a href="#1150---2021-10-28" id="1150---2021-10-28"></a>

#### Added[​](https://docs.mapsindoors.com/changelogs/components#added-11) <a href="#added-11" id="added-11"></a>

* **mi-dropdown**: Now supports navigating and selecting items using the keyboard.

### \[11.4.2] - 2021-10-18[​](https://docs.mapsindoors.com/changelogs/components#1142---2021-10-18) <a href="#1142---2021-10-18" id="1142---2021-10-18"></a>

#### Fixed[​](https://docs.mapsindoors.com/changelogs/components#fixed-17) <a href="#fixed-17" id="fixed-17"></a>

* **mi-dropdown**: The dropdown filtering options now got a fixed position.

### \[11.4.1] - 2021-10-14[​](https://docs.mapsindoors.com/changelogs/components#1141---2021-10-14) <a href="#1141---2021-10-14" id="1141---2021-10-14"></a>

#### Fixed[​](https://docs.mapsindoors.com/changelogs/components#fixed-18) <a href="#fixed-18" id="fixed-18"></a>

* **mi-dropdown**: The spacing between checkbox and icon is now `12px`.
* **mi-dropdown**: The spacing between the icon and the label is now `8px`.

### \[11.4.0] - 2021-10-14[​](https://docs.mapsindoors.com/changelogs/components#1140---2021-10-14) <a href="#1140---2021-10-14" id="1140---2021-10-14"></a>

#### Added[​](https://docs.mapsindoors.com/changelogs/components#added-12) <a href="#added-12" id="added-12"></a>

* **mi-dropdown**: Now has support for adding icons to items. `<mi-dropdown-item value="foo"><img src="example.com/image.png />bar</mi-dropdown-item>`.
* **mi-column**: Now has an `sort` attribute for pre-sorting the table by that column. `sort="asc|desc"`
* **mi-column**: The `sortable` attribute can now take an optional value `"date"` to sort the specific column as dates. `sortable="date"`.

### \[11.3.0] - 2021-10-05[​](https://docs.mapsindoors.com/changelogs/components#1130---2021-10-05) <a href="#1130---2021-10-05" id="1130---2021-10-05"></a>

#### Added[​](https://docs.mapsindoors.com/changelogs/components#added-13) <a href="#added-13" id="added-13"></a>

* **mi-dropdown**: Now has an `icon` property, which accepts an image source.
* **mi-dropdown**: Now has an `icon-alt` property, which sets the alternative text for an image.

### \[11.2.0] - 2021-09-16[​](https://docs.mapsindoors.com/changelogs/components#1120---2021-09-16) <a href="#1120---2021-09-16" id="1120---2021-09-16"></a>

#### Added[​](https://docs.mapsindoors.com/changelogs/components#added-14) <a href="#added-14" id="added-14"></a>

* **mi-route-instructions**: Support for three new highways that can occur in a route: `ladder`, `wheelchairramp` and `wheelcharlift`.
* **mi-icon**: Icons for `ladder`, `wheelchair-ramp` and `wheelchair-lift`.

### \[11.1.0] - 2021-09-15[​](https://docs.mapsindoors.com/changelogs/components#1110---2021-09-15) <a href="#1110---2021-09-15" id="1110---2021-09-15"></a>

#### Changed[​](https://docs.mapsindoors.com/changelogs/components#changed-3) <a href="#changed-3" id="changed-3"></a>

* **mi-list-item-location** and **mi-list-item-category**: Images hosted on `image.mapsindoors.com` are now requested with query parameters for getting the image in the displayed size.

### \[11.0.0] - 2021-09-08[​](https://docs.mapsindoors.com/changelogs/components#1100---2021-09-08) <a href="#1100---2021-09-08" id="1100---2021-09-08"></a>

#### Added[​](https://docs.mapsindoors.com/changelogs/components#added-15) <a href="#added-15" id="added-15"></a>

* **mi-data-table**: Now has a `sticky-header` property, which can be used to make the table header non-sticky.

### \[10.12.0] - 2021-09-06[​](https://docs.mapsindoors.com/changelogs/components#10120---2021-09-06) <a href="#10120---2021-09-06" id="10120---2021-09-06"></a>

#### Added[​](https://docs.mapsindoors.com/changelogs/components#added-16) <a href="#added-16" id="added-16"></a>

* **mi-tabs**: Now has a `bordered` property, which can be set to add a border surrounding the content view.

### \[10.11.0] - 2021-09-01[​](https://docs.mapsindoors.com/changelogs/components#10110---2021-09-01) <a href="#10110---2021-09-01" id="10110---2021-09-01"></a>

#### Added[​](https://docs.mapsindoors.com/changelogs/components#added-17) <a href="#added-17" id="added-17"></a>

* **mi-dropdown**: Now displays a message when no results can be found based on the search query.
* **mi-dropdown**: Now disables the filter select buttons when there's nothing to select.
* **mi-dropdown**: Now performs filtering based on a fuzzy search algorithm.

#### Fixed[​](https://docs.mapsindoors.com/changelogs/components#fixed-19) <a href="#fixed-19" id="fixed-19"></a>

* mi-dropdown: Filtering within the component now works as expected.

### \[10.10.0] - 2021-08-26[​](https://docs.mapsindoors.com/changelogs/components#10100---2021-08-26) <a href="#10100---2021-08-26" id="10100---2021-08-26"></a>

#### Added[​](https://docs.mapsindoors.com/changelogs/components#added-18) <a href="#added-18" id="added-18"></a>

* **mi-data-table**: `emptyPageHeader` and `emptyPageSubheader` properties added which can be used to set the header and subheader that is being presented when the table is empty.

### \[10.9.0] - 2021-08-25[​](https://docs.mapsindoors.com/changelogs/components#1090---2021-08-25) <a href="#1090---2021-08-25" id="1090---2021-08-25"></a>

#### Added[​](https://docs.mapsindoors.com/changelogs/components#added-19) <a href="#added-19" id="added-19"></a>

* **mi-dropdown**: `itemsOrder` property added to control the sorting of the dropdown options.

### \[10.8.0] - 2021-08-20[​](https://docs.mapsindoors.com/changelogs/components#1080---2021-08-20) <a href="#1080---2021-08-20" id="1080---2021-08-20"></a>

#### Fixed[​](https://docs.mapsindoors.com/changelogs/components#fixed-20) <a href="#fixed-20" id="fixed-20"></a>

* **mi-scroll-buttons**: The state of the up and down buttons now disable or enable correctly when the scrollbar reaches the top or bottom.

### \[10.7.0] - 2021-08-16[​](https://docs.mapsindoors.com/changelogs/components#1070---2021-08-16) <a href="#1070---2021-08-16" id="1070---2021-08-16"></a>

#### Added[​](https://docs.mapsindoors.com/changelogs/components#added-20) <a href="#added-20" id="added-20"></a>

* **mi-dropdown**: Option to style icon on the right-hand side of the dropdown component.

#### Fixed[​](https://docs.mapsindoors.com/changelogs/components#fixed-21) <a href="#fixed-21" id="fixed-21"></a>

* **mi-dropdown**: Dropdown content previously had no max height to prevent it from taking more space than available.

### \[10.6.0] - 2021-08-11[​](https://docs.mapsindoors.com/changelogs/components#1060---2021-08-11) <a href="#1060---2021-08-11" id="1060---2021-08-11"></a>

#### Fixed[​](https://docs.mapsindoors.com/changelogs/components#fixed-22) <a href="#fixed-22" id="fixed-22"></a>

* **mi-dropdown**: Collapsing button now has a pre-defined height.

#### Added[​](https://docs.mapsindoors.com/changelogs/components#added-21) <a href="#added-21" id="added-21"></a>

* **mi-dropdown**: Disabled state for the button when no textual content is available.

### \[10.5.1] - 2021-08-03[​](https://docs.mapsindoors.com/changelogs/components#1051---2021-08-03) <a href="#1051---2021-08-03" id="1051---2021-08-03"></a>

#### Fixed[​](https://docs.mapsindoors.com/changelogs/components#fixed-23) <a href="#fixed-23" id="fixed-23"></a>

* **mi-dropdown**: The button will now display the name of the first `mi-dropdown-item` as its content instead of being empty.

### \[10.5.0] - 2021-08-03[​](https://docs.mapsindoors.com/changelogs/components#1050---2021-08-03) <a href="#1050---2021-08-03" id="1050---2021-08-03"></a>

#### Added[​](https://docs.mapsindoors.com/changelogs/components#added-22) <a href="#added-22" id="added-22"></a>

* **mi-dropdown**: Option to style the textual content inside the button using document-level CSS.

### \[10.4.0] - 2021-07-21[​](https://docs.mapsindoors.com/changelogs/components#1040---2021-07-21) <a href="#1040---2021-07-21" id="1040---2021-07-21"></a>

#### Added[​](https://docs.mapsindoors.com/changelogs/components#added-23) <a href="#added-23" id="added-23"></a>

* **mi-dropdown**: The button can now be styled using document-level CSS.

### \[10.3.2] - 2021-07-16[​](https://docs.mapsindoors.com/changelogs/components#1032---2021-07-16) <a href="#1032---2021-07-16" id="1032---2021-07-16"></a>

#### Fixed[​](https://docs.mapsindoors.com/changelogs/components#fixed-24) <a href="#fixed-24" id="fixed-24"></a>

* **mi-dropdown**: The `mi-dropdown-item`'s wasn't rendered when the `items` attribute was an empty array.

### \[10.3.1] - 2021-07-15[​](https://docs.mapsindoors.com/changelogs/components#1031---2021-07-15) <a href="#1031---2021-07-15" id="1031---2021-07-15"></a>

#### Fixed[​](https://docs.mapsindoors.com/changelogs/components#fixed-25) <a href="#fixed-25" id="fixed-25"></a>

* **mi-dropdown**: The `mi-dropdown` component didn't render the `mi-dropdown-item` elements when set before the first render.

### \[10.3.0] - 2021-07-15[​](https://docs.mapsindoors.com/changelogs/components#1030---2021-07-15) <a href="#1030---2021-07-15" id="1030---2021-07-15"></a>

#### Added[​](https://docs.mapsindoors.com/changelogs/components#added-24) <a href="#added-24" id="added-24"></a>

* **mi-data-table**: The `selectionChanged` event has been added. If the table is selectable this event will fire when the selection changes.

### \[10.2.0] - 2021-07-15[​](https://docs.mapsindoors.com/changelogs/components#1020---2021-07-15) <a href="#1020---2021-07-15" id="1020---2021-07-15"></a>

#### Added[​](https://docs.mapsindoors.com/changelogs/components#added-25) <a href="#added-25" id="added-25"></a>

* **mi-column**: The `fit-content` attribute has been added. When present the column width will be fitted to the content.

### \[10.1.0] - 2021-07-14[​](https://docs.mapsindoors.com/changelogs/components#1010---2021-07-14) <a href="#1010---2021-07-14" id="1010---2021-07-14"></a>

#### Added[​](https://docs.mapsindoors.com/changelogs/components#added-26) <a href="#added-26" id="added-26"></a>

* **mi-data-table**: The `selectable` attribute has been added. When present on the data-table the first column will be rendered as checkboxes.

### \[10.0.0] - 2021-07-07[​](https://docs.mapsindoors.com/changelogs/components#1000---2021-07-07) <a href="#1000---2021-07-07" id="1000---2021-07-07"></a>

#### Added[​](https://docs.mapsindoors.com/changelogs/components#added-27) <a href="#added-27" id="added-27"></a>

* **mi-dropdown**: Documentation added.

#### Changed[​](https://docs.mapsindoors.com/changelogs/components#changed-4) <a href="#changed-4" id="changed-4"></a>

* **mi-dropdown**: Cleanup of component including look and feel.
* **mi-dropdown**: `change` event now emits selected items instead of the component itself.

### \[9.2.0] - 2021-06-17[​](https://docs.mapsindoors.com/changelogs/components#920---2021-06-17) <a href="#920---2021-06-17" id="920---2021-06-17"></a>

#### Added[​](https://docs.mapsindoors.com/changelogs/components#added-28) <a href="#added-28" id="added-28"></a>

* **mi-data-table**: Emit `clicked` event when clickin on elements within table cells.
* **mi-column**: Make it possible to use bindings for boolean HTML attributes within table cells.
* **mi-column**: Make it possible to style elements within table cells with [MIDT helper classes](https://github.com/MapsIndoors/midt) and [MapsIndoors CSS classes](https://github.com/MapsIndoors/css).
* **mi-map-mapbox**: New attribute for setting max pitch (defaults to 60).

#### Changed[​](https://docs.mapsindoors.com/changelogs/components#changed-5) <a href="#changed-5" id="changed-5"></a>

* **mi-map-mapbox**: Upgrade to use Mapbox GL JS v2.3.0.

### \[9.1.0] - 2021-06-10[​](https://docs.mapsindoors.com/changelogs/components#910---2021-06-10) <a href="#910---2021-06-10" id="910---2021-06-10"></a>

#### Changed[​](https://docs.mapsindoors.com/changelogs/components#changed-6) <a href="#changed-6" id="changed-6"></a>

* From previously inserting a script tag manually to now using the [Google Maps JS API Loader](https://www.npmjs.com/package/@googlemaps/js-api-loader) npm package.

### \[9.0.2] - 2021-05-06[​](https://docs.mapsindoors.com/changelogs/components#902---2021-05-06) <a href="#902---2021-05-06" id="902---2021-05-06"></a>

#### Fixed[​](https://docs.mapsindoors.com/changelogs/components#fixed-26) <a href="#fixed-26" id="fixed-26"></a>

* Fixed a bug where moving across buildings would show incorrect step heading.

### \[9.0.1] - 2021-04-29[​](https://docs.mapsindoors.com/changelogs/components#901---2021-04-29) <a href="#901---2021-04-29" id="901---2021-04-29"></a>

#### Changed[​](https://docs.mapsindoors.com/changelogs/components#changed-7) <a href="#changed-7" id="changed-7"></a>

* Updated the version used of @mapsindoors/typescript-interfaces.

### \[9.0.0] - 2021-04-29[​](https://docs.mapsindoors.com/changelogs/components#900---2021-04-29) <a href="#900---2021-04-29" id="900---2021-04-29"></a>

#### Changed[​](https://docs.mapsindoors.com/changelogs/components#changed-8) <a href="#changed-8" id="changed-8"></a>

* Deprecate the following interfaces: Anchor, Building, BuildingInfo, LatLng, DisplayRule, Field, Location, Venue in favor of using the TypeScript interface library @mapsindoors/typescript-interfaces.

### \[8.2.3] - 2021-04-29[​](https://docs.mapsindoors.com/changelogs/components#823---2021-04-29) <a href="#823---2021-04-29" id="823---2021-04-29"></a>

#### Fixed[​](https://docs.mapsindoors.com/changelogs/components#fixed-27) <a href="#fixed-27" id="fixed-27"></a>

* **mi-map-googlemaps**: Reduce memory leaks when removing component.
* **mi-map-mapbox**: Reduce memory leaks when removing component.

### \[8.2.2] - 2021-04-23[​](https://docs.mapsindoors.com/changelogs/components#822---2021-04-23) <a href="#822---2021-04-23" id="822---2021-04-23"></a>

#### Added[​](https://docs.mapsindoors.com/changelogs/components#added-29) <a href="#added-29" id="added-29"></a>

* **mi-scroll-buttons**: Documentation added.

### \[8.2.1] - 2021-04-20[​](https://docs.mapsindoors.com/changelogs/components#821---2021-04-20) <a href="#821---2021-04-20" id="821---2021-04-20"></a>

#### Fixed[​](https://docs.mapsindoors.com/changelogs/components#fixed-28) <a href="#fixed-28" id="fixed-28"></a>

* **mi-route-instructions-step** Replaced the empty circle with the steps action icon. Now showing the steps instruction when available (defaults to action for travel mode).
* **mi-map-mapbox** Removed default maxZoom value of 21. This is handled in the SDK.
* Upgrade to use the latest MapsIndoors JavaScript SDK (v4.7.0) with various bugfixes.
* **mi-share-sms**: Property name changed from `inputPlaceholder` to `input-placeholder`.

### \[8.2.0] - 2021-02-23[​](https://docs.mapsindoors.com/changelogs/components#820---2021-02-23) <a href="#820---2021-02-23" id="820---2021-02-23"></a>

#### Added[​](https://docs.mapsindoors.com/changelogs/components#added-30) <a href="#added-30" id="added-30"></a>

* **mi-list-item-location**: Added properties `iconBadge` and `iconBadgeValue` which can be used to add a badge to the icon.

#### Changed[​](https://docs.mapsindoors.com/changelogs/components#changed-9) <a href="#changed-9" id="changed-9"></a>

* **mi-share-sms**: Documentation updated.
* **mi-location-info**: Documentation updated.
* **mi-step-switcher**: Documentation updated.

### \[8.1.0] - 2021-02-15[​](https://docs.mapsindoors.com/changelogs/components#810---2021-02-15) <a href="#810---2021-02-15" id="810---2021-02-15"></a>

#### Added[​](https://docs.mapsindoors.com/changelogs/components#added-31) <a href="#added-31" id="added-31"></a>

* mi-map-googlemaps: `language` property added to set the language of the component. This property is not reactive.
* mi-map-mapbox: `language` property added to set the language of the component. This property is not reactive.

#### Fixed[​](https://docs.mapsindoors.com/changelogs/components#fixed-29) <a href="#fixed-29" id="fixed-29"></a>

* mi-map-googlemaps: Now checks if an instance of Google Maps API is initialized or not.
* mi-map-googlemaps: Now checks if an instance of the Mapbox API is initialized or not.

### \[8.0.0] - 2021-02-08[​](https://docs.mapsindoors.com/changelogs/components#800---2021-02-08) <a href="#800---2021-02-08" id="800---2021-02-08"></a>

#### Added[​](https://docs.mapsindoors.com/changelogs/components#added-32) <a href="#added-32" id="added-32"></a>

* **mi-search**: New custom `shortInput` event.
* **mi-map-googlemaps**: `getDirectionsServiceInstance` method added to expose `DirectionsService` instance.
* **mi-map-googlemaps**: `getDirectionsRendererInstance` method added to expose `DirectionsRenderer` instance.
* **mi-map-mapbox**: `getDirectionsServiceInstance` method added to expose `DirectionsService` instance.
* **mi-map-mapbox**: `getDirectionsRendererInstance` method added to expose `DirectionsRenderer` instance.

#### Changed[​](https://docs.mapsindoors.com/changelogs/components#changed-10) <a href="#changed-10" id="changed-10"></a>

* **mi-map-googlemaps**: `showRoute`, `setRoute`, `clearRoute`, `nextRouteLeg`, `previousRouteLeg`, `setRouteLegIndex`, and `getRoute` methods is deprecated in favor for new `getDirectionsRendererInstance` and `getDirectionsRendererInstance` methods.
* **mi-map-googlemaps**: Component updated to latests SDK release (V. 4.5.0).
* **mi-map-mapbox**: `showRoute`, `setRoute`, `clearRoute`, `nextRouteLeg`, `previousRouteLeg`, `setRouteLegIndex`, and `getRoute` methods is deprecated in favor for new `getDirectionsRendererInstance` and `getDirectionsRendererInstance` methods.
* **mi-map-mapbox**: Component updated to latests SDK release (V. 4.5.0).
* **RouteParams interface**: Deprecation of `RouteParams` interface.

### \[7.3.2] - 2021-02-03[​](https://docs.mapsindoors.com/changelogs/components#732---2021-02-03) <a href="#732---2021-02-03" id="732---2021-02-03"></a>

#### Changed[​](https://docs.mapsindoors.com/changelogs/components#changed-11) <a href="#changed-11" id="changed-11"></a>

* **mi-map-googlemaps**: Default value for `strokeWeight` at the `polygonHighlightOptions` property is changed from 1 to 2.
* **mi-map-mapbox**: Default value for `strokeWeight` at the `polygonHighlightOptions` property is changed from 1 to 2.

### \[7.3.1] - 2021-02-02[​](https://docs.mapsindoors.com/changelogs/components#731---2021-02-02) <a href="#731---2021-02-02" id="731---2021-02-02"></a>

#### Fixed[​](https://docs.mapsindoors.com/changelogs/components#fixed-30) <a href="#fixed-30" id="fixed-30"></a>

* **mi-location-booking**: Remove hardcoded participant list for the bookings.

### \[7.3.0] - 2021-01-29[​](https://docs.mapsindoors.com/changelogs/components#730---2021-01-29) <a href="#730---2021-01-29" id="730---2021-01-29"></a>

#### Added[​](https://docs.mapsindoors.com/changelogs/components#added-33) <a href="#added-33" id="added-33"></a>

* **mi-location-booking**: New component that can show and perform location bookings.

### \[7.2.3] - 2021-01-28[​](https://docs.mapsindoors.com/changelogs/components#723---2021-01-28) <a href="#723---2021-01-28" id="723---2021-01-28"></a>

#### Changed[​](https://docs.mapsindoors.com/changelogs/components#changed-12) <a href="#changed-12" id="changed-12"></a>

* **mi-route-instructions**: Documentation updated.
* **mi-route-instructions-maneuver**: Documentation updated.
* **mi-map-googlemaps**: Component updated to latests SDK release (V. 4.4.0).
* **mi-map-mapbox**: Component updated to latests SDK release (V. 4.4.0).

### \[7.2.2] - 2021-01-20[​](https://docs.mapsindoors.com/changelogs/components#722---2021-01-20) <a href="#722---2021-01-20" id="722---2021-01-20"></a>

#### Added[​](https://docs.mapsindoors.com/changelogs/components#added-34) <a href="#added-34" id="added-34"></a>

* **Field Interface**: Export `Field` interface used for `fields` property at `Location` objects.

### \[7.2.1] - 2021-01-14[​](https://docs.mapsindoors.com/changelogs/components#721---2021-01-14) <a href="#721---2021-01-14" id="721---2021-01-14"></a>

#### Fixed[​](https://docs.mapsindoors.com/changelogs/components#fixed-31) <a href="#fixed-31" id="fixed-31"></a>

* **mi-route-instructions**: The step toggle didn't show the pointer cursor on hover if the step was active.

### \[7.2.0] - 2021-01-14[​](https://docs.mapsindoors.com/changelogs/components#720---2021-01-14) <a href="#720---2021-01-14" id="720---2021-01-14"></a>

#### Added[​](https://docs.mapsindoors.com/changelogs/components#added-35) <a href="#added-35" id="added-35"></a>

* **mi-route-instructions**: Add `activeStep` attribute for visually highlighting of current step.
* **mi-route-instructions**: Add `step` and `active` part attributes for external styling of step element.

### \[7.1.3] - 2021-01-13[​](https://docs.mapsindoors.com/changelogs/components#713---2021-01-13) <a href="#713---2021-01-13" id="713---2021-01-13"></a>

#### Changed[​](https://docs.mapsindoors.com/changelogs/components#changed-13) <a href="#changed-13" id="changed-13"></a>

* **mi-map-googlemaps**: Documentation updated.
* **mi-map-mapbox**: Documentation updated.
* **mi-route-instructions-step**: Documentation updated.
* **mi-distance**: Documentation updated.
* **mi-icon**: Documentation updated. Note added regards component not being compatible with IE11.

#### Fixed[​](https://docs.mapsindoors.com/changelogs/components#fixed-32) <a href="#fixed-32" id="fixed-32"></a>

* **mi-route-instructions**: The translations for "venue" and "building" was missing and can now be added to the `translations` attribute.
* **mi-route-instructions-step**: The translations for "venue" and "building" was missing and can now be added to the `translations` attribute.

### \[7.1.2] - 2021-01-06[​](https://docs.mapsindoors.com/changelogs/components#712---2021-01-06) <a href="#712---2021-01-06" id="712---2021-01-06"></a>

#### Fixed[​](https://docs.mapsindoors.com/changelogs/components#fixed-33) <a href="#fixed-33" id="fixed-33"></a>

* **mi-map-googlemaps**: Add missing protocol to URL used for googleMaps API script tag.

### \[7.1.1] - 2020-12-15[​](https://docs.mapsindoors.com/changelogs/components#711---2020-12-15) <a href="#711---2020-12-15" id="711---2020-12-15"></a>

#### Fixed[​](https://docs.mapsindoors.com/changelogs/components#fixed-34) <a href="#fixed-34" id="fixed-34"></a>

* **mi-route-instructions-maneuver**: Set `instructions` property as default maneuver and fallback to the `maneuver` property.

### \[7.1.0] - 2020-12-14[​](https://docs.mapsindoors.com/changelogs/components#710---2020-12-14) <a href="#710---2020-12-14" id="710---2020-12-14"></a>

#### Added[​](https://docs.mapsindoors.com/changelogs/components#added-36) <a href="#added-36" id="added-36"></a>

* **mi-route-instructions**: `originLocation` and `originName` attributes added.

#### Fixed[​](https://docs.mapsindoors.com/changelogs/components#fixed-35) <a href="#fixed-35" id="fixed-35"></a>

* **mi-route-instructions-step**: Header saying "Leave" was presented for outdoor to outdoor steps.

### \[7.0.0] - 2020-12-11[​](https://docs.mapsindoors.com/changelogs/components#700---2020-12-11) <a href="#700---2020-12-11" id="700---2020-12-11"></a>

#### Changed[​](https://docs.mapsindoors.com/changelogs/components#changed-14) <a href="#changed-14" id="changed-14"></a>

* **mi-route-instructions**: Add a `hideIndoorSubsteps` attribute which can be used to control the visibility of the indoor substeps at the `<mi-route-instructions-step>` element.
* **mi-route-instructions-step**: Add a `hideIndoorSubsteps` attribute which can be used to control the visibility of the indoor substeps.
* **mi-route-instructions-maneuver**: Fallback to `instructions` property value if the `maneuver` property is empty.

#### Fixed[​](https://docs.mapsindoors.com/changelogs/components#fixed-36) <a href="#fixed-36" id="fixed-36"></a>

* **mi-route-instructions-step**: A solid box was rendered instead of a maneuver icon when the `maneuver` property was empty.

### \[6.0.4] - 2020-12-07[​](https://docs.mapsindoors.com/changelogs/components#604---2020-12-07) <a href="#604---2020-12-07" id="604---2020-12-07"></a>

#### Added[​](https://docs.mapsindoors.com/changelogs/components#added-37) <a href="#added-37" id="added-37"></a>

* **mi-route-instructions**: Handles for styleable elements in shadow tree.
* **mi-route-instructions-step**: Handles for styleable elements in shadow tree.
* **mi-route-instructions-maneuver**: Handles for styleable elements in shadow tree.

#### Changed[​](https://docs.mapsindoors.com/changelogs/components#changed-15) <a href="#changed-15" id="changed-15"></a>

* **mi-step-switcher**: Documentation simplified for styling handles.

### \[6.0.3] - 2020-12-03[​](https://docs.mapsindoors.com/changelogs/components#603---2020-12-03) <a href="#603---2020-12-03" id="603---2020-12-03"></a>

#### Fixed[​](https://docs.mapsindoors.com/changelogs/components#fixed-37) <a href="#fixed-37" id="fixed-37"></a>

* **mi-route-instructions-step**: Transit destination wasn't presented.

### \[6.0.2] - 2020-12-03[​](https://docs.mapsindoors.com/changelogs/components#602---2020-12-03) <a href="#602---2020-12-03" id="602---2020-12-03"></a>

#### Changed[​](https://docs.mapsindoors.com/changelogs/components#changed-16) <a href="#changed-16" id="changed-16"></a>

* **mi-route-instructions-heading**: Documentation updated.
* **mi-route-instructions**: 'arrive' and 'take' translation strings is deprecated and not longer needed.

#### Fixed[​](https://docs.mapsindoors.com/changelogs/components#fixed-38) <a href="#fixed-38" id="fixed-38"></a>

* **mi-route-instructions-step**: Transit destination wasn't presented.

### \[6.0.1] - 2020-12-02[​](https://docs.mapsindoors.com/changelogs/components#601---2020-12-02) <a href="#601---2020-12-02" id="601---2020-12-02"></a>

#### Added[​](https://docs.mapsindoors.com/changelogs/components#added-38) <a href="#added-38" id="added-38"></a>

* **mi-step-switcher**: Handles for styleable elements in shadow tree.
* **mi-route-instructions**: IE11 support.
* **mi-route-instructions-step**: IE11 support.

### \[6.0.0] - 2020-11-30[​](https://docs.mapsindoors.com/changelogs/components#600---2020-11-30) <a href="#600---2020-11-30" id="600---2020-11-30"></a>

#### Added[​](https://docs.mapsindoors.com/changelogs/components#added-39) <a href="#added-39" id="added-39"></a>

* **mi-map-googlemaps**: New map component using Google Maps as map provider and SDK V. 4.1.1.
* **mi-map-mapbox**: Position Control support added.
* **mi-route-instructions-step**: Added missing rendering of transit step.
* **mi-spinner**: Documentation updated.
* **mi-notification**: Documentation updated.
* **mi-map-mapbox**: Documentation added.

#### Changed[​](https://docs.mapsindoors.com/changelogs/components#changed-17) <a href="#changed-17" id="changed-17"></a>

* **mi-map**: Component deprecated in favor for new `<mi-map-googlemaps>` component.
* **mi-map-mapbox**: The `mapsIndoors` instance is removed from the payload of the `mapsIndoorsReady` event in favor for new `getMapsIndoorsInstance` method.
* **mi-map-mapbox**: Deprecated the following methods: `panTo`, `getBounds`, `fitBounds`, `setDisplayRule`, `setVenue`, `fitVenue`, `filterLocations`, and `clearLocationFilter` in favor for the `getMapInstance` and `getMapsIndoorsInstance` methods.

### \[5.0.8] - 2020-11-24[​](https://docs.mapsindoors.com/changelogs/components#508---2020-11-24) <a href="#508---2020-11-24" id="508---2020-11-24"></a>

#### Fixed[​](https://docs.mapsindoors.com/changelogs/components#fixed-39) <a href="#fixed-39" id="fixed-39"></a>

* **mi-route-instructions**: The action name reflects now the proper Enter/Exit step.

#### Added[​](https://docs.mapsindoors.com/changelogs/components#added-40) <a href="#added-40" id="added-40"></a>

* **mi-route-instructions**: Adds Building or Venue name to step heading.

### \[5.0.7] - 2020-11-20[​](https://docs.mapsindoors.com/changelogs/components#507---2020-11-20) <a href="#507---2020-11-20" id="507---2020-11-20"></a>

#### Added[​](https://docs.mapsindoors.com/changelogs/components#added-41) <a href="#added-41" id="added-41"></a>

* **mi-search**: Reflect namespace changes introduced in SDK 4.
* **mi-share-sms**: Reflect namespace changes introduced in SDK 4.

### \[5.0.6] - 2020-11-20[​](https://docs.mapsindoors.com/changelogs/components#506---2020-11-20) <a href="#506---2020-11-20" id="506---2020-11-20"></a>

#### Added[​](https://docs.mapsindoors.com/changelogs/components#added-42) <a href="#added-42" id="added-42"></a>

* **mi-route-instructions**: Add default translations for `mi-time` component.
* **mi-time**: Clean up handling of `translations` attribute.

### \[5.0.5] - 2020-11-20[​](https://docs.mapsindoors.com/changelogs/components#505---2020-11-20) <a href="#505---2020-11-20" id="505---2020-11-20"></a>

#### Changed[​](https://docs.mapsindoors.com/changelogs/components#changed-18) <a href="#changed-18" id="changed-18"></a>

* **mi-map-mapbox**: Component updated to latests SDK release (V. 4.1.1).

### \[5.0.4] - 2020-11-19[​](https://docs.mapsindoors.com/changelogs/components#504---2020-11-19) <a href="#504---2020-11-19" id="504---2020-11-19"></a>

#### Added[​](https://docs.mapsindoors.com/changelogs/components#added-43) <a href="#added-43" id="added-43"></a>

* **mi-time**: `translations` attributes is added.
* **mi-keyboard**: Support added for `da-DK` browser language.

#### Changed[​](https://docs.mapsindoors.com/changelogs/components#changed-19) <a href="#changed-19" id="changed-19"></a>

* **mi-keyboard**: Documentation update.
* **mi-search**: The fixed height of the component is removed.
* **mi-search**: Documentation update.

### \[5.0.3] - 2020-11-18[​](https://docs.mapsindoors.com/changelogs/components#503---2020-11-18) <a href="#503---2020-11-18" id="503---2020-11-18"></a>

#### Added[​](https://docs.mapsindoors.com/changelogs/components#added-44) <a href="#added-44" id="added-44"></a>

* **mi-keyboard**: Documentation update with sample usage and working example.
* **mi-route-instructions**: Documentation update to describe the clicked event.

#### Fixed[​](https://docs.mapsindoors.com/changelogs/components#fixed-40) <a href="#fixed-40" id="fixed-40"></a>

* **mi-map-mapbox**: Component updated to latests SDK release (V. 4.1.0).
* **mi-route-instructions**: Unit property wasn't reflected in child components.

### \[5.0.2] - 2020-11-03[​](https://docs.mapsindoors.com/changelogs/components#502---2020-11-03) <a href="#502---2020-11-03" id="502---2020-11-03"></a>

#### Added[​](https://docs.mapsindoors.com/changelogs/components#added-45) <a href="#added-45" id="added-45"></a>

* **mi-icon**: Printer icon added.

### \[5.0.1] - 2020-10-30[​](https://docs.mapsindoors.com/changelogs/components#501---2020-10-30) <a href="#501---2020-10-30" id="501---2020-10-30"></a>

#### Added[​](https://docs.mapsindoors.com/changelogs/components#added-46) <a href="#added-46" id="added-46"></a>

* **mi-route-instructions**: New component displaying MapsIndoors route instructions.

### \[5.0.0] - 2020-10-28[​](https://docs.mapsindoors.com/changelogs/components#500---2020-10-28) <a href="#500---2020-10-28" id="500---2020-10-28"></a>

#### Changed[​](https://docs.mapsindoors.com/changelogs/components#changed-20) <a href="#changed-20" id="changed-20"></a>

* **mi-map-mapbox**: `highlightLocation` method is made public.
* **mi-map-mapbox**: `clearPolygonHighlight` method is renamed to `clearHighlightLocation`.

### \[3.2.2] - 2020-09-30[​](https://docs.mapsindoors.com/changelogs/components#322---2020-09-30) <a href="#322---2020-09-30" id="322---2020-09-30"></a>

#### Added[​](https://docs.mapsindoors.com/changelogs/components#added-47) <a href="#added-47" id="added-47"></a>

* **mi-map-mapbox**: New map component using Mapbox as map provider and the SDK v.4 alpha 7.

#### Changed[​](https://docs.mapsindoors.com/changelogs/components#changed-21) <a href="#changed-21" id="changed-21"></a>

* Initial load url for dev server is changed to components.html.

### \[3.2.1] - 2020-09-03[​](https://docs.mapsindoors.com/changelogs/components#321---2020-09-03) <a href="#321---2020-09-03" id="321---2020-09-03"></a>

#### Fixed[​](https://docs.mapsindoors.com/changelogs/components#fixed-41) <a href="#fixed-41" id="fixed-41"></a>

* **mi-search**: Fixed bug where clearing search field could cause similar subsequent search to fail.

### \[3.2.0] - 2020-09-02[​](https://docs.mapsindoors.com/changelogs/components#320---2020-09-02) <a href="#320---2020-09-02" id="320---2020-09-02"></a>

#### Fixed[​](https://docs.mapsindoors.com/changelogs/components#fixed-42) <a href="#fixed-42" id="fixed-42"></a>

* **mi-scroll-buttons**: Changed the styling of the button container.

#### Added[​](https://docs.mapsindoors.com/changelogs/components#added-48) <a href="#added-48" id="added-48"></a>

* **mi-search**: Expose a `mi-venue` prop to restrict MapsIndoors search results to a specific venue.

### \[3.1.0] - 2020-08-31[​](https://docs.mapsindoors.com/changelogs/components#310---2020-08-31) <a href="#310---2020-08-31" id="310---2020-08-31"></a>

#### Fixed[​](https://docs.mapsindoors.com/changelogs/components#fixed-43) <a href="#fixed-43" id="fixed-43"></a>

* **mi-search**: The clear button is now always visible in the right side on the input field no matter what browser is used.

#### Added[​](https://docs.mapsindoors.com/changelogs/components#added-49) <a href="#added-49" id="added-49"></a>

* **mi-map**: Location polygon is highlighted when clicked. The highlight can be cleared using the `clearPolygonHighlight` method, and styling of the highlight can be controlled with the `polygonHighlightOptions` prop.

### \[3.0.1] - 2020-08-14[​](https://docs.mapsindoors.com/changelogs/components#301---2020-08-14) <a href="#301---2020-08-14" id="301---2020-08-14"></a>

#### Fixed[​](https://docs.mapsindoors.com/changelogs/components#fixed-44) <a href="#fixed-44" id="fixed-44"></a>

* **mi-location-info**: details string wasn't returned when the venue and building was named the same.
* **mi-keyboard**: eventListener was attached multiple times.

#### Added[​](https://docs.mapsindoors.com/changelogs/components#added-50) <a href="#added-50" id="added-50"></a>

* **mi-keyboard**: custom `inputCleared` event listener.

### \[3.0.0] - 2020-08-13[​](https://docs.mapsindoors.com/changelogs/components#300---2020-08-13) <a href="#300---2020-08-13" id="300---2020-08-13"></a>

#### Fixed[​](https://docs.mapsindoors.com/changelogs/components#fixed-45) <a href="#fixed-45" id="fixed-45"></a>

* **mi-location-info**: details for outdoor locations wasn't shown.

#### Changed[​](https://docs.mapsindoors.com/changelogs/components#changed-22) <a href="#changed-22" id="changed-22"></a>

* **mi-keyboard**: some breaking changes was introduced for better control of when the keyboard should be visible. A layout and inputElement property is added.
* **mi-share-sms**: necessary changes to reflect changes made in mi-keyboard component.

### \[2.4.0] - 2020-08-07[​](https://docs.mapsindoors.com/changelogs/components#240---2020-08-07) <a href="#240---2020-08-07" id="240---2020-08-07"></a>

#### Added[​](https://docs.mapsindoors.com/changelogs/components#added-51) <a href="#added-51" id="added-51"></a>

* **New**: mi-share-sms component.

#### Fixed[​](https://docs.mapsindoors.com/changelogs/components#fixed-46) <a href="#fixed-46" id="fixed-46"></a>

* **mi-map**: didn't show any locations until the map had been idle.
* **mi-card**: had a unnecessary div tag which in some cases did cause trouble.

#### Changed[​](https://docs.mapsindoors.com/changelogs/components#changed-23) <a href="#changed-23" id="changed-23"></a>

* Upgrade to MapsIndoors JS SDK version 3.11.0.

### \[2.3.1] - 2020-07-29[​](https://docs.mapsindoors.com/changelogs/components#231---2020-07-29) <a href="#231---2020-07-29" id="231---2020-07-29"></a>

#### Fixed[​](https://docs.mapsindoors.com/changelogs/components#fixed-47) <a href="#fixed-47" id="fixed-47"></a>

* **mi-search**: fixed `mi-near` to provide correctly formatted data to the SDK.

### \[2.3.0][​](https://docs.mapsindoors.com/changelogs/components#230) <a href="#230" id="230"></a>

#### Added[​](https://docs.mapsindoors.com/changelogs/components#added-52) <a href="#added-52" id="added-52"></a>

* **mi-search**: added a componentRendered event.

### \[2.2.0][​](https://docs.mapsindoors.com/changelogs/components#220) <a href="#220" id="220"></a>

#### Added[​](https://docs.mapsindoors.com/changelogs/components#added-53) <a href="#added-53" id="added-53"></a>

* **mi-search**: added a idAttribute and dataAttributes attribute.

### \[2.1.2][​](https://docs.mapsindoors.com/changelogs/components#212) <a href="#212" id="212"></a>

#### Fixed[​](https://docs.mapsindoors.com/changelogs/components#fixed-48) <a href="#fixed-48" id="fixed-48"></a>

* **mi-keyboard**: added a "same element" check to handleFocusin method.

### \[2.1.1][​](https://docs.mapsindoors.com/changelogs/components#211) <a href="#211" id="211"></a>

#### Fixed[​](https://docs.mapsindoors.com/changelogs/components#fixed-49) <a href="#fixed-49" id="fixed-49"></a>

* **mi-step-switcher**: adjusted the vertical padding.

### \[2.1.0][​](https://docs.mapsindoors.com/changelogs/components#210) <a href="#210" id="210"></a>

#### Added[​](https://docs.mapsindoors.com/changelogs/components#added-54) <a href="#added-54" id="added-54"></a>

* **New**: mi-step-switcher component.

### \[2.0.0][​](https://docs.mapsindoors.com/changelogs/components#200) <a href="#200" id="200"></a>

#### Added[​](https://docs.mapsindoors.com/changelogs/components#added-55) <a href="#added-55" id="added-55"></a>

* Changelog.

#### Changed[​](https://docs.mapsindoors.com/changelogs/components#changed-24) <a href="#changed-24" id="changed-24"></a>

* Switched to semantic versioning.
* **mi-search**: disabled browser autocomplete.
* **mi-search**: style changes for a larger appearance.
* **mi-keyboard**: removed the enter key from the keyboard layouts.
* **mi-list-item-location**: vertically centering.

#### Fixed[​](https://docs.mapsindoors.com/changelogs/components#fixed-50) <a href="#fixed-50" id="fixed-50"></a>

* **mi-location-info**: removed alike building names.
* **mi-keyboard**: when clicking outside the keyboard to dismiss it now exposes the correct click target.
* **mi-list**: fixed reference bug.

[\
](https://docs.mapsindoors.com/changelogs/web)
