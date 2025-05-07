# Step 1. Set Up Your Environment[​](https://docs.mapsindoors.com/getting-started/web/new-project#set-up-your-environment) <a href="#set-up-your-environment" id="set-up-your-environment"></a>

1. If you do not have prior development experience, you can install an Integrated Development Environment (IDE), e.g. [<mark style="color:purple;">Visual Studio Code</mark>](https://code.visualstudio.com/).
2. Start by creating a new project folder. The location is not important, just remember the location, and ensure your newly created project folder is empty.
3. Inside that, create two empty files: `index.html` and `script.js`.

    > The file `index.html` is the entry point for our application and contains the HTML code. The file `script.js` will be read by `index.html` and consists of the JavaScript code for the actual application to run. To try the app you will be creating, run `index.html` in your web browser.
4. Open `index.html`. Create a basic HTML structure and include the `script.js` file as follows:

```html
<!-- index.html -->
<!DOCTYPE html>
<html lang="en">

<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>MapsIndoors</title>
</head>

<body>
    <script src="script.js"></script>
</body>

</html>
```

Both here, and in the following examples, you will always be able to see which of the two files the code should go in, by looking at the first line, where the name of the file is written.

Your environment is now fully configured, and you have the necessary API keys. Next, you will learn how to display a map with MapsIndoors.
