# Base Entities

This page covers the fundamental building blocks of the iOS SDK: coordinates, paths, and geographic areas.

## Coordinates[​](#coordinates "Direct link to Coordinates")

`CoordinatesObject` represents a geographic point in WGS84 using latitude, longitude, and optional altitude. The Navigation SDK for iOS uses the [WGS](https://en.wikipedia.org/wiki/World_Geodetic_System) coordinates standard.

**Key components:**

* **Latitude**: North-south position, valid range `-90.0 ... +90.0`
* **Longitude**: East-west position, valid range `-180.0 ... +180.0`
* **Altitude**: Height in meters (optional)

### Create coordinates[​](#create-coordinates "Direct link to Create coordinates")

Create a `CoordinatesObject` using the factory method with latitude and longitude:

```swift
let eiffelTower = CoordinatesObject.coordinates(withLatitude: 48.858844, longitude: 2.294351)

```

To include altitude:

```swift
let withAltitude = CoordinatesObject.coordinates(withLatitude: 48.858844, longitude: 2.294351, altitude: 324.0)

```

### Validate coordinates[​](#validate-coordinates "Direct link to Validate coordinates")

Use `isValid` to confirm a coordinate object is usable before passing it into other SDK calls:

```swift
if eiffelTower.isValid {
    // safe to use
}

```

### Calculate distance[​](#calculate-distance "Direct link to Calculate distance")

`getDistance(_:)` calculates the distance in meters between two `CoordinatesObject` instances. It computes the Haversine (great-circle) distance — the shortest path over the Earth's surface — and accounts for altitude if both coordinates carry one.

```swift
let coords1 = CoordinatesObject.coordinates(withLatitude: 48.858844, longitude: 2.294351)
let coords2 = CoordinatesObject.coordinates(withLatitude: 48.854520, longitude: 2.299751)

let distanceInMeters = coords1.getDistance(coords2)

```

This result is the geographic (straight-line) distance, not the road distance that would be followed along a route.

### Calculate azimuth[​](#calculate-azimuth "Direct link to Calculate azimuth")

`getAzimuth(_:)` returns the bearing in degrees from one coordinate to another, calculated using the WGS84 ellipsoid model. A result of `0.0` points north, `90.0` points east, and so on.

```swift
let bearing = coords1.getAzimuth(coords2)

```

> 📝 **INFO**
>
> `getDistance(_:)` and `getAzimuth(_:)` operate on the WGS84 ellipsoid and may exhibit slight inaccuracies over very long distances due to Earth curvature approximation.

> 🚨 **DANGER**
>
> Do not compare coordinates using strict floating-point equality (`==`). Minor variations in representation can cause incorrect results. For example, `48.858395` may be stored internally as `48.858394583109785`. Use a small numerical tolerance (epsilon) for reliable comparisons.

## Path[​](#path "Direct link to Path")

`PathObject` is a core component for representing and managing an ordered sequence of `CoordinatesObject` points. It supports creation from raw coordinates or file data, manipulation, and export.

**Key capabilities:**

* Create a path from raw coordinates or a data buffer (`GPX`, `KML`, `NMEA`, `GeoJSON`, and other supported formats)
* Read path coordinates, waypoints, and metadata
* Compute enclosing area and total length
* Clone complete or partial paths, including in reverse
* Export path data back to file formats

### Supported formats[​](#supported-formats "Direct link to Supported formats")

The `PathFileFormat` enum defines the supported import and export formats:

| Format            | Description                       |
| ----------------- | --------------------------------- |
| `.gpx`            | GPX                               |
| `.kml`            | KML                               |
| `.nmea`           | NMEA                              |
| `.geoJson`        | GeoJSON                           |
| `.latLonTxt`      | Latitude, Longitude text file     |
| `.lonLatTxt`      | Longitude, Latitude text file     |
| `.packedGeometry` | Packed geometry (internal format) |

### Create from coordinates[​](#create-from-coordinates "Direct link to Create from coordinates")

Create a `PathObject` directly from an array of `CoordinatesObject` values:

```swift
let coordinates = [
    CoordinatesObject.coordinates(withLatitude: 40.786, longitude: -74.202),
    CoordinatesObject.coordinates(withLatitude: 40.690, longitude: -74.209),
    CoordinatesObject.coordinates(withLatitude: 40.695, longitude: -73.814),
    CoordinatesObject.coordinates(withLatitude: 40.782, longitude: -73.710),
]

let path = PathObject(coordinates: coordinates)

```

### Create from data[​](#create-from-data "Direct link to Create from data")

Create a `PathObject` from file data in a supported format:

```swift
let data: Data = ... // GPX file contents
let path = PathObject(dataBuffer: data, format: .gpx)

```

### Get coordinate at position[​](#get-coordinate-at-position "Direct link to Get coordinate at position")

Retrieve the coordinate at a normalized position along the path (0.0 = start, 1.0 = end):

```swift
if let midPoint = path.getCoordinatesAtPercent(0.5) {
    // midPoint is a CoordinatesObject at the halfway mark
}

```

### Export path data[​](#export-path-data "Direct link to Export path data")

`exportAs(_:)` returns the path as `Data?` in the requested format:

```swift
if let exportedData = path.exportAs(.geoJson) {
    // write exportedData to disk or share it
}

```

### Main operations[​](#main-operations "Direct link to Main operations")

| Method                          | Description                               |
| ------------------------------- | ----------------------------------------- |
| `init(coordinates:)`            | Creates a path from coordinate list       |
| `init(dataBuffer:format:)`      | Creates a path from file data             |
| `getCoordinates()`              | Returns the internal coordinates          |
| `getWayPoints()`                | Returns waypoint indices                  |
| `getArea()`                     | Returns path bounding rectangle           |
| `getLength()`                   | Returns path length in meters             |
| `getName()` / `setName(_:)`     | Gets or sets the path name                |
| `cloneStartEnd(_:endLocation:)` | Clones sub-path between coordinates       |
| `cloneReverse()`                | Clones reversed path                      |
| `exportAs(_:)`                  | Exports path as `Data?` in chosen format  |
| `getCoordinatesAtPercent(_:)`   | Returns coordinate at normalized progress |

## Geographic areas[​](#geographic-areas "Direct link to Geographic areas")

Geographic areas represent specific regions used for map framing, search restrictions, geofencing, and spatial checks. Multiple SDK entities return a bounding box as a geographic area.

**Available types:**

* **Rectangle geographic area** — Rectangular area with sides aligned to longitude and latitude lines
* **Circle geographic area** — Area around a center point with a specified radius in meters
* **Polygon geographic area** — Complex area with high precision for detailed boundaries

The abstract base class is `GeographicAreaObject`. The `GeographicAreaType` enum identifies which subtype a given instance is:

| Type              | Description          |
| ----------------- | -------------------- |
| `.undefined`      | No type set          |
| `.circle`         | Circle area          |
| `.rectangle`      | Rectangle area       |
| `.polygon`        | Polygon area         |
| `.tileCollection` | Tile-collection area |

### Base operations[​](#base-operations "Direct link to Base operations")

| Method                    | Description                                             |
| ------------------------- | ------------------------------------------------------- |
| `getType()`               | Returns the `GeographicAreaType` of this area           |
| `containsCoordinates(_:)` | Checks if a point is inside the area                    |
| `getCenterPoint()`        | Returns the geographic center                           |
| `equals(_:)`              | Compares with another area                              |
| `isDefault()`             | Checks if the area is in its default (unmodified) state |
| `isEmpty()`               | Checks if the area is empty                             |
| `reset()`                 | Resets to default                                       |

### Rectangle geographic area[​](#rectangle-geographic-area "Direct link to Rectangle geographic area")

`RectangleGeographicAreaObject` represents an axis-aligned bounding rectangle. You can create one from a center point and radii, or by setting the top-left and bottom-right corners explicitly.

Create from a center point with horizontal and vertical radii in meters:

```swift
let center = CoordinatesObject.coordinates(withLatitude: 48.858844, longitude: 2.294351)
let rect = RectangleGeographicAreaObject(location: center, horizontalRadius: 500.0, verticalRadius: 300.0)

```

Alternatively, set corners directly after construction:

```swift
let topLeft = CoordinatesObject.coordinates(withLatitude: 44.93343, longitude: 25.09946)
let bottomRight = CoordinatesObject.coordinates(withLatitude: 44.93324, longitude: 25.09987)

let rect = RectangleGeographicAreaObject(location: topLeft, horizontalRadius: 0, verticalRadius: 0)
rect.setTopLeft(topLeft)
rect.setBottomRight(bottomRight)

```

> 🚨 **DANGER**
>
> A valid `RectangleGeographicAreaObject` requires the latitude of `topLeft` to be greater than the latitude of `bottomRight`, and the longitude of `topLeft` to be less than the longitude of `bottomRight`.

Additional operations:

* `intersects(_:)` and `contains(_:)` for rectangle overlap/containment checks
* `makeIntersection(_:)` and `makeUnion(_:)` to produce new rectangles from geometry composition
* `getBoundingBox()` to retrieve the normalized enclosing rectangle

### Circle geographic area[​](#circle-geographic-area "Direct link to Circle geographic area")

`CircleGeographicAreaObject` represents a radial area defined by a center point and a radius in meters.

```swift
let center = CoordinatesObject.coordinates(withLatitude: 40.748817, longitude: -73.985428)
let circle = CircleGeographicAreaObject(center: center, radius: 500)

```

You can also update the center and radius after creation:

```swift
circle.setCenter(newCenter, radius: 1000)

```

### Polygon geographic area[​](#polygon-geographic-area "Direct link to Polygon geographic area")

`PolygonGeographicAreaObject` represents a custom area defined by an arbitrary list of coordinates. It is suited for complex or irregular boundaries.

```swift
let coordinates = [
    CoordinatesObject.coordinates(withLatitude: 10, longitude: 0),
    CoordinatesObject.coordinates(withLatitude: 10, longitude: 10),
    CoordinatesObject.coordinates(withLatitude: 0,  longitude: 10),
    CoordinatesObject.coordinates(withLatitude: 0,  longitude: 0),
]

let polygon = PolygonGeographicAreaObject(coordinates: coordinates)

```

> 🚨 **DANGER**
>
> A valid `PolygonGeographicAreaObject` requires at least 3 coordinates. Avoid overlapping or self-intersecting edges.

***

These entities are used throughout landmarks, overlays, routing, traffic, and navigation instructions.
