# Caching & Offline Data

{% tabs %}
{% tab title="Java" %}
#### Cacheable Data[​](https://docs.mapsindoors.com/offline-data#cacheable-data) <a href="#cacheable-data" id="cacheable-data"></a>

MapsIndoors has three levels of caching:

1. **Basic Data**: All descriptions, geometries and metadata about POIs, rooms, areas, buildings and venues.
2. **Detailed Data**: The same as **Basic Data**, plus images referenced by the data.
3. **Full Dataset**: The same as **Detailed Data**, plus Map Tiles.

Full Dataset caching requires that Map Tiles are prepared specifically for this purpose. Contact MapsPeople in order to arrange this.

#### Automatic Caching[​](https://docs.mapsindoors.com/offline-data#automatic-caching) <a href="#automatic-caching" id="automatic-caching"></a>

Out of the box, MapsIndoors automatically caches all basic data for the **active** dataset on the device, whereas images and Map Tiles are cached only as they are used.

This means all MapsIndoors-specific data is cached automatically, but images are only cached after they have been needed for map display. Likewise, Map Tiles are only cached when needed for map display, so all parts of the map that has been shown are cached. Areas and Zoom Levels that have not been shown as part of user interaction are not cached.

#### Tweaking Caching Behaviour[​](https://docs.mapsindoors.com/offline-data#tweaking-caching-behaviour) <a href="#tweaking-caching-behaviour" id="tweaking-caching-behaviour"></a>

Applications have a few ways to change the default caching behaviour:

The synchronization process can be started manually:

```java
MapsIndoors.synchronizeContent((e) -> {
    ...
});
```

The level of caching can be changed:

```java
MPDataSetCache dataSet = MPDataSetCacheManager.getInstance().getDataSetByID("API KEY");
dataSet.setScope(mContext, MPDataSetCacheScope.DETAILED);
MPDataSetCacheManager.getInstance().synchronizeDataSets(Collections.singletonList(dataSet));
```

#### Caching of Multiple Datasets[​](https://docs.mapsindoors.com/offline-data#caching-of-multiple-datasets) <a href="#caching-of-multiple-datasets" id="caching-of-multiple-datasets"></a>

The most common use of MapsIndoors involves only one dataset, but for large deployments, data may be partitioned into multiple datasets.

Offline caching of multiple simultaneous datasets is fully supported, and is mostly limited by the available storage space on device.

**NOTE**: Only one dataset is active at any point in time.

Management of multiple datasets is done via `MPDataSetCacheManager`, which allows querying, adding, modifying and removing datasets.

**Listing Managed Datasets**[**​**](https://docs.mapsindoors.com/offline-data#listing-managed-datasets)

All datasets currently managed are accessible via the `MPDataSetCacheManager`:

```java
for (MPDataSetCache dataSet : MPDataSetCacheManager.getInstance().getManagedDataSets()) {
    Log.i("dataset", dataSet.getSolutionId() + ": size " + dataSet.getCacheItem().getSyncSize());
}
```

This can be used to build a management user interface, and information about individual datasets can be accessed from the `MPDataSetCache` and `MPDataSetCacheItem` classes.

**Adding Datasets for Offline Caching**[**​**](https://docs.mapsindoors.com/offline-data#adding-datasets-for-offline-caching)

Datasets are scheduled for caching using `MPDataSetCacheManager`:

```java
MPDataSetCacheManager.getInstance().addDataSetWithCachingScope("API KEY", MPDataSetCacheScope.BASIC);
```

The current MapsIndoors API key is automatically added as a managed dataset with `MPDataSetCacheScope.BASIC`.

**Removing Datasets**[**​**](https://docs.mapsindoors.com/offline-data#removing-datasets)

Datasets are removed from caching using `MPDataSetCacheManager.getInstance().removeDataSetCache(MPDataSetCache);`:

```java
MPDataSetCacheManager.getInstance().removeDataSetCache(MPDataSetCache);
```

**NOTE:** The currently active dataset is not removed.

**Changing Caching Parameters**[**​**](https://docs.mapsindoors.com/offline-data#changing-caching-parameters)

To change the extent of caching, for example in a management menu:

```java
MPDataSetCache dataSet = MPDataSetCacheManager.getInstance().getDataSetByID("API KEY");
dataSet.setScope(mContext, MPDataSetCacheScope.DETAILED);
MPDataSetCacheManager.getInstance().synchronizeDataSets(Collections.singletonList(dataSet));
```

**Determining the Caching Size of a Dataset**[**​**](https://docs.mapsindoors.com/offline-data#determining-the-caching-size-of-a-dataset)

The estimated and cached size of a dataset is available via:

```java
dataSet.getCacheItem().getCacheSize(mContext);
dataSet.getCacheItem().getSyncSize();
```

To refresh or get the size of a synced dataset:

```java
MPDataSetCacheManager.getInstance().getSyncSizesForDataSetCaches(Collections.singletonList(dataSet), this);
```

This is an asynchronous process, and a `MPDataSetCacheManagerSizeListener` is needed for getting information about progress and results.

**Synchronizing Data with MPDataSetCacheManager**[**​**](https://docs.mapsindoors.com/offline-data#synchronizing-data-with-mpdatasetcachemanager)

The `MPDataSetCacheManager`allows for detailed control over which datasets are synchronized, and allows for cancellation:

```java
MPDataSetCacheManager dataSetCacheManager = MPDataSetCacheManager.getInstance();

// sync all managed datasets
dataSetCacheManager.synchronizeDataSets();

// sync specific datasets
dataSetCacheManager.synchronizeDataSets(dataSets);
```
{% endtab %}

{% tab title="Kotlin" %}
#### Cacheable Data[​](https://docs.mapsindoors.com/offline-data#cacheable-data-1) <a href="#cacheable-data-1" id="cacheable-data-1"></a>

MapsIndoors has three levels of caching:

1. **Basic Data**: All descriptions, geometries and metadata about POIs, rooms, areas, buildings and venues.
2. **Detailed Data**: The same as **Basic Data**, plus images referenced by the data.
3. **Full Dataset**: The same as **Detailed Data**, plus Map Tiles.

Full Dataset caching requires that Map Tiles are prepared specifically for this purpose. Contact MapsPeople in order to arrange this.

#### Automatic Caching[​](https://docs.mapsindoors.com/offline-data#automatic-caching-1) <a href="#automatic-caching-1" id="automatic-caching-1"></a>

Out of the box, MapsIndoors automatically caches all basic data for the **active** dataset on the device, whereas images and Map Tiles are cached only as they are used.

This means all MapsIndoors-specific data is cached automatically, but images are only cached after they have been needed for map display. Likewise, Map Tiles are only cached when needed for map display, so all parts of the map that has been shown are cached. Areas and Zoom Levels that have not been shown as part of user interaction are not cached.

#### Tweaking Caching Behaviour[​](https://docs.mapsindoors.com/offline-data#tweaking-caching-behaviour-1) <a href="#tweaking-caching-behaviour-1" id="tweaking-caching-behaviour-1"></a>

Applications have a few ways to change the default caching behaviour:

The synchronization process can be started manually:

```kotlin
MapsIndoors.synchronizeContent { error ->
    ...
}
```

The level of caching can be changed:

```kotlin
val dataset = MPDataSetCacheManager.getInstance().getDataSetByID("API KEY")
dataset?.setScope(mContext, MPDataSetCacheScope.DETAILED)
MPDataSetCacheManager.getInstance().synchronizeDataSets(Collections.singletonList(dataset))
```

#### Caching of Multiple Datasets[​](https://docs.mapsindoors.com/offline-data#caching-of-multiple-datasets-1) <a href="#caching-of-multiple-datasets-1" id="caching-of-multiple-datasets-1"></a>

The most common use of MapsIndoors involves only one dataset, but for large deployments, data may be partitioned into multiple datasets.

Offline caching of multiple simultaneous datasets is fully supported, and is mostly limited by the available storage space on device.

**NOTE**: Only one dataset is active at any point in time.

Management of multiple datasets is done via `MPDataSetCacheManager`, which allows querying, adding, modifying and removing datasets.

**Listing Managed Datasets**[**​**](https://docs.mapsindoors.com/offline-data#listing-managed-datasets-1)

All datasets currently managed are accessible via the `MPDataSetCacheManager`:

```kotlin
for (dataSet in MPDataSetCacheManager.getInstance().managedDataSets) {
    Log.i("dataset", dataSet.solutionId + ": size " + dataSet.cacheItem.syncSize)
}
```

This can be used to build a management user interface, and information about individual datasets can be accessed from the `MPDataSetCache` and `MPDataSetCacheItem` classes.

**Adding Datasets for Offline Caching**[**​**](https://docs.mapsindoors.com/offline-data#adding-datasets-for-offline-caching-1)

Datasets are scheduled for caching using `MPDataSetCacheManager`:

```kotlin
MPDataSetCacheManager.getInstance()
        .addDataSetWithCachingScope("API KEY", MPDataSetCacheScope.BASIC)
```

The current MapsIndoors API key is automatically added as a managed dataset with `MPDataSetCacheScope.BASIC`.

**Removing Datasets**[**​**](https://docs.mapsindoors.com/offline-data#removing-datasets-1)

Datasets are removed from caching using `MPDataSetCacheManager.getInstance().removeDataSetCache(MPDataSetCache);`:

```kotlin
MPDataSetCacheManager.getInstance().removeDataSetCache(MPDataSetCache)
```

**NOTE:** The currently active dataset is not removed.

**Changing Caching Parameters**[**​**](https://docs.mapsindoors.com/offline-data#changing-caching-parameters-1)

To change the extent of caching, for example in a management menu:

```kotlin
val dataset = MPDataSetCacheManager.getInstance().getDataSetByID("API KEY")
dataset?.setScope(mContext, MPDataSetCacheScope.DETAILED)
MPDataSetCacheManager.getInstance().synchronizeDataSets(Collections.singletonList(dataset))
```

**Determining the Caching Size of a Dataset**[**​**](https://docs.mapsindoors.com/offline-data#determining-the-caching-size-of-a-dataset-1)

The estimated and cached size of a dataset is available via:

```kotlin
dataSet?.cacheItem?.getCacheSize(mContext)
dataSet?.cacheItem?.syncSize
```

To refresh or get the size of a synced dataset:

```kotlin
MPDataSetCacheManager.getInstance().getSyncSizesForDataSetCaches(listOf(dataSet), this)
```

This is an asynchronous process, and a `MPDataSetCacheManagerSizeListener` is needed for getting information about progress and results.

**Synchronizing Data with MPDataSetCacheManager**[**​**](https://docs.mapsindoors.com/offline-data#synchronizing-data-with-mpdatasetcachemanager-1)

The `MPDataSetCacheManager`allows for detailed control over which datasets are synchronized, and allows for cancellation:

```kotlin
MPDataSetCacheManager dataSetCacheManager = MPDataSetCacheManager.getInstance();

// sync all managed datasets
dataSetCacheManager.synchronizeDataSets()

// sync specific datasets
dataSetCacheManager.synchronizeDataSets(dataSets)
```
{% endtab %}
{% endtabs %}
