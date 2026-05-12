# Manage Content

Manage offline content through the UNL Navigation SDK for iOS.

## Content types[​](#content-types "Direct link to Content types")

The SDK provides dedicated context classes for each type of downloadable content:

* **`MapsContext`**
    - Downloads road maps covering countries and regions for search, routing, and navigation
* **`MapStyleContext`** 
    - Downloads map styles (high-res and low-res) that can be applied offline
* **`HumanVoiceContext`** 
    - Downloads pre-recorded human voice files for spoken navigation instructions

Each context class exposes a consistent API for listing, downloading, and managing its respective content type.

> 💡 **TIP**
>
> Use `MapsContext` for most offline use cases — road map data is required for offline search, routing, and navigation.

## ContentStoreObject overview[​](#contentstoreobject-overview "Direct link to ContentStoreObject overview")

Each downloadable item is represented by a `ContentStoreObject`, which provides details such as name, identifier, type, version, and size. You download and delete content through the item object itself.

> 🚨 **DANGER**
>
> Ensure your API token is set and valid before performing content store operations.

> 🚨 **DANGER**
>
> Modifying downloaded maps (download, delete, update) may interrupt ongoing operations such as search, route calculation, or navigation.

## List available maps[​](#list-available-maps "Direct link to List available maps")

### List local content[​](#list-local-content "Direct link to List local content")

Retrieve locally available maps using `getLocalList()` on a `MapsContext` instance:

```swift
let mapsContext = MapsContext()
let localMaps = mapsContext.getLocalList()
for item in localMaps {
    print("\(item.getName()) — \(item.getTotalSizeFormatted())")
}

```

### List online content[​](#list-online-content "Direct link to List online content")

Retrieve the list of available maps from the UNL servers using `getOnlineList(_:)`:

```swift
mapsContext.getOnlineList { items in
    for item in items {
        print("\(item.getName()) — status: \(item.getStatus().rawValue)")
    }
}

```

> 📝 **INFO**
>
> Call `getOnlineList(_:)` only when an active internet connection is available. Use `getLocalList()` when offline.

The same pattern applies for styles and voices:

```swift
let styleContext = MapStyleContext()
styleContext.getOnlineList { items in
    // handle style items
}

let voiceContext = HumanVoiceContext()
voiceContext.getOnlineList { items in
    // handle voice items
}

```

## ContentStoreObject fields[​](#contentstoreobject-fields "Direct link to ContentStoreObject fields")

### General information[​](#general-information "Direct link to General information")

| Method                      | Description                                                        |
| --------------------------- | ------------------------------------------------------------------ |
| `getName()`                 | The name of the content item, automatically translated             |
| `getIdentifier()`           | The unique identifier of the item                                  |
| `getType()`                 | The content type as a `ContentStoreOnlineType` value               |
| `getFileName()`             | The full path to the content data file                             |
| `getTotalSize()`            | The total size of the content in bytes                             |
| `getTotalSizeFormatted()`   | The total size as a formatted string                               |
| `getAvailableSize()`        | The downloaded size of the content in bytes                        |
| `getStatus()`               | The current status as a `ContentStoreObjectStatus` value           |
| `isImagePreviewAvailable()` | Returns whether an image preview is available                      |
| `getImagePreview(_:)`       | Returns the preview image at the specified width                   |
| `getChapterName()`          | The chapter name for large countries divided into multiple items   |
| `getCountryCodes()`         | Array of ISO 3166-1 alpha-3 country codes associated with the item |

> 💡 **TIP**
>
> Check a `ContentStoreObject`'s current state using `getStatus()`:
>
> * `ContentStoreObjectStatusUnavailable` — Not downloaded; cannot be used
> * `ContentStoreObjectStatusCompleted` — Downloaded and ready to use
> * `ContentStoreObjectStatusPaused` — Download paused by the user
> * `ContentStoreObjectStatusDownloadQueued` — Download queued, waiting for resources
> * `ContentStoreObjectStatusDownloadWaiting` — Waiting for a network connection
> * `ContentStoreObjectStatusDownloadWaitingFreeNetwork` — Waiting for a non-metered network
> * `ContentStoreObjectStatusDownloadRunning` — Download actively in progress
> * `ContentStoreObjectStatusUpdateWaiting` — Waiting for an update operation to finish

> 🚨 **DANGER**
>
> Content items of type `roadMap` do not have an image preview. Use `getCountryFlagWithIsoCode(_:size:)` on `MapsContext` to retrieve a country flag image instead.

### Download and update information[​](#download-and-update-information "Direct link to Download and update information")

| Method                     | Description                                               |
| -------------------------- | --------------------------------------------------------- |
| `getClientVersion()`       | The current client version of the content                 |
| `getDownloadProgress()`    | The current download progress (0–100)                     |
| `isCompleted()`            | Returns `true` if the item is fully downloaded            |
| `isUpdatable()`            | Returns `true` if a newer version is available            |
| `getUpdateSize()`          | The update size in bytes, if an update is available       |
| `getUpdateSizeFormatted()` | The update size as a formatted string                     |
| `getUpdateVersion()`       | The newer version string, if available                    |
| `getUpdateItem()`          | The corresponding update item if an update is in progress |
| `canDeleteContent()`       | Returns `true` if the content can be deleted              |

## Download content[​](#download-content "Direct link to Download content")

Download a content item by calling `download(withAllowCellularNetwork:completionHandler:)` on the `ContentStoreObject`. Set `allowCellularNetwork` to `true` to permit downloads over cellular:

```swift
item.download(withAllowCellularNetwork: false) { success in
    print("Download completed: \(success)")
}

```

Track progress using the variant with a `progressHandler`:

```swift
item.download(withAllowCellularNetwork: false, progressHandler: { progress in
    print("Download progress: \(progress)/100")
}) { success in
    print("Download completed: \(success)")
}

```

Alternatively, use `MapsContext` to download a map by identifier:

```swift
let mapId = localMaps.first?.getIdentifier() ?? 0
mapsContext.downloadMap(withIdentifier: mapId, allowCellularNetwork: false) { success in
    print("Map download completed: \(success)")
}

```

### Pause and resume download[​](#pause-and-resume-download "Direct link to Pause and resume download")

Pause an active download using `pauseDownload()`. Resume it by calling `downloadWithAllowCellularNetwork(_:completionHandler:)` again:

```swift
item.pauseDownload()

```

> 🚨 **DANGER**
>
> Do not perform further operations on the `ContentStoreObject` until the pause operation has completed.

### Cancel download[​](#cancel-download "Direct link to Cancel download")

Cancel and discard partially downloaded content using `cancelDownload()`:

```swift
item.cancelDownload()

```

## Monitor download progress via delegate[​](#monitor-download-progress-via-delegate "Direct link to Monitor download progress via delegate")

Implement `ContentStoreObjectDelegate` on the item to receive status and progress callbacks:

```swift
item.delegate = self

// MARK: - ContentStoreObjectDelegate

func contentStoreObject(_ object: ContentStoreObject, notifyStart hasProgress: Bool) {
    print("Download started, has progress: \(hasProgress)")
}

func contentStoreObject(_ object: ContentStoreObject, notifyProgress progress: Int32) {
    print("Download progress: \(progress)/100")
}

func contentStoreObject(_ object: ContentStoreObject, notifyComplete success: Bool) {
    print("Download completed: \(success)")
}

func contentStoreObject(_ object: ContentStoreObject, notifyStatusChanged status: ContentStoreObjectStatus) {
    print("Status changed: \(status.rawValue)")
}

```

## Delete downloaded content[​](#delete-downloaded-content "Direct link to Delete downloaded content")

Check if an item can be removed, then call `deleteContent()`:

```swift
if item.canDeleteContent() {
    item.deleteContent()
    print("Content deleted")
} else {
    print("Content cannot be deleted")
}

```
