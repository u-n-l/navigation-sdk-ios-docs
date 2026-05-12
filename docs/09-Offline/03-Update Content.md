# Update Content

The UNL Navigation SDK for iOS allows updating downloaded content to stay synchronized with the latest map data.

New map versions are released every few weeks. The update operation is supported via `MapsContext` for road maps and via `MapStyleContext` for map styles.

The SDK requires all road map content items to share the same version. Partial updates of individual items are not supported.

> 🚨 **DANGER**
>
> The update process invalidates all routes currently in use. Ensure there are no active navigation sessions, route calculations, or similar operations running when applying an update.
>
> If a navigation session or route calculation is in progress at the time of the update, it will fail. Interacting with objects created before the update — such as route or navigation instruction objects — may lead to undefined behavior.
>
> Cancel all ongoing operations and discard related objects before applying the update.

> 📝 **INFO**
>
> The iOS SDK does not provide an automatic update mechanism for offline content. All updates for the downloaded content must be manually triggered via the `MapsContext` or `MapStyleContext` API.

## Content online support status[​](#content-online-support-status "Direct link to Content online support status")

Based on the client's content version relative to the newest available release, it can be in one of three states, defined by `ContentStoreOnlineSupportStatus`:

| Status                                       | Description                                                                                                                                                                                                 |
| -------------------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `ContentStoreOnlineSupportStatusUpToDate`    | The client version is current. Features work online when connected and offline using downloaded regions. No updates are available.                                                                          |
| `ContentStoreOnlineSupportStatusOldData`     | The client version is outdated but still supports online operations. An update is recommended.                                                                                                              |
| `ContentStoreOnlineSupportStatusExpiredData` | The client version is significantly outdated and no longer supports online operations. All features function exclusively on downloaded regions. An update is **mandatory** to restore online functionality. |

## Check for updates[​](#check-for-updates "Direct link to Check for updates")

Call `checkForUpdate(_:)` on `MapsContext` to check whether a new version is available:

```swift
let mapsContext = MapsContext()

mapsContext.checkForUpdate { status in
    switch status {
    case .upToDate:
        print("Map is up to date")
    case .oldData:
        print("A new map version is available — update recommended")
    case .expiredData:
        print("Map version has expired — update required")
    @unknown default:
        break
    }
}

```

## Start the update[​](#start-the-update "Direct link to Start the update")

Call `update(withAllowCellularNetwork:completionHandler:)` to download and automatically apply the update once all items are downloaded:

```swift
mapsContext.update(withAllowCellularNetwork: false) { success in
    if success {
        print("Update completed. New version: \(mapsContext.getWorldMapVersion())")
    } else {
        print("Update failed")
    }
}

```

Set `allowCellularNetwork` to `true` to permit downloading over cellular data.

### Defer applying the update[​](#defer-applying-the-update "Direct link to Defer applying the update")

Use the `canUpdateBeAppliedHandler` variant to control when the update is applied — for example, to wait until active navigation ends:

```swift
mapsContext.update(withAllowCellularNetwork: false, canUpdateBeAppliedHandler: { [weak self] in
    guard let strongSelf = self else { return false }
    
    // Return true only when safe to apply (e.g. no active navigation)
    return strongSelf.navigationContext.isNavigationActive()
    
}) { success in
    print("Update result: \(success)")
}

```

## Monitor update progress via delegate[​](#monitor-update-progress-via-delegate "Direct link to Monitor update progress via delegate")

Assign a `ContentUpdateDelegate` to `delegateUpdate` on `MapsContext` to receive status and progress callbacks throughout the update process:

```swift
mapsContext.delegateUpdate = self

func contextUpdate(_ context: NSObject, notifyStart hasProgress: Bool) {
    print("Update started")
}

func contextUpdate(_ context: NSObject, notifyProgress progress: Int32) {
    print("Update progress: \(progress)/100")
}

func contextUpdate(_ context: NSObject, notifyComplete success: Bool) {
    print("Update completed: \(success)")
}

func contextUpdate(_ context: NSObject, notifyStatusChanged status: ContentUpdateStatus) {
    print("Update status: \(status.rawValue)")
}

```

**`ContentUpdateStatus` values:**

| Value                                         | Description                                    |
| --------------------------------------------- | ---------------------------------------------- |
| `ContentUpdateStatusIdle`                     | Not started                                    |
| `ContentUpdateStatusWaitConnection`           | Waiting for internet connection                |
| `ContentUpdateStatusWaitWIFIConnection`       | Waiting for Wi-Fi connection                   |
| `ContentUpdateStatusCheckForUpdate`           | Checking for updated items                     |
| `ContentUpdateStatusDownload`                 | Downloading updated content                    |
| `ContentUpdateStatusFullyReady`               | Update fully downloaded and ready to apply     |
| `ContentUpdateStatusPartiallyReady`           | Update partially downloaded and ready to apply |
| `ContentUpdateStatusDownloadRemainingContent` | Downloading remaining content after appliance  |
| `ContentUpdateStatusDownloadPendingContent`   | Downloading pending content                    |
| `ContentUpdateStatusComplete`                 | Finished with success                          |
| `ContentUpdateStatusError`                    | Finished with error                            |

## Cancel the update[​](#cancel-the-update "Direct link to Cancel the update")

Call `cancelUpdate()` to abort an in-progress update:

```swift
mapsContext.cancelUpdate()

```

## Check update state[​](#check-update-state "Direct link to Check update state")

Query the current update state at any time:

```swift
let isStarted = mapsContext.isUpdateStarted()
let progress = mapsContext.getUpdateProgress()     // 0–100
let status = mapsContext.getUpdateStatus()
let canApply = mapsContext.canApplyUpdate()
let updateItems = mapsContext.getUpdateItems()

```

Call `applyUpdate()` manually if you want to trigger the application of a fully downloaded update:

```swift
if mapsContext.canApplyUpdate() {
    mapsContext.applyUpdate()
}

```

## Update map styles[​](#update-map-styles "Direct link to Update map styles")

The same update API is available via `MapStyleContext`:

```swift
let styleContext = MapStyleContext()

styleContext.checkForUpdate { status in
    if status != .upToDate {
        styleContext.update(withAllowCellularNetwork: false) { success in
            print("Style update result: \(success)")
        }
    }
}

```
