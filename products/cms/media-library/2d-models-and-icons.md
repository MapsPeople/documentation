# 2D Models and Icons

The Media Library currently supports 2D models and Icons.

In the CMS you can change the Icons for markers on the Map by using the Media Library. This is done by opening the Media Library from the Icon and 2D Model sections in the Display Rules editor, both on Location, Type, and Main Display Rule levels. More information about the Media Library can be found [here](https://docs.mapsindoors.com/cms-media-library/).

In the Media Library, you can see and manage all uploaded media, both 2D Models and icons. Media can be either in .jpg, .png or .svg file formats. For icons specifically, we highly recommend using the SVG format.

Icons have a suggested limit of 128x64 pixels, and no more than 150kb in size. There is a hard limit on file size of 8 mb.

Please note, that when using the Integration API to set urls for Icons and 2D Models, all media must reside on a server that has CORS enabled. Otherwise the Media can't be loaded when using the JavaScript SDK.

### Uploading SVGs​ <a href="#uploading-svgs" id="uploading-svgs"></a>

SVG is a vector file format, which lets MapsIndoors convert your Icon in a range of sizes to get the best looking Icon in every situation.

SVGs should be uploaded with a `width` and `height` that you want the SVG to be displayed on the Map in. Make sure you define it in `px`, not `cm` or `%`. E.g., if you want to display a 32x24px Icon on the Map, upload an SVG with the attributes `width='32px'` and `height='24px'`. For consistency, it's good form to make the `viewport` the same size as the `width` and `height`.

### Supported SVG Elements​ <a href="#supported-svg-elements" id="supported-svg-elements"></a>

We only accept SVGs that conform to a very strict ruleset. If an uploaded SVG contains anything other than the elements and attributes listed below, it will be rejected. All child elements can be nested as supported by the SVG format.

* `<svg>` element
* `viewbox` attribute
* `<path>` child element
* `<rect>` child element
* `<title>` child element
* `<desc>` child element
* `<circle>` child element
* `<defs>` child element
* `<ellipse>` child element
* `<line>` child element
* `<pattern>` child element
* `<polygon>` child element
* `<polyline>` child element
* `<text>` child element
* `<stop>` child element
* `<use>` child element
* `<linearGradient>` child element
* `<radialGradient>` child element
* `<symbol>` child element
* `<style>` child element
* `<tspan>` child element

When you try to upload an SVG containing one or more of these elements and/or attributes, the upload is cancelled and you will see which files contain the unsupported elements.

If your SVGs contain unsupported elements, you must remove them before they can be uploaded. One typical issue is embedded `base64` data in the SVG, which usually indicates the SVG will display raster image data (PNGs and the like) somewhere in it. That can lead to unintended consequences on the map.

#### SVG Help​ <a href="#svg-help" id="svg-help"></a>

Michelle Barker has written [a terrific guide to optimizing SVGs for the web](https://css-irl.info/optimising-svgs-for-the-web/) on her site.

A great tool to strip unnecesary elements from your SVG-file is [SVGOMG by Jake Archibald](https://jakearchibald.github.io/svgomg/).

### Syncing Icons to Other Solutions​ <a href="#syncing-icons-to-other-solutions" id="syncing-icons-to-other-solutions"></a>

If you have multiple Solutions, you can sync Media across multiple Solutions to make sure you can use the same Media in all of your Solutions.

Open the Media Library. Find the Media you want to sync to one or more Solutions, and click the "Sync"-icon. Then you can select which other Solutions you want to sync this Media to.

When you sync a piece of Media, if the Media exists in the target Solution (i.e. a piece of Media with the exact same filename), you override the Media in the target Solution. If the Media does not exist in the target Solution, it is added.

### PNG Image File Support​ <a href="#png-image-file-support" id="png-image-file-support"></a>

We highly recommend using SVGs for Icons across MapsIndoors, but support PNG files as well.

When uploading an Icon in the PNG format, make sure you upload it in a 3x size to accommodate for it being scaled down on the map. For example, to display a 20x20px Icon on the Map, upload it in 60x60px.

[\
](https://docs.mapsindoors.com/cms-media-library)
