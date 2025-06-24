# Set Up Your Environment

This guide assumes you have a basic understanding of HTML, CSS, and JavaScript. If you're new to web development, we recommend using an Integrated Development Environment (IDE) like [Visual Studio Code](https://code.visualstudio.com/) to help manage your files and code.

Let's start by creating the necessary file structure:

1. **Create a new project folder:** Choose any location on your computer. Let's call this folder `mapsindoors-tutorial`. Ensure the folder is empty.
2. **Create three empty files** inside your `mapsindoors-tutorial` folder:
   * `index.html`: This file will be your application's entry point and contain the main HTML structure.
   * `style.css:` This file will contain the CSS styles for the HTML document.
   * `script.js`: This file will contain the JavaScript code for initializing and controlling the MapsIndoors map.

Now, open the `index.html` file in your code editor and add the following basic HTML structure. This includes the necessary boilerplate and links to the MapsIndoors Web SDK, your custom CSS, and your custom JavaScript file:

{% code lineNumbers="true" %}
```html
<!-- index.html -->

<!DOCTYPE html>
<html lang="en">

<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>MapsIndoors</title>
    <!-- Include the style.css -->
    <link rel="stylesheet" href="style.css">
    <!-- Include the MapsIndoors SDK -->
    <script src="https://app.mapsindoors.com/mapsindoors/js/sdk/4.41.0/mapsindoors-4.41.0.js.gz"></script>
</head>

<body>
    <script src="script.js"></script>
</body>

</html>
```
{% endcode %}

Next, open the `style.css` file and add the following styles. These styles ensure the map container can fill the page correctly.

{% code lineNumbers="true" %}
```css
/* style.css */

/* Ensure html and body take up full height and use flexbox */
html, body {
    height: 100%;
    margin: 0;
    padding: 0;
    overflow: hidden; /* Prevent scrollbars if map is full size */
    display: flex; /* Use flexbox for layout */
    flex-direction: column; /* Stack children vertically */
}
```
{% endcode %}

**Explanation:**

* We've set up a standard HTML5 document (`index.html`).
* The `<meta>` tags ensure proper character display and responsiveness.
* The `<title>` tag sets the title for the browser tab.
* The `<link rel="stylesheet" href="style.css">` tag in the `<head>` loads your external CSS file, allowing you to style your HTML elements.
* The first `<script>` tag in the `<head>` loads the MapsIndoors Web SDK library.
* The `<script src="script.js"></script>` tag at the end of the `<body>` links to your custom JavaScript file. Placing it here ensures it runs after the HTML body has been parsed and the MapsIndoors SDK is available.
* The `style.css` file currently contains basic styles for the `html` and `body` elements. We've added `display: flex` and `flex-direction: column` to prepare the page for a flexible layout where elements can fill the available space, which will be useful when adding the map and other UI components later. `height: 100%`, `margin: 0`, `padding: 0`, and `overflow: hidden` are included to ensure the page takes up the full viewport and prevents unwanted scrollbars.

_Note: In this documentation, we indicate which file a code snippet belongs to by showing the filename on the first line in the code samples._

You have now created the basic project structure and included the MapsIndoors SDK and your custom CSS file.
