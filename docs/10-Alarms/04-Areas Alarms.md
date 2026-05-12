# Areas alarms

Trigger operations when users enter or exit defined geographic areas using the `AlarmContext`.

## Add areas to monitor[​](#add-areas-to-monitor "Direct link to Add areas to monitor")

Define geographic areas and call `monitorArea(_:areaId:)` on your `AlarmContext` instance. You can monitor three types: `RectangleGeographicAreaObject`, `CircleGeographicAreaObject`, and `PolygonGeographicAreaObject`.

Assign a unique string identifier to each area so you can determine which zone a user has entered or exited:

```swift
let topLeft = CoordinatesObject.coordinates(withLatitude: 1, longitude: 0.5)
let bottomRight = CoordinatesObject.coordinates(withLatitude: 0.5, longitude: 1)
let rect = RectangleGeographicAreaObject(topLeftLocation: topLeft, bottomRightLocation: bottomRight)

let center = CoordinatesObject.coordinates(withLatitude: 1, longitude: 0.5)
let circle = CircleGeographicAreaObject(center: center, radius: 100)

let points = [
    CoordinatesObject.coordinates(withLatitude: 1, longitude: 0.5),
    CoordinatesObject.coordinates(withLatitude: 0.5, longitude: 1),
    CoordinatesObject.coordinates(withLatitude: 1, longitude: 1),
    CoordinatesObject.coordinates(withLatitude: 1, longitude: 0.5), // close the polygon
]
let polygon = PolygonGeographicAreaObject(coordinates: points)

alarmContext?.monitorArea(rect, areaId: "areaRect")
alarmContext?.monitorArea(circle, areaId: "areaCircle")
alarmContext?.monitorArea(polygon, areaId: "areaPolygon")

```

> 💡 **TIP**
>
> When defining a `PolygonGeographicAreaObject`, always close the shape by making the first and last coordinates identical. Otherwise, the SDK may not match the polygon you provided.

## Unmonitor an area[​](#unmonitor-an-area "Direct link to Unmonitor an area")

Remove a monitored area by passing the same `GeographicAreaObject` instance used in `monitorArea(_:areaId:)`:

```swift
alarmContext?.unmonitorArea(rect)

```

Remove multiple areas by passing an array of their identifiers:

```swift
alarmContext?.unmonitorAreas(["areaRect", "areaCircle"])

```

Remove all monitored areas:

```swift
alarmContext?.unmonitorAllAreas()

```

## Get notified when users enter or exit areas[​](#get-notified-when-users-enter-or-exit-areas "Direct link to Get notified when users enter or exit areas")

Implement `alarmContext(onBoundaryCrossed:enteredArea:exitedAreas:)` in your `AlarmContextDelegate`. The callback provides arrays of `AlarmMonitoredAreaObject` for entered and exited areas:

```swift
func alarmContext(onBoundaryCrossed alarmContext: AlarmContext,
                  enteredArea arrayIn: [AlarmMonitoredAreaObject],
                  exitedAreas arrayOut: [AlarmMonitoredAreaObject]) {
    for area in arrayIn {
        print("Entered area: \(area.getMonitorIdentifier())")
    }
    for area in arrayOut {
        print("Exited area: \(area.getMonitorIdentifier())")
    }
}

```

Retrieve the geographic area from an `AlarmMonitoredAreaObject` using `getMonitorGeographicArea()`.
