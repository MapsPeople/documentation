# Caching & Offline Data

### Cacheable Data[​](https://docs.mapsindoors.com/offline-data#cacheable-data-2) <a href="#cacheable-data-2" id="cacheable-data-2"></a>

MapsIndoors has three levels of caching:

1. **Basic Data**: All descriptions, geometries and metadata about POIs, rooms, areas, buildings and venues.
2. **Detailed Data**: The same as **Basic Data**, plus images referenced by the data.
3. **Full Dataset**: The same as **Detailed Data**, plus Map Tiles. All necessary content is downloaded for a data set to work offline.

Full Dataset caching requires that Map Tiles are prepared specifically for this purpose. Contact MapsPeople in order to arrange this.

### Automatic Caching[​](https://docs.mapsindoors.com/offline-data#automatic-caching-2) <a href="#automatic-caching-2" id="automatic-caching-2"></a>

Out of the box, MapsIndoors automatically caches all basic data for the **active** dataset on the device, whereas images and Map Tiles are cached only as they are used.

This means all MapsIndoors-specific data is cached automatically, but images are only cached after they have been needed for map display. Likewise, Map Tiles are only cached when needed for map display, so all parts of the map that has been shown are cached. Areas and Zoom Levels that have not been shown as part of user interaction are not cached.

### Tweaking Caching Behaviour[​](https://docs.mapsindoors.com/offline-data#tweaking-caching-behaviour-2) <a href="#tweaking-caching-behaviour-2" id="tweaking-caching-behaviour-2"></a>

Applications have a few ways to change the default caching behaviour:

The synchronization process can be started manually:

```swift
do {
    try await MPMapsIndoors.shared.synchronize()
} catch {
    print("Error synchronizing MapsIndoors")
}
```

The level of caching can be changed:

```swift
let dataSetManager = MPMapsIndoors.shared.datasetCacheManager
let dataSet = dataSetManager.dataSetForCurrentMapsIndoorsAPIKey()
dataSetManager.setCachingScope(.full, cacheItem: dataSet!.cacheItem)
```

### Caching of Multiple Datasets[​](https://docs.mapsindoors.com/offline-data#caching-of-multiple-datasets-2) <a href="#caching-of-multiple-datasets-2" id="caching-of-multiple-datasets-2"></a>

The most common use of MapsIndoors involves only one dataset, but for large deployments, data may be partitioned into multiple datasets.

Offline caching of multiple simultaneous datasets is fully supported, and is mostly limited by the available storage space on device.

**NOTE**: Only one dataset is active at any point in time.

Management of multiple datasets is done via `MPDataSetCacheManager`, which allows querying, adding, modifying and removing datasets.

#### Listing Managed Datasets[​](https://docs.mapsindoors.com/offline-data#listing-managed-datasets-2) <a href="#listing-managed-datasets-2" id="listing-managed-datasets-2"></a>

All datasets currently managed are accessible via the `MPMapsIndoors.shared.datasetCacheManager.managedDataSets` collection:

```swift
for ds in MPMapsIndoors.shared.datasetCacheManager.managedDataSets {
    print("\(ds.name): size \(ds.cacheItem.syncSize)")
}
```

This can be used to build a management user interface, and information about individual datasets can be accessed from the `MPDataSetCache` and `MPDataSetCacheItem` classes.

#### Adding Datasets for Offline Caching[​](https://docs.mapsindoors.com/offline-data#adding-datasets-for-offline-caching-2) <a href="#adding-datasets-for-offline-caching-2" id="adding-datasets-for-offline-caching-2"></a>

Datasets are scheduled for caching using one of the `MPMapsIndoors.shared.datasetCacheManager.addDataSet()` variants:

```swift
MPMapsIndoors.shared.datasetCacheManager.addDataSet("API Key")
MPMapsIndoors.shared.datasetCacheManager.addDataSet("API Key", cachingScope: .basic)
```

The current MapsIndoors API key is automatically added as a managed dataset with `.basic`.

#### Removing Datasets[​](https://docs.mapsindoors.com/offline-data#removing-datasets-2) <a href="#removing-datasets-2" id="removing-datasets-2"></a>

Datasets are removed from the cache using:

```swift
MPMapsIndoors.shared.datasetCacheManager.removeDataSet(MPDataSetCache)
```

**NOTE:** The currently active dataset is not removed.

#### Changing Caching Parameters[​](https://docs.mapsindoors.com/offline-data#changing-caching-parameters-2) <a href="#changing-caching-parameters-2" id="changing-caching-parameters-2"></a>

To change the extent of caching, for example in a management menu:

```swift
let dataSetManager = MPMapsIndoors.shared.datasetCacheManager
let dataSet = dataSetManager.dataSetForCurrentMapsIndoorsAPIKey()
dataSetManager.setCachingScope(.detailed, cacheItem: dataSet!.cacheItem)
```

#### Determining the Caching Size of a Dataset[​](https://docs.mapsindoors.com/offline-data#determining-the-caching-size-of-a-dataset-2) <a href="#determining-the-caching-size-of-a-dataset-2" id="determining-the-caching-size-of-a-dataset-2"></a>

The estimated and cached size of a dataset is available via:

```swift
dataSet?.cacheItem.cachedSize
dataSet?.cacheItem.syncSize
```

To refesh or get the size of a synced dataset:

```swift
dataSetManager.fetchSyncSizesFor(dataSetCaches: [dataSet], delegate: self)
```

This is an asynchronous process, and a `MPDataSetCacheManagerSizeDelegate` is needed for getting information about progress and results. The reference docs for `MPDataSetCacheManagerSizeDelegate` can be found [here](https://app.mapsindoors.com/mapsindoors/reference/ios/4.2.1/documentation/mapsindoors/mpdatasetcachemanagersizedelegate)

#### Synchronizing Data with MPDataSetCacheManager[​](https://docs.mapsindoors.com/offline-data#synchronizing-data-with-mpdatasetcachemanager-2) <a href="#synchronizing-data-with-mpdatasetcachemanager-2" id="synchronizing-data-with-mpdatasetcachemanager-2"></a>

The `MPDataSetCacheManager`allows for finegrained control which datasets are synchronized, and allows for cancellation:

```swift
// Sync allmanaged datasets:
dataSetManager.synchronizeContent()

// Synchronize specific datasets:
dataSetManager.synchronizeCacheItems([dataSet.cacheItem, ...])

// Cancellation:
dataSetManager.cancelSynchronization()
```
