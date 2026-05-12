# Display Overlays

Overlays provide enhanced, layered information on top of your base map. They offer dynamic, interactive, and customized data that adds contextual value to map elements.

## Get available overlays[​](#get-available-overlays "Direct link to Get available overlays")

Use `OverlayServiceContext` to retrieve the current overlay catalog.

```swift
let overlayService = OverlayServiceContext()

if let collection = overlayService.getAvailableOverlays() {
    for overlay in collection.getOverlays() {
        print("overlay id=\(overlay.getUid()) name=\(overlay.getName())")
    }
}

```

Disable overlays by applying a custom map style with certain overlays disabled, or by using the `disableOverlay` method:

## Disable and enable overlays[​](#disable-and-enable-overlays "Direct link to Disable and enable overlays")

Disable or enable whole overlays by UID.

```swift
let overlayService = OverlayServiceContext()
let publicTransportOverlayId = Int32(CommonOverlayIdentifier.publicTransport.rawValue)

let disableResult = overlayService.disableOverlay(publicTransportOverlayId)
print("disable result: \(disableResult)")

let enableResult = overlayService.enableOverlay(publicTransportOverlayId)
print("enable result: \(enableResult)")

```

Disable a specific category by providing both overlay and category IDs:

```swift
if let overlays = overlayService.getAvailableOverlays(),
   let overlay = overlays.getOverlayAt(0),
   let category = overlay.getCategories().first {

    _ = overlayService.disableOverlay(overlay.getUid(), category: category.getUid())
}

```

> 🚨 **DANGER**
>
> To disable or enable an overlay the SDK must be online. If the SDK is offline, the methods will return `SDKErrorCodeNotFound`. Check with `isOnlineConnection()` from `GEMSdk` before calling these methods.

## Check overlay state[​](#check-overlay-state "Direct link to Check overlay state")

```swift
let enabled = overlayService.isOverlayEnabled(publicTransportOverlayId)
print("overlay enabled: \(enabled)")

```

## Select overlay items on map[​](#select-overlay-items-on-map "Direct link to Select overlay items on map")

Use cursor-based selection from `MapViewController`:

```swift
let selectedOverlays = mapViewController.getCursorSelectionOverlayItems()

for item in selectedOverlays {
    print("item id=\(item.getUid()) name=\(item.getName())")
}

```

You can also handle overlay selection in `MapViewControllerDelegate` using `didSelectOverlays` callbacks.

