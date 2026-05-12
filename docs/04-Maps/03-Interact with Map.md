# Interact with map

Use `MapViewControllerDelegate`, cursor selection APIs, and map-view preferences to handle user interactions, gestures, selections, and map render updates.

## Understand gesture and event handling[​](#understand-gesture-and-event-handling "Direct link to Understand gesture and event handling")

The map handles common gestures (tap, double-tap, pan, pinch, rotate, shove) natively. To react to user interaction, implement callbacks in `MapViewControllerDelegate`.

| Interaction    | Delegate callback (`MapViewControllerDelegate`)                   |
| -------------- | ----------------------------------------------------------------- |
| Tap            | `mapViewController:onTouchPoint:`                                 |
| Long press     | `mapViewController:onLongTouchPoint:`                             |
| Double tap     | `mapViewController:onDoubleTouch:`                                |
| Two-finger tap | `mapViewController:onTwoTouches:`                                 |
| Pan            | `mapViewController:onMovePoint:toPoint:`                          |
| Pinch          | `mapViewController:onPinch:startPoint2:toPoint1:toPoint2:center:` |
| Shove          | `mapViewController:onShove:initial:start:end:`                    |

Gesture behavior explained:

* **Tap**: Tap the screen with one finger. This gesture does not have a predefined map action.
* **Long press**: Press and hold one finger on the screen. This gesture does not have a predefined map action.
* **Double tap**: Zoom the map in by a fixed amount by tapping twice with one finger.
* **2 finger tap**: Align the map to north by tapping the screen with two fingers.
* **Pan**: Move the map by pressing and holding one finger, then dragging in any direction. The map continues with slight momentum after release.
* **Pinch**: Zoom in or out continuously by changing the distance between two fingers. You can also rotate continuously by changing the angle between two fingers.
* **Shove**: With two fingers on the map, move both fingers together in parallel (typically up or down) to change the camera tilt angle while keeping the map center stable.

> 📝 **INFO**
>
> Gesture enable/disable is controlled per map using `MapViewPreferencesContext`.

## Enable and disable gestures[​](#enable-and-disable-gestures "Direct link to Enable and disable gestures")

```swift
let preferences = mapViewController.getPreferences()

_ = preferences.enableTouchGesture(.onMove, enable: false)
_ = preferences.enableTouchGesture(.onPinch, enable: true)

let enabledGestures = MapViewTouchGestures.onRotate.rawValue | MapViewTouchGestures.onShove.rawValue
_ = preferences.enableTouchGestures(Int32(enabledGestures), enable: true)

let rotateEnabled = preferences.isTouchGestureEnabled(.onRotate)
print("Rotate gesture enabled: \(rotateEnabled)")

```

## Select map elements[​](#select-map-elements "Direct link to Select map elements")

Use cursor-based selection when you already have a screen point (for example from your own gesture recognizer).

```swift
func selectElements(at point: CGPoint, mapViewController: MapViewController) {
    mapViewController.setCursorPosition(point)

    let landmarks = mapViewController.getCursorSelectionLandmarks()
    let streets = mapViewController.getCursorSelectionStreets()
    let overlays = mapViewController.getCursorSelectionOverlayItems()
    let routes = mapViewController.getCursorSelectionRoutes()
    let trafficEvents = mapViewController.getCursorSelectionTrafficEvents()

    print("landmarks=\(landmarks.count) streets=\(streets.count) overlays=\(overlays.count)")
    print("routes=\(routes.count) traffic=\(trafficEvents.count)")
}

```

You can also receive selection directly from delegate callbacks such as:

| Selection type | Delegate callback (`MapViewControllerDelegate`)                                                                                                                         |
| -------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Landmarks      | `mapViewController:didSelectLandmarks:onTouchPoint:` and `mapViewController:didSelectLandmarks:onLongTouchPoint:`                                                       |
| Streets        | `mapViewController:didSelectStreets:onTouchPoint:` and `mapViewController:didSelectStreets:onLongTouchPoint:`                                                           |
| Routes         | `mapViewController:didSelectRoutes:onTouchPoint:`                                                                                                                       |
| Overlays       | `mapViewController:didSelectOverlays:`, `mapViewController:didSelectOverlays:onTouchPoint:`, and `mapViewController:didSelectOverlays:onLongTouchPoint:`                |
| Traffic events | `mapViewController:didSelectTrafficEvents:`, `mapViewController:didSelectTrafficEvents:onTouchPoint:`, and `mapViewController:didSelectTrafficEvents:onLongTouchPoint:` |

> 🚨 **DANGER**
>
> Avoid heavy work inside touch or render callbacks. Long-running operations can reduce map responsiveness.

## Observe render and camera movement[​](#observe-render-and-camera-movement "Direct link to Observe render and camera movement")

Use render callbacks to react to loading completion or camera movement status.

```swift
mapViewController.setOnMapViewRendered { dataStatus, cameraStatus in
    if dataStatus == .complete && cameraStatus == .stationary {
        print("Map is fully rendered and camera is stationary")
    }
}

```

You can also use delegate methods:

* `mapViewController:willRender:`
* `mapViewController:didRender:cameraTransitionStatus:`

## Capture the map as an image[​](#capture-the-map-as-an-image "Direct link to Capture the map as an image")

```swift
let targetSize = CGSize(width: 1080, height: 1920)

// Use CGRect.zero to capture the entire map view
let image = mapViewController.snapshotImage(withSize: targetSize, captureRect: .zero)

if image == nil {
    print("Could not capture map image")
}

```

> 🚨 **DANGER**
>
> Capturing the map view may not work correctly when map rendering is disabled.

