# Camera feed

The SDK `DataSourceContext` provides access to camera frames for both live camera feeds and recorded logs.

Learn how to render those frames using `CameraRenderViewController`.

> 💡 **TIP**
>
> Recorded logs that include camera data are stored as `.mp4` files and can be played with standard iOS media players (for example `AVPlayer`). For simple log playback scenarios, a system player is often the best option.

## Create a camera render view[​](#create-a-camera-render-view "Direct link to Create a camera render view")

`CameraRenderViewController` is the UI component that renders camera frames on screen.

Initialize it with a data source context:

```swift
let source = DataSourceContext()
let cameraViewController = CameraRenderViewController(context: source)

```

You can also use `init(context:ppi:scale:)` when you need explicit control over rendering parameters tied to the device display.

> 🚨 **DANGER**
>
> The `DataSourceContext` used by `CameraRenderViewController` must provide camera data. If camera data is missing, rendering cannot start.

> 🚨 **DANGER**
>
> For live camera feed, ensure a video recording session is active for the same data source context.

Add it like a standard child view controller:

```swift
addChild(cameraViewController)
view.addSubview(cameraViewController.view)
cameraViewController.didMove(toParent: self)

```

## Start and stop camera feed[​](#start-and-stop-camera-feed "Direct link to Start and stop camera feed")

Use `startCamera()` and `startRender()` to begin camera acquisition and frame rendering. Stop both when the feed is no longer needed.

```swift
_ = cameraViewController.startCamera()
cameraViewController.startRender()

// ...

cameraViewController.stopRender()
_ = cameraViewController.stopCamera()

```

## Properties[​](#properties "Direct link to Properties")

Use these values and methods as the runtime state surface for `CameraRenderViewController`:

* `DataSourceContext` (constructor input) - Source of live or recorded camera frames used by the renderer
* `view` - The `UIView` surface where frames are displayed
* `getFrameFit()` - Current `FrameFit` used to scale camera frames in the view
* `isRenderActive()` - Indicates whether rendering is currently active
* `startCamera()` / `stopCamera()` return value - Boolean result showing whether camera start/stop succeeded

## Control frame fitting[​](#control-frame-fitting "Direct link to Control frame fitting")

`setFrameFit(_:)` controls how camera frames are scaled in the view. Read the current value with `getFrameFit()`.

```swift
cameraViewController.setFrameFit(.inside)
let fit = cameraViewController.getFrameFit()
print("frame fit = \(fit)")

```

### Observe playback progress for logs[​](#observe-playback-progress-for-logs "Direct link to Observe playback progress for logs")

When rendering camera frames from recorded logs, use the progress callback to receive playback time updates in milliseconds.

```swift
cameraViewController.setOnLogProgressCallback { timeMs in
    print("camera log progress: \(timeMs) ms")
}

```

## Lifecycle management[​](#lifecycle-management "Direct link to Lifecycle management")

Manage `CameraRenderViewController` explicitly to avoid camera/render resource leaks.

Recommended flow:

1. Create and attach the controller as a child view controller.
2. Start camera and render when the feed is needed.
3. Stop render and camera when the feed is no longer visible.
4. Call `destroy()` before disposal.

### Cleanup[​](#cleanup "Direct link to Cleanup")

Destroy the camera render controller when it is no longer needed to release rendering and camera resources.

```swift
cameraViewController.destroy()

```
