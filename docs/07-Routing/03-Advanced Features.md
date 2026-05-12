# Advanced features

This guide covers advanced routing features including route ranges, path-based routes, public transit routing, and route export.

## Compute route ranges[​](#compute-route-ranges "Direct link to Compute route ranges")

A route range calculates the area reachable from a starting point within a specified distance, time, or energy budget.

To compute a route range:

* Set `setRouteRanges(_:quality:)` on `RoutePreferencesObject` with one or more range values. Measurement units correspond to the route type (see table below).
* Provide only one landmark — the starting point.
* Optionally set `setRouteType(_:)` (default is `RouteTypeFastest`) and `setTransportMode(_:)`.

**Measurement units by route type:**

| Route type          | Units       |
| ------------------- | ----------- |
| `RouteTypeFastest`  | Seconds     |
| `RouteTypeShortest` | Meters      |
| `RouteTypeEconomic` | Wh (energy) |

> 🚨 **DANGER**
>
> Routes computed using route ranges are **not navigable**.

> 🚨 **DANGER**
>
> `RouteTypeScenic` is not supported for route ranges.

Compute two ranges (30 min and 60 min) from a single starting point:

```swift
let start = LandmarkObject.landmark(
    withName: "Paris",
    location: CoordinatesObject.coordinates(withLatitude: 48.85682, longitude: 2.34375)
)

let preferences = RoutePreferencesObject()
preferences.setRouteType(.fastest)
// 30 minutes and 60 minutes in seconds
preferences.setRouteRanges([1800, 3600], quality: 100)

let nav = NavigationContext(preferences: preferences)
nav.calculateRoute(withWaypoints: [start],
    statusHandler: { status in
        // handle status
    },
    completionHandler: { routes, code in
        if code == .kNoError {
            print("Range routes computed: \(routes.count)")
        } else if code == .kCancel {
            print("Computation cancelled")
        } else {
            print("Error: \(code)")
        }
    }
)

```

> 📝 **INFO**
>
> Display computed range routes on the map like regular routes. Use `RouteRenderSettings` to define polygon fill and border colors.

## Compute path-based routes[​](#compute-path-based-routes "Direct link to Compute path-based routes")

A `PathObject` contains a list of coordinates (a track) created from:

* Custom coordinate arrays.
* GPX or other file formats.
* Finger-drawn paths.

Create a **path-backed landmark** using `RouteBookmarksObject.setWaypointTrackData(_:)`, then pass it as a waypoint in `calculateRoute(withWaypoints:)`. The path acts as a hint for the routing algorithm and produces a single result route.

```swift
let coords: [CoordinatesObject] = [
    CoordinatesObject.coordinates(withLatitude: 40.786, longitude: -74.202),
    CoordinatesObject.coordinates(withLatitude: 40.690, longitude: -74.209),
    CoordinatesObject.coordinates(withLatitude: 40.695, longitude: -73.814),
    CoordinatesObject.coordinates(withLatitude: 40.782, longitude: -73.710),
]

let path = PathObject(coordinates: coords)

// Create a path-backed landmark from the PathObject
if let pathLandmark = RouteBookmarksObject.setWaypointTrackData(path) {
    let preferences = RoutePreferencesObject()

    let nav = NavigationContext(preferences: preferences)
    nav.calculateRoute(withWaypoints: [pathLandmark],
        statusHandler: { status in
            // handle status
        },
        completionHandler: { routes, code in
            if code == .kNoError {
                print("Path route computed: \(routes.count)")
            }
        }
    )
}

```

> 💡 **TIP**
>
> Add a path to an existing landmark using `RouteBookmarksObject.setWaypointTrackData(_:path:)`. Extract a path from a landmark using `RouteBookmarksObject.getWaypointTrackData(_:)`.

> 🚨 **DANGER**
>
> When computing a route with both path-backed and non-path-backed landmarks, set `setAccurateTrackMatch(true)` on `RoutePreferencesObject`. Without it, routing fails.

Configure routing engine behavior with `setTrackResume(_:)`:

* **`true`** (default) — Matches the track from the closest coordinate to the last.
* **`false`** — Uses the entire track from first to last coordinate.

## Compute routes from GPX files[​](#compute-routes-from-gpx-files "Direct link to Compute routes from GPX files")

Compute a route from a GPX file by creating a `PathObject` from the file data:

```swift
guard let gpxURL = Bundle.main.url(forResource: "recorded_route", withExtension: "gpx"),
      let gpxData = try? Data(contentsOf: gpxURL) else {
    print("GPX file not found")
    return
}

// Initialize PathObject from GPX data
let path = PathObject(dataBuffer: gpxData, format: .gpx)

if let pathLandmark = RouteBookmarksObject.setWaypointTrackData(path) {
    let preferences = RoutePreferencesObject()
    preferences.setTransportMode(.bicycle)

    let nav = NavigationContext(preferences: preferences)
    nav.calculateRoute(withWaypoints: [pathLandmark],
        statusHandler: { status in
            // handle status
        },
        completionHandler: { routes, code in
            // handle result
        }
    )
}

```

## Compute public transit routes[​](#compute-public-transit-routes "Direct link to Compute public transit routes")

Set the transport mode to `.public` to compute public transit routes:

```swift
let preferences = RoutePreferencesObject()
preferences.setTransportMode(.public)

```

> 🚨 **DANGER**
>
> Public transit routes are not navigable.

Compute and handle a public transit route:

```swift
let departure = LandmarkObject.landmark(
    withName: "Stop A",
    location: CoordinatesObject.coordinates(withLatitude: 45.6646, longitude: 25.5872)
)
let destination = LandmarkObject.landmark(
    withName: "Stop B",
    location: CoordinatesObject.coordinates(withLatitude: 45.6578, longitude: 25.6233)
)

let preferences = RoutePreferencesObject()
preferences.setTransportMode(.public)

let nav = NavigationContext(preferences: preferences)
nav.calculateRoute(withWaypoints: [departure, destination],
    statusHandler: { status in
        // handle status
    },
    completionHandler: { routes, code in
        guard code == .kNoError, let route = routes.first else { return }

        for segment in route.getSegments() {
            if segment.isCommon() {
                // Walking segment — use standard turn instructions
                for instruction in segment.getInstructions() {
                    let turn = instruction.getTurnInstruction()
                    print("Walk: \(turn)")
                }
            } else {
                // Public transit segment — use PT instructions
                for ptInstruction in segment.getPTInstructions() {
                    print("PT Station: \(ptInstruction.getName())")
                }
            }
        }
    }
)

```

A public transit route contains one or more segments. Check `isCommon()` to determine the segment type:

* `false` — Walking segment. Use `getInstructions()` → `[RouteInstructionObject]`.
* `true` — Public transit segment. Use `getPTInstructions()` → `[PTRouteInstructionObject]`.

> 💡 **TIP**
>
> Specify departure or arrival time and additional preferences for public transit:
>
>```swift
>let preferences = RoutePreferencesObject()
>preferences.setTransportMode(.public)
>preferences.setAlgorithmType(.arrival)
>preferences.setSortingStrategy(.bestTime)
>preferences.setUseBikes(false)
>preferences.setUseWheelchair(false)
>// Set a timestamp one hour from now for desired arrival
>let oneHourFromNow = TimeObject(...)
>preferences.setTimestamp(oneHourFromNow)
>
>```

## Export routes to files[​](#export-routes-to-files "Direct link to Export routes to files")

Export a specific route from a `RouteBookmarksObject` to a file on disk using `export(toFile:path:)`:

```swift
let bookmarks = RouteBookmarksObject.init(fileName: "bookmarksFile", folderName: nil)

let exported = bookmarks.export(toFile: 0, path: "/path/to/exported_route")
if exported {
    print("Export successful")
} else {
    print("Export failed — check the index and path")
}

```

**Common failure reasons:**

* Index out of bounds or route does not exist.
* Destination directory does not exist or is not writable.

## Export routes as data[​](#export-routes-as-data "Direct link to Export routes as data")

Export a route to a data buffer in a supported file format using `export(as:)`. The method returns `Data?` containing the full route in the requested format.

```swift
// Export to GPX format
if let gpxData = route.export(as: .gpx) {
    // Write to disk or share as needed
    try? gpxData.write(to: outputURL)
}

```

**Supported `PathFileFormat` values:** `.gpx`, `.kml`, `.nmea`, `.geoJson`, `.packedGeometry`.

