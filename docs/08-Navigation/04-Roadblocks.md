# Roadblocks

This guide explains how to add, manage, and remove roadblocks to customize route planning and navigation.

A **roadblock** is a user-defined restriction applied to a specific road segment or geographic area. It reflects traffic disruptions such as construction, closures, or areas to avoid, and influences route planning by marking certain paths or zones as unavailable for navigation.

Roadblocks can be **path-based** (defined by a sequence of coordinates) or **area-based** (covering a geographic region). They may be **temporary** (active only during navigation) or **persistent** (survive SDK uninitialization), depending on their intended duration.

The `TrafficEventObject` class represents roadblocks. Roadblocks are managed through the `TrafficContext` class.

While some roadblocks are provided in real time by online data from UNL servers, you can also define your own **user roadblocks** to customize routing behavior.

If the applied style includes traffic data and traffic display is enabled, a visual indication of the blocked portion will appear on the map, highlighted in red.

## Configure traffic usage[​](#configure-traffic-usage "Direct link to Configure traffic usage")

Traffic behavior can be customized through the `TrafficContext` class. The `TrafficUsage` enum offers the following options:

| Value                    | Description                                                       |
| ------------------------ | ----------------------------------------------------------------- |
| `TrafficUsageUseNone`    | Disables all traffic data usage                                   |
| `TrafficUsageUseOnline`  | Uses both online and offline traffic data (default)               |
| `TrafficUsageUseOffline` | Uses only offline traffic data, including user-defined roadblocks |

## Add temporary roadblock during navigation[​](#add-temporary-roadblock-during-navigation "Direct link to Add temporary roadblock during navigation")

You can add a roadblock to bypass a portion of the current route for a specified distance. The route is recalculated automatically and the updated route is returned via the `navigationContext(_:navigationRouteUpdated:)` delegate method.

To add a 100-meter roadblock starting 10 meters ahead:

```swift
// Using NavigationContext
navigationContext?.setRoadBlockWithLength(100, starting: 10)

```

Roadblocks added through `setRoadBlockWithLength(_:starting:)` only affect the ongoing navigation session.

## Check traffic information availability[​](#check-traffic-information-availability "Direct link to Check traffic information availability")

Use `getOnlineServiceRestrictions(_:)` on `TrafficContext` to check if traffic events are available for a geographic position. It takes a `CoordinatesObject` and returns a `TrafficOnlineRestrictions` value:

```swift
let trafficContext = TrafficContext()
let coords = CoordinatesObject.coordinates(withLatitude: 50.108, longitude: 8.783)
let restriction = trafficContext.getOnlineServiceRestrictions(coords)
print("Traffic restriction: \(restriction.rawValue)")

```

The `TrafficOnlineRestrictions` enum provides the following values:

* `TrafficOnlineRestrictionsNone` — No restrictions; online traffic is available
* `TrafficOnlineRestrictionsSettings` — Online traffic is disabled in preferences
* `TrafficOnlineRestrictionsConnection` — No internet connection is available
* `TrafficOnlineRestrictionsNetworkType` — Not allowed on extra charged networks (e.g., roaming)
* `TrafficOnlineRestrictionsProviderData` — Required provider data is missing
* `TrafficOnlineRestrictionsWorldMapVersion` — The world map version is outdated and incompatible. Update the road map
* `TrafficOnlineRestrictionsDiskSpace` — Insufficient disk space to store traffic data
* `TrafficOnlineRestrictionsInitFail` — Failed to initialize the traffic service

## Add area-based persistent roadblock[​](#add-area-based-persistent-roadblock "Direct link to Add area-based persistent roadblock")

Use `addPersistentRoadblock(_:startTime:expireTime:transportMode:identifier:)` to add **area-based** user roadblocks. It accepts a `CircleGeographicAreaObject` representing the area to be avoided.

Construct a `TimeObject` from a Unix timestamp in milliseconds using `fromInt(_:)`:

```swift
let trafficContext = TrafficContext()

let center = CoordinatesObject.coordinates(withLatitude: 46.764942, longitude: 7.122563)
let area = CircleGeographicAreaObject(center: center, radius: 300) // 300 m radius

let startTime = TimeObject()
startTime.fromInt(Int(Date().timeIntervalSince1970 * 1000))

let expireTime = TimeObject()
expireTime.fromInt(Int(Date().addingTimeInterval(3600).timeIntervalSince1970 * 1000))

if let event = trafficContext.addPersistentRoadblock(area,
                                                     startTime: startTime,
                                                     expireTime: expireTime,
                                                     transportMode: .car,
                                                     identifier: "test_area_id") {
    print("Area roadblock added: \(event.getDescription())")
} else {
    print("Failed to add area roadblock")
}

```

> ⚠️ **WARNING**
>
> The following conditions apply when adding a persistent roadblock:
>
> * If a roadblock already exists at the same location, the addition will fail and return `nil`
> * If the input parameters are invalid (e.g., `expireTime` is earlier than `startTime`, missing `identifier`, or invalid area object), the addition will fail

## Add path-based persistent roadblock[​](#add-path-based-persistent-roadblock "Direct link to Add path-based persistent roadblock")

Use `addPersistentRoadblock(_:startTime:expireTime:transportMode:)` (the array overload) to add **path-based** user roadblocks. It accepts an array of `CoordinatesObject` instances:

* **Single coordinate** — Defines a **point-based** roadblock. This may result in two roadblocks created — one for each travel direction.
* **Multiple coordinates** — Defines a **path-based** roadblock from the first to the last coordinate, restricting access along a specific road segment.

```swift
let coords = [CoordinatesObject.coordinates(withLatitude: 45.64695, longitude: 25.62070)]

let startTime = TimeObject()
startTime.fromInt(Int(Date().timeIntervalSince1970 * 1000))

let expireTime = TimeObject()
expireTime.fromInt(Int(Date().addingTimeInterval(3600).timeIntervalSince1970 * 1000))

if let event = trafficContext.addPersistentRoadblock(coords,
                                                     startTime: startTime,
                                                     expireTime: expireTime,
                                                     transportMode: .car) {
    print("Path roadblock added: \(event.getDescription())")
} else {
    print("Failed to add path roadblock — check coordinates and road data availability")
}

```

> ⚠️ **WARNING**
>
> The path-based roadblock may also fail if:
>
> * **No suitable road found** — No valid road can be identified at the specified coordinates, or no road data (online or offline) is available for the given location.
> * **Route computation failed** — If multiple coordinates are provided but a valid route cannot be computed between them.

## Get all persistent roadblocks[​](#get-all-persistent-roadblocks "Direct link to Get all persistent roadblocks")

Use `getPersistentRoadblocks()` to retrieve the list of all active or scheduled persistent roadblocks. Expired roadblocks are automatically removed:

```swift
let roadblocks = trafficContext.getPersistentRoadblocks()
for roadblock in roadblocks {
    print(roadblock.getDescription())
}

```

## Remove roadblocks[​](#remove-roadblocks "Direct link to Remove roadblocks")

### Remove persistent roadblock by coordinates[​](#remove-persistent-roadblock-by-coordinates "Direct link to Remove persistent roadblock by coordinates")

Use `removePersistentRoadblock(_:)` to remove a path-based roadblock by providing the **first** coordinate used to create it:

```swift
let startCoord = CoordinatesObject.coordinates(withLatitude: 45.64695, longitude: 25.62070)
trafficContext.removePersistentRoadblock(startCoord)

```

### Remove roadblock using TrafficEventObject[​](#remove-roadblock-using-trafficeventobject "Direct link to Remove roadblock using TrafficEventObject")

If the `TrafficEventObject` instance is available, remove it using `removeUserRoadblock(_:)`:

```swift
if let event = trafficContext.getPersistentRoadblocks().first {
    trafficContext.removeUserRoadblock(event)
}

```

> 💡 **TIP**
>
> `removeUserRoadblock(_:)` can be used for both persistent and non-persistent roadblocks.

### Remove all persistent roadblocks[​](#remove-all-persistent-roadblocks "Direct link to Remove all persistent roadblocks")

Use `removeAllPersistentRoadblocks()` to delete all existing user-defined roadblocks:

```swift
trafficContext.removeAllPersistentRoadblocks()

```

## Monitor roadblock lifecycle events[​](#monitor-roadblock-lifecycle-events "Direct link to Monitor roadblock lifecycle events")

Implement `PersistentRoadblockDelegate` on `TrafficContext` to receive notifications when roadblocks expire or become active:

```swift
trafficContext.persistentRoadblockDelegate = self
trafficContext.startPersistentRoadblockNotification()

// MARK: - PersistentRoadblockDelegate

func trafficContext(_ trafficContext: TrafficContext,
                    onRoadblocksExpired events: [TrafficEventObject]) {
    for event in events {
        print("Roadblock expired: \(event.getDescription())")
    }
}

func trafficContext(_ trafficContext: TrafficContext,
                    onRoadblocksActivated events: [TrafficEventObject]) {
    for event in events {
        print("Roadblock activated: \(event.getDescription())")
    }
}

```

Call `stopPersistentRoadblockNotification()` when notifications are no longer needed.

