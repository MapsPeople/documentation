# Media Library

The Media Library lets you upload and manage media files to use in the MapsIndoors CMS, for example the use of custom icons for Locations, or to place images of logos on the map. The Media Library can be accessed through the "Media Library" button in the top navigation bar, on Locations and Types' Display Rules panel where applicable, like Icons, 2D Models, and 3D Models sections), as well as the App Categories page, found here: `Solution Details -> App Settings -> App Configuration -> App Categories`.

<figure><img src="../../../.gitbook/assets/CleanShot 2023-07-05 at 15.11.42@2x.png" alt=""><figcaption></figcaption></figure>

### Interface Overview[​](https://docs.mapsindoors.com/cms-media-library#interface-overview) <a href="#interface-overview" id="interface-overview"></a>

1. Upload a file to the Media Library.
2. Sort the content of the Media Library.&#x20;
   * Options are by file name or by upload date, both ascending and descending.
3. Filter the contents of the Media Library by file type.
4. Filter the contents of the Media Library, choosing between user uploads or MapsIndoors default icons.
5. Search the Media Library by file name.
6. Delete this content from the Media Library.
7. Preview of the content. Click to select the content, highlighting it.
   * Hover on this preview to see more information about this content, such as dimensions and upload date.
8. Cancel this use of the Media Library.
9. A warning if your file is too large, as it may impact performance.
   * Click the box to proceed anyway. Only appears when accessing Media Library via `Icon` menu.
10. Select the highlighted media.
11. Sync a piece of media to other Solutions you own.

### Feature List[​](https://docs.mapsindoors.com/cms-media-library#feature-list) <a href="#feature-list" id="feature-list"></a>

#### Media Library[​](https://docs.mapsindoors.com/cms-media-library#media-library) <a href="#media-library" id="media-library"></a>

The Media Library contains an overview of all uploaded media in the Solution, along with functions to manage it.

Media present in the Media Library is displayed with a preview image. This preview will also contain the filename and the option to delete the Media. If you hover over the preview, the footer will expand to reveal further information about the media, such as dimensions (in pixels) and upload date.

To select a piece of media, click on it to select it. To deselect it, click it again, or select another file in the Media Library. Your selection is indicated by a highlight. Confirm the selection of the media by clicking the "Select" button.



**Icons**[**​**](https://docs.mapsindoors.com/cms-media-library#icons)

When accessing the Media Library through the `Icon` menu point, you will be presented with a slightly different functionality than elsewhere. If you select a piece of content larger than 128x64 pixels in size, or larger than 150 kb in file size, you will be presented with a warning, in which you will have to confirm your choice before allowing you to proceed. This is to prevent unintentional performance impacts by uploading too large icons.



#### Uploading Files[​](https://docs.mapsindoors.com/cms-media-library#uploading-files) <a href="#uploading-files" id="uploading-files"></a>

Clicking the "Upload" button opens a local File Explorer window, allowing you to locate the file you wish to upload to the Media Library. If a file with the same name as the selected file already exists in the Media Library, a warning will appear. You may choose to cancel the upload, or to overwrite the existing file, but this will also replace all existing uses of the file with this name. `sampleimage.jpg` and `sampleimage.png` may both exist in the Media Library concurrently, but `sampleimage.jpg` and `SampleImage.jpg` will throw a warning, as this check is not case-sensitive.

Files cannot be larger than 8 mb.



#### Sort[​](https://docs.mapsindoors.com/cms-media-library#sort) <a href="#sort" id="sort"></a>

The "Sort" option in the filter-bar provides the option to sort the content of the Media Library alphabetically or by upload date, both ascending and descending. This provides the following options in a drop-down menu:

* File Name A-Z
* File Name Z-A
* Recently Uploaded (default)
* Oldest Upload

Clicking on one of these options initiates the sort.



#### Filters[​](https://docs.mapsindoors.com/cms-media-library#filters) <a href="#filters" id="filters"></a>

Various filtering options have been built into the Media Library, to ensure that you have an easy time finding exactly the media you need.



**Type Filter**[**​**](https://docs.mapsindoors.com/cms-media-library#type-filter)

The "Filter" option in the filter-bar opens a drop-down menu allowing you to select which file-type should be displayed. These are currently `.svg`, `.png`, `.jpg/.jpeg` and `.glb`. You can select more than one option. If no option is selected, content of all file types will be displayed.



**MapsIndoors Filter**[**​**](https://docs.mapsindoors.com/cms-media-library#mapsindoors-filter)

A secondary filter also exists, allowing you to sort between media that is user-uploaded, and media that MapsPeople has pre-loaded as default options. These "MapsIndoors Icons" cannot be deleted, but using this filter you can opt to not have them displayed.



**Search by File Name**[**​**](https://docs.mapsindoors.com/cms-media-library#search-by-file-name)

By typing a part of a filename here, the filter will return to display all media in the Media Library containing the typed string of text. For example, typing `ima` will return all three of `LimaPeru.jpg`, `sampleImage.png` and `imaginePlease.svg`.



#### Sync Manager[​](https://docs.mapsindoors.com/cms-media-library#sync-manager) <a href="#sync-manager" id="sync-manager"></a>

The Sync Manager is a feature that allows you to sync your media between Solutions you have access to. The Sync Manager window contains a preview of the media you're currently syncing, above a list of Solutions you can sync to. You then select the Solutions you want to sync to by clicking the box next to their name(s). Once synced, the files will appear in the selected Solution's Media Library.

To read more about connected features, check out our documentation on [Display Rules](https://docs.mapsindoors.com/display-rules/), and how to utilize [2D Models](https://docs.mapsindoors.com/cms#2d-models-and-icons/).
