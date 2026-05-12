# Speed warnings

This guide explains how to configure speed limit monitoring and receive notifications about speed violations and speed limit changes.

The SDK monitors and notifies you about speed limits and violations. You can configure alerts for when a user exceeds the speed limit, when the speed limit changes on a new road segment, and when the user returns to a normal speed range. Thresholds for speed violations can be adjusted independently for city and non-city areas.

## Configure speed limit callbacks[​](#configure-speed-limit-callbacks "Direct link to Configure speed limit callbacks")

Implement the speed-related `AlarmContextDelegate` methods:

```swift
func alarmContext(_ alarmContext: AlarmContext, onHighSpeed limit: Double, insideCityArea: Bool) {
    if insideCityArea {
        print("Speed limit exceeded inside city area — limit: \(limit) m/s")
    } else {
        print("Speed limit exceeded outside city area — limit: \(limit) m/s")
    }
}

func alarmContext(_ alarmContext: AlarmContext, onSpeedLimit speed: Double, limit: Double, insideCityArea: Bool) {
    if insideCityArea {
        print("New speed limit \(limit) m/s inside city area. Current speed: \(speed) m/s")
    } else {
        print("New speed limit \(limit) m/s outside city area. Current speed: \(speed) m/s")
    }
}

func alarmContext(_ alarmContext: AlarmContext, onNormalSpeed limit: Double, insideCityArea: Bool) {
    if insideCityArea {
        print("Normal speed restored inside city area — limit: \(limit) m/s")
    } else {
        print("Normal speed restored outside city area — limit: \(limit) m/s")
    }
}

```

The `onHighSpeed` callback is continuously triggered while the user exceeds the speed limit by more than the configured threshold.

The `onSpeedLimit` callback fires when the road segment speed limit changes.

The `onNormalSpeed` callback fires when the user's speed returns within the limit.

> 📝 **INFO**
>
> Although the parameter is named `insideCityArea`, it refers to areas with generally lower speed limits, such as cities, towns, villages, or similar settlements, regardless of their classification.

> ⚠️ **WARNING**
>
> The `limit` value provided to `onSpeedLimit` will be `0` if the matched road section has no speed limit available or if no road could be found.

## Set speed threshold[​](#set-speed-threshold "Direct link to Set speed threshold")

Configure the overspeed threshold using `setOverSpeedThreshold(_:insideCityArea:)`:

```swift
// Trigger onHighSpeed when speed limit is exceeded by 1 m/s inside a city area
alarmContext?.setOverSpeedThreshold(1, insideCityArea: true)

// Trigger onHighSpeed when speed limit is exceeded by 3 m/s outside a city area
alarmContext?.setOverSpeedThreshold(3, insideCityArea: false)

```

## Get speed threshold[​](#get-speed-threshold "Direct link to Get speed threshold")

Retrieve the currently configured threshold using `getOverSpeedThreshold(_:)`:

```swift
let thresholdInsideCity = alarmContext?.getOverSpeedThreshold(true) ?? 0
let thresholdOutsideCity = alarmContext?.getOverSpeedThreshold(false) ?? 0

```
