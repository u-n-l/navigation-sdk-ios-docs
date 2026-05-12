# Show location on map

Learn how to follow the current position, tune follow-position behavior, and customize the position tracker on the map.

When the live data source is active and location permission is granted, the SDK renders the position tracker on the map. In follow mode, the camera keeps the tracker in view and can rotate with heading depending on your follow settings.

> 📝 **INFO**
>
> GPS quality can vary depending on the environment. Indoors, urban canyons, tunnels, or areas with poor sky visibility can reduce accuracy and make the tracker appear less stable, especially while stationary.

## Start and stop follow position[​](#start-and-stop-follow-position "Direct link to Start and stop follow position")

Use `MapViewController` follow APIs:

```swift
mapViewController.startFollowingPosition(withAnimationDuration: 700, zoomLevel: -1, viewAngle: 0) { success in
    print("following started: \(success)")
}

// ...

mapViewController.stopFollowingPosition()

```

Use `zoomLevel: -1` for automatic zoom while following. The `viewAngle` argument controls tilt at follow start.

Follow mode can also be exited by user gestures (depending on your follow preferences). To stop following programmatically and restore touch-modified camera values, use `stopFollowingPosition(withRestoreCameraMode:)`.

## Follow mode behavior[​](#follow-mode-behavior "Direct link to Follow mode behavior")

Use these APIs to inspect and manage follow state during runtime:

| API                                                                  | What it tells you                                                      |
| -------------------------------------------------------------------- | ---------------------------------------------------------------------- |
| `isFollowingPosition()`                                              | Whether follow mode is currently active                                |
| `isFollowingPositionTouchHandlerModified()`                          | Whether the user changed follow camera values via gestures             |
| `isDefaultFollowingPosition()`                                       | Whether follow mode is in default auto-zoom behavior                   |
| `restoreFollowingPosition(withAnimationDuration:completionHandler:)` | Returns from touch-modified follow behavior to default follow behavior |

## Configure follow preferences[​](#configure-follow-preferences "Direct link to Configure follow preferences")

`FollowPositionPreferencesContext` lets you control behavior during follow mode.

```swift
let preferences = mapViewController.getPreferences()
let follow = preferences.getFollowPositionPreferences()

follow.setMapRotationMode(.positionHeading, angle: 0, objectFollowMap: true)
follow.setTouchHandlerExitAllow(true)
follow.setTouchHandlerModifyPersistent(true)
follow.setCameraFocus(CGPoint(x: 0.5, y: 0.5))
follow.setAccuracyCircleVisibility(true)

```

The follow preferences only affect camera behavior while follow mode is active.

| Preference                                     | Purpose                                                                                                           |
| ---------------------------------------------- | ----------------------------------------------------------------------------------------------------------------- |
| `setMapRotationMode(_:angle:objectFollowMap:)` | Chooses heading-based, compass-based, or fixed map rotation and controls whether the tracker rotates with the map |
| `setCameraFocus(_:)`                           | Places the tracker at a relative viewport position (`(0,0)` top-left, `(1,1)` bottom-right)                       |
| `setTouchHandlerExitAllow(_:)`                 | Controls whether gestures can exit follow mode                                                                    |
| `setTouchHandlerModifyPersistent(_:)`          | Keeps or resets user gesture changes across follow sessions                                                       |
| `setViewAngle(_:)`                             | Sets vertical camera angle used in follow mode                                                                    |
| `setZoomLevel(_:animationDuration:)`           | Sets follow zoom (`-1` means automatic zoom)                                                                      |
| `setAccuracyCircleVisibility(_:)`              | Shows or hides the position accuracy circle                                                                       |
| `setTimeBeforeTurnPresentation(_:)`            | Sets lead time before turn presentation                                                                           |

Set `setTouchHandlerModifyPersistent(true)` when you want gesture-adjusted follow zoom/angle to remain active the next time follow mode starts.

Set `setTouchHandlerExitAllow(false)` when you want follow mode to remain locked until you explicitly stop it in code.

## Customize the position tracker[​](#customize-the-position-tracker "Direct link to Customize the position tracker")

Use the default tracker style and apply visual adjustments such as scale, visibility, and accuracy-circle color.

```swift
mapViewController.setDefaultPositionTracker()
mapViewController.setPositionTrackerScaleFactor(1.0)
mapViewController.setPositionTrackerVisibility(true)
mapViewController.setPositionTrackerAccuracyCircleColor(.systemBlue.withAlphaComponent(0.2))

```

`1.0` is the default scale. For robust UI behavior, keep the scale in `(0, mapViewController.getPositionTrackerMaxScaleFactor()]`.

Use a custom image texture for the tracker:

```swift
if let image = UIImage(named: "navArrow"),
   let png = image.pngData() {
    mapViewController.customizePositionTracker(png)
}

```

Multiple tracker instances are not supported on one map view. If you need to return to SDK defaults after customization, call `setDefaultPositionTracker()` again.
