# Background location

Learn how to enable location updates while your app is in background on iOS.

## Step 1: Configure Info.plist[​](#step-1-configure-infoplist "Direct link to Step 1: Configure Info.plist")

Add permission and background modes:

```xml
<key>NSLocationAlwaysAndWhenInUseUsageDescription</key>
<string>Location is needed for map localization and navigation.</string>

<key>UIBackgroundModes</key>
<array>
    <string>location</string>
    <string>processing</string>
</array>

```

## Step 2: Enable background updates in data source configuration[​](#step-2-enable-background-updates-in-data-source-configuration "Direct link to Step 2: Enable background updates in data source configuration")

```swift
let source = DataSourceContext()
let config = DataSourceConfigurationObject()

config.setAllowBackgroundLocationUpdates(true)

_ = source.setConfiguration(config, for: .position)
_ = source.start()

```

> ⚠️ **WARNING**
>
> Make sure `setAllowBackgroundLocationUpdates(true)` is called only if the `Info.plist` is properly configured.

## Step 3: Continue consuming updates[​](#step-3-continue-consuming-updates "Direct link to Step 3: Continue consuming updates")

Use your existing `PositionContext` flow in the same way as foreground mode:

```swift
let positionContext = PositionContext(context: source)
positionContext.startUpdatingPositionDelegate(.position)

```
