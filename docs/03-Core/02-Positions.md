# Positions

This page covers position handling in iOS using `PositionObject` and `PositionContext`.

> 💡 **TIP**
>
> Do not confuse `CoordinatesObject` with `PositionObject`.
>
> `CoordinatesObject` describes a location only. `PositionObject` includes movement and quality metadata such as speed, course, provider, and map-matching context.

## Create and access positions[​](#create-and-access-positions "Direct link to Create and access positions")

In most app flows, you receive positions from `PositionContext`. For testing and simulation-style pipelines, you can also create a `PositionObject` manually with `createPosition:latitude:longitude:altitude:course:speed:speedAccuracy:horizontalAccuracy:verticalAccuracy:courseAccuracy:`.

```swift
let synthetic = PositionObject.createPosition(
	Date().timeIntervalSince1970 * 1000,
	latitude: 48.858844,
	longitude: 2.294351,
	altitude: 35.0,
	course: 90.0,
	speed: 13.5,
	speedAccuracy: 1.2,
	horizontalAccuracy: 5.0,
	verticalAccuracy: 8.0,
	courseAccuracy: 10.0
)

```

### Start continuous updates[​](#start-continuous-updates "Direct link to Start continuous updates")

Use `PositionContext` to receive continuous updates:

```swift
final class PositionHandler: NSObject, PositionContextDelegate {
	private let positionContext: PositionContext

	init(dataSourceContext: DataSourceContext) {
		self.positionContext = PositionContext(context: dataSourceContext)
		super.init()
		self.positionContext.delegate = self
	}

	func startRawUpdates() {
		positionContext.startUpdatingPositionDelegate(.position)
	}

	func startImprovedUpdates() {
		positionContext.startUpdatingPositionDelegate(.improvedPosition)
	}

	func stopUpdates() {
		positionContext.stopUpdatingPositionDelegate()
	}

	func positionContext(_ positionContext: PositionContext, didUpdatePosition position: PositionObject) {
		guard position.hasCoordinates() else { return }
		let coordinates = position.getCoordinates()
		print("Position: \(coordinates.latitude), \(coordinates.longitude)")
	}
}

```

### Read latest snapshots[​](#read-latest-snapshots "Direct link to Read latest snapshots")

You can fetch on-demand snapshots for raw or improved position:

```swift
if let raw = positionContext.getPosition(.position) {
	print(raw.getSatelliteTime())
}

if let improved = positionContext.getPosition(.improvedPosition) {
	print(improved.getRoadSpeedLimit())
}

```

### PositionContext operations[​](#positioncontext-operations "Direct link to PositionContext operations")

| Method / Property                   | Description                                                       |
| ----------------------------------- | ----------------------------------------------------------------- |
| `init(context:)`                    | Creates a context bound to a `DataSourceContext`                  |
| `delegate`                          | Assign a `PositionContextDelegate` to receive updates             |
| `startUpdatingPositionDelegate(_:)` | Starts updates for `.position` or `.improvedPosition`             |
| `stopUpdatingPositionDelegate()`    | Stops position updates                                            |
| `getPosition()`                     | Returns latest position from default stream                       |
| `getPosition(_:)`                   | Returns latest position for a specific `PositionType`             |
| `getSourceType()`                   | Returns source stream type as `PositionDataType`                  |
| `getPositionDataType()`             | Returns position data type (`.live`, `.playback`, `.unavailable`) |
| `setSpeedMultiplier(_:)`            | Sets playback/simulation speed multiplier                         |
| `clean()`                           | Performs context cleanup                                          |

## Raw position data[​](#raw-position-data "Direct link to Raw position data")

Raw position data corresponds to `PositionType.position`. It reflects source-level sensor/location data without map-matching enrichment.

## Map matched position data[​](#map-matched-position-data "Direct link to Map matched position data")

Map-matched position data corresponds to `PositionType.improvedPosition`. It enriches raw position with road and terrain context (road modifiers, road speed limits, road info, terrain altitude/slope).

## Compare position types[​](#compare-position-types "Direct link to Compare position types")

Map-matched positions provide additional data compared to raw positions:

| Attribute                | Raw | Map Matched | Available when          | Description / API                                                         |
| ------------------------ | --- | ----------- | ----------------------- | ------------------------------------------------------------------------- |
| `satelliteTime`          | ✅  | ✅          | always                  | Sensor timestamp in ms since 1970 (`getSatelliteTime`)                    |
| `provider`               | ✅  | ✅          | always                  | Position source (`getProvider`)                                           |
| `latitude` & `longitude` | ✅  | ✅          | `hasCoordinates`        | Geographic coordinates (`getLatitude`, `getLongitude`)                    |
| `coordinates`            | ✅  | ✅          | `hasCoordinates`        | Full `CoordinatesObject` (`getCoordinates`)                               |
| `altitude`               | ✅  | ✅          | `hasAltitude`           | Altitude in meters (`getAltitude`)                                        |
| `speed`                  | ✅  | ✅          | `hasSpeed`              | Speed in m/s (`getSpeed`)                                                 |
| `speedAccuracy`          | ✅  | ✅          | `hasSpeedAccuracy`      | Speed accuracy in m/s (`getSpeedAccuracy`)                                |
| `course`                 | ✅  | ✅          | `hasCourse`             | Heading in degrees (`getCourse`)                                          |
| `courseAccuracy`         | ✅  | ✅          | `hasCourseAccuracy`     | Heading accuracy in degrees (`getCourseAccuracy`)                         |
| `horizontalAccuracy`     | ✅  | ✅          | `hasHorizontalAccuracy` | Horizontal accuracy in meters (`getHorizontalAccuracy`)                   |
| `verticalAccuracy`       | ✅  | ✅          | `hasVerticalAccuracy`   | Vertical accuracy in meters (`getVerticalAccuracy`)                       |
| `fixQuality`             | ✅  | ✅          | always                  | Trustworthiness (`getFixQuality`)                                         |
| `roadModifiers`          | ❌  | ✅          | `hasRoadLocalization`   | Packed road flags (`getRoadModifier`)                                     |
| `speedLimit`             | ❌  | ✅          | always                  | Road speed limit in m/s (`getRoadSpeedLimit`)                             |
| `roadInfo`               | ❌  | ✅          | `hasRoadLocalization`   | Road metadata list (`getRoadInfo`)                                        |
| `roadAddress`            | ❌  | ✅          | `hasRoadLocalization`   | Address fields from matched road (`getRoadAddressFieldNameWithType`)      |
| `terrainAltitude`        | ❌  | ✅          | `hasTerrainData`        | Terrain altitude from map data (`getTerrainAltitude`)                     |
| `terrainSlope`           | ❌  | ✅          | `hasTerrainData`        | Terrain slope in degrees (`getTerrainSlope`)                              |
| `formattedSpeed`         | ❌  | ✅          | always                  | Localized speed/value unit (`getFormattedSpeed`, `getFormattedSpeedUnit`) |

> 📝 **INFO**
>
> `getRoadSpeedLimit` can return `0` even for map-matched positions when speed-limit data is unavailable for the current road segment.

## PositionObject structure[​](#positionobject-structure "Direct link to PositionObject structure")

`PositionObject` contains both sensor and map-aware metadata.

### Core fields and checks[​](#core-fields-and-checks "Direct link to Core fields and checks")

| Method                                              | Description                                                                                |
| --------------------------------------------------- | ------------------------------------------------------------------------------------------ |
| `isValid()`                                         | Checks if position is valid                                                                |
| `getSatelliteTime()`                                | Satellite timestamp in milliseconds                                                        |
| `getProvider()`                                     | Position source (`unknown`, `gps`, `network`, `sensorFusion`, `mapMatching`, `simulation`) |
| `getLatitude()` / `getLongitude()`                  | Raw latitude/longitude values                                                              |
| `getCoordinates()`                                  | Position coordinates as `CoordinatesObject`                                                |
| `getAltitude()`                                     | Altitude in meters                                                                         |
| `getSpeed()`                                        | Current speed in m/s                                                                       |
| `getSpeedAccuracy()`                                | Current speed accuracy in m/s                                                              |
| `getCourse()`                                       | Heading in degrees (`0` north, `90` east)                                                  |
| `getCourseAccuracy()`                               | Heading accuracy in degrees                                                                |
| `getHorizontalAccuracy()` / `getVerticalAccuracy()` | Accuracy metrics in meters                                                                 |
| `getFixQuality()`                                   | Fix confidence (`invalid`, `inertial`, `low`, `high`)                                      |
| `getType()`                                         | Data object type (`DataType`)                                                              |
| `getFormattedSpeed()` / `getFormattedSpeedUnit()`   | Localized speed text and unit                                                              |

Availability checks are exposed via:

| Check                     | Description                            |
| ------------------------- | -------------------------------------- |
| `hasCoordinates()`        | Latitude/longitude available and valid |
| `hasAltitude()`           | Altitude is available                  |
| `hasSpeed()`              | Speed is available                     |
| `hasSpeedAccuracy()`      | Speed accuracy is available            |
| `hasCourse()`             | Course is available                    |
| `hasCourseAccuracy()`     | Course accuracy is available           |
| `hasHorizontalAccuracy()` | Horizontal accuracy is available       |
| `hasVerticalAccuracy()`   | Vertical accuracy is available         |

## Raw vs improved position data[​](#raw-vs-improved-position-data "Direct link to Raw vs improved position data")

Use `PositionType` in `PositionContext` to choose the stream:

* **Raw position**: source-level positioning data
* **Improved position**: map-matched data enriched with road and terrain context

Improved-only style fields are exposed through `PositionObject` when available:

| Method                                | Description                                                    |
| ------------------------------------- | -------------------------------------------------------------- |
| `hasRoadLocalization()`               | Indicates map matching localized the position on a road        |
| `getRoadModifier()`                   | Packed `RoadModifier` flags (`Tunnel`, `Bridge`, `Ramp`, etc.) |
| `getRoadSpeedLimit()`                 | Current road speed limit in m/s (`0` if unavailable)           |
| `getRoadInfo()`                       | Road metadata list in ascending priority                       |
| `getRoadCodeImage(_:)`                | Renders road code image (`UIImage`)                            |
| `getRoadCodeImage(_:scale:ppi:)`      | Renders road code image with explicit scale/PPI                |
| `getRoadAddressFieldNameWithType(_:)` | Road-address fields from matched position                      |
| `hasTerrainData()`                    | Indicates terrain metadata is available                        |
| `getTerrainAltitude()`                | Terrain altitude from map data                                 |
| `getTerrainSlope()`                   | Current slope in degrees (positive ascent, negative descent)   |

Decode road modifier flags from improved positions:

```swift
if let improved = positionContext.getPosition(.improvedPosition), improved.hasRoadLocalization() {
	let flags = improved.getRoadModifier()
	// 0x1 is the Tunnel flag in RoadModifier
	let isTunnel = (flags & 0x1) != 0
	print("isTunnel = \(isTunnel)")
}

```

## Provider and source context[​](#provider-and-source-context "Direct link to Provider and source context")

Use `PositionProvider` and `PositionDataType` to reason about data origin:

* Provider indicates where the position came from (`GPS`, `MapMatching`, `Simulation`, etc.)
* Source type indicates whether data is live or playback (`PositionDataTypeLive`, `PositionDataTypePlayback`)

This distinction helps when building simulation flows, diagnostics, and navigation UI behavior.

| Enum                 | Values                                                                   |
| -------------------- | ------------------------------------------------------------------------ |
| `PositionProvider`   | `unknown`, `gps`, `network`, `sensorFusion`, `mapMatching`, `simulation` |
| `PositionFixQuality` | `invalid`, `inertial`, `low`, `high`                                     |
| `PositionDataType`   | `live`, `playback`, `unavailable`                                        |

Read context values from `PositionContext`:

```swift
let sourceType = positionContext.getSourceType()
let dataType = positionContext.getPositionDataType()

if dataType == .playback {
	positionContext.setSpeedMultiplier(2.0)
}

```
