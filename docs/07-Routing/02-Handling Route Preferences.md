# Handling Route Preferences

This guide explains how to customize routes using preferences, configure vehicle profiles, and apply routing constraints for different transport modes.

## Configure route preferences[​](#configure-route-preferences "Direct link to Configure route preferences")

Set route options using **`RoutePreferencesObject`** to customize route calculations.

**Generic route options:**

| Property                         | Setter / Getter                                                                                | Default                                 |
| -------------------------------- | ---------------------------------------------------------------------------------------------- | --------------------------------------- |
| accurateTrackMatch               | `setAccurateTrackMatch(_:)` / `getAccurateTrackMatch()`                                        | `true`                                  |
| allowOnlineCalculation           | `setAllowOnlineCalculation(_:)` / `getAllowOnlineCalculation()`                                | `true`                                  |
| alternativeRoutesBalancedSorting | `setAlternativeRoutesBalancedSorting(_:)` / `getAlternativeRoutesBalancedSorting()`            | `true`                                  |
| alternativesSchema               | `setAlternativesSchema(_:)` / `getAlternativesSchema()`                                        | `RouteAlternativesSchemaDefault`        |
| departureHeading                 | `setDepartureHeading(_:accuracy:)` / `getDepartureHeading()` + `getDepartureHeadingAccuracy()` | heading: -1, accuracy: 0                |
| ignoreRestrictionsOverTrack      | `setIgnoreRestrictionsOverTrack(_:)` / `getIgnoreRestrictionsOverTrack()`                      | `false`                                 |
| maximumDistanceConstraint        | `setMaximumDistanceConstraint(_:)` / `getMaximumDistanceConstraint()`                          | `true`                                  |
| pathAlgorithm                    | `setPathAlgorithm(_:)` / `getPathAlgorithm()`                                                  | `RoutePathAlgorithmTypeUnl`       |
| pathAlgorithmFlavor              | `setPathAlgorithmFlavor(_:)` / `getPathAlgorithmFlavor()`                                      | `RoutePathAlgorithmFlavorTypeUnl` |
| resultDetails                    | `setResultDetails(_:)` / `getResultDetails()`                                                  | `RouteResultDetailsFull`                |
| routeRanges                      | `setRouteRanges(_:quality:)` / `getRouteRanges()`                                              | `[]`                                    |
| routeRangesQuality               | `setRouteRanges(_:quality:)` / `getRouteRangesQuality()`                                       | `100`                                   |
| routeType                        | `setRouteType(_:)` / `getRouteType()`                                                          | `RouteTypeFastest`                      |
| timestamp                        | `setTimestamp(_:)` / `getTimestamp()`                                                          | current time                            |
| transportMode                    | `setTransportMode(_:)` / `getTransportMode()`                                                  | `RouteTransportModeCar`                 |


**Complex structure creation options:**

| Property            | Setter / Getter                                                | Default |
| ------------------- | -------------------------------------------------------------- | ------- |
| buildConnections    | `setBuildConnections(_:maxLengthM:)` / `getBuildConnections()` | `false` |
| buildTerrainProfile | `setBuildTerrainProfile(_:)` / `getBuildTerrainProfile()`      | `false` |

**Vehicle profile options:**

| Property            | Setter / Getter                                                           | Default                     |
| ------------------- | ------------------------------------------------------------------------- | --------------------------- |
| bikeProfile         | `setBikeProfile(_:)` / `getBikeProfile()`                                 | `.road` (`BikeProfileRoad`) |
| eBikeProfileDetails | `setBikeProfile(_:withEBikeProfileDetails:)` / `getEBikeProfileDetails()` | —                           |
| pedestrianProfile   | `setPedestrianProfile(_:)` / `getPedestrianProfile()`                     | `PedestrianProfileWalk`     |
| truckProfile        | `setTruckProfile(_:)` / `getTruckProfile()`                               | all-zero struct             |

**Route avoidance options:**

| Property                   | Setter / Getter                                                         | Default                    |
| -------------------------- | ----------------------------------------------------------------------- | -------------------------- |
| avoidMotorways             | `setAvoidMotorways(_:)` / `getAvoidMotorways()`                         | `false`                    |
| avoidTollRoads             | `setAvoidTollRoads(_:)` / `getAvoidTollRoads()`                         | `false`                    |
| avoidFerries               | `setAvoidFerries(_:)` / `getAvoidFerries()`                             | `false`                    |
| avoidCarpoolLanes          | `setAvoidCarpoolLanes(_:)` / `getAvoidCarpoolLanes()`                   | `false`                    |
| avoidUnpavedRoads          | `setAvoidUnpavedRoads(_:)` / `getAvoidUnpavedRoads()`                   | `false`                    |
| avoidTurnAroundInstruction | `setAvoidTurnAroundInstruction(_:)` / `getAvoidTurnAroundInstruction()` | `false`                    |
| avoidTraffic               | `setAvoidTrafficType(_:)` / `getAvoidTraffic()`                         | `TrafficAvoidanceTypeNone` |

**Emergency vehicle options:**

| Property                           | Setter / Getter                                                                        | Default |
| ---------------------------------- | -------------------------------------------------------------------------------------- | ------- |
| emergencyVehicleMode               | `setEmergencyVehicleMode(_:)` / `getEmergencyVehicleMode()`                            | `false` |
| emergencyVehicleExtraFreedomLevels | `setEmergencyVehicleMode(_:extraFreedom:)` / `getEmergencyVehicleExtraFreedomLevels()` | `0`     |

**Public transport options:**

| Property                     | Setter / Getter                                                             | Default                          |
| ---------------------------- | --------------------------------------------------------------------------- | -------------------------------- |
| algorithmType                | `setAlgorithmType(_:)` / `getAlgorithmType()`                               | `RouteAlgorithmTypeDeparture`    |
| minimumTransferTimeInMinutes | `setMinimumTransferTimeInMinutes(_:)` / `getMinimumTransferTimeInMinutes()` | `1`                              |
| maximumTransferTimeInMinutes | `setMaximumTransferTimeInMinutes(_:)` / `getMaximumTransferTimeInMinutes()` | `300`                            |
| maximumWalkDistance          | `setMaximumWalkDistance(_:)` / `getMaximumWalkDistance()`                   | `5000`                           |
| sortingStrategy              | `setSortingStrategy(_:)` / `getSortingStrategy()`                           | `PTRouteSortingStrategyBestTime` |
| routeTypePreferences         | `setRouteTypePreferences(_:)` / `getRouteTypePreferences()`                 | `0` (none)                       |
| useBikes                     | `setUseBikes(_:)` / `getUseBikes()`                                         | `false`                          |
| useWheelchair                | `setUseWheelchair(_:)` / `getUseWheelchair()`                               | `false`                          |

Example — calculate the fastest car route with terrain profile:

```swift
let preferences = RoutePreferencesObject()
preferences.setTransportMode(.car)
preferences.setRouteType(.fastest)
preferences.setBuildTerrainProfile(true)

```

## Configure vehicle profiles[​](#configure-vehicle-profiles "Direct link to Configure vehicle profiles")

Customize routing behavior for different vehicle types using profile configurations.

### Truck profile[​](#truck-profile "Direct link to Truck profile")

Define truck-specific routing preferences using the **`TruckProfileDetails`** C struct.

Available fields:

| Field      | Type     | Default | Description                  |
| ---------- | -------- | ------- | ---------------------------- |
| `mass`     | `Int32`  | 0       | Truck weight in kg.          |
| `height`   | `Int32`  | 0       | Truck height in cm.          |
| `length`   | `Int32`  | 0       | Truck length in cm.          |
| `width`    | `Int32`  | 0       | Truck width in cm.           |
| `axleLoad` | `Int32`  | 0       | Maximum load per axle in kg. |
| `maxSpeed` | `Double` | 0       | Maximum speed in m/s.        |

All fields default to 0, meaning they are not considered in routing. Set at least one field to a non-zero value to activate truck-specific restrictions.

### Electric bike profile[​](#electric-bike-profile "Direct link to Electric bike profile")

Define electric bike routing preferences using the **`EBikeProfileDetails`** C struct together with a `BikeProfile` enum value.

Available `EBikeProfileDetails` fields:

| Field                     | Type           | Default            | Description                                          |
| ------------------------- | -------------- | ------------------ | ---------------------------------------------------- |
| `type`                    | `EBikeProfile` | `EBikeProfileNone` | E-bike type.                                         |
| `bikeMass`                | `Float`        | 0                  | Bike mass in kg.                                     |
| `bikerMass`               | `Float`        | 0                  | Biker mass in kg.                                    |
| `auxConsumptionDay`       | `Float`        | 0                  | Auxiliary power consumption during day in Watts.     |
| `auxConsumptionNight`     | `Float`        | 0                  | Auxiliary power consumption during night in Watts.   |
| `batteryCapacity`         | `Float`        | 1000               | Battery usable capacity in Wh.                       |
| `departureSoc`            | `Float`        | —                  | Battery state of charge at departure (`0.0`–`1.0`).  |
| `refSpeed`                | `Float`        | 0                  | Reference speed in m/s.                              |
| `ignoreLegalRestrictions` | `Bool`         | `false`            | Ignore country-based legal restrictions for e-bikes. |

**`EBikeProfile`** values: `EBikeProfileNone`, `EBikeProfilePedelec`, `EBikeProfilePowerOnDemand`.

**`BikeProfile`** values: `BikeProfileRoad`, `BikeProfileCross`, `BikeProfileCity`, `BikeProfileMountain`.

**`PedestrianProfile`** values: `PedestrianProfileWalk`, `PedestrianProfileHike`.

## Calculate truck routes[​](#calculate-truck-routes "Direct link to Calculate truck routes")

Compute routes optimized for trucks by setting truck-specific preferences and the lorry transport mode.

```swift
let departure = LandmarkObject.landmark(
    withName: "Paris",
    location: CoordinatesObject.coordinates(withLatitude: 48.87126, longitude: 2.33787)
)
let destination = LandmarkObject.landmark(
    withName: "London",
    location: CoordinatesObject.coordinates(withLatitude: 51.4739, longitude: -0.0302)
)

var truckProfile = TruckProfileDetails()
truckProfile.height = 180   // cm
truckProfile.length = 500   // cm
truckProfile.width  = 200   // cm
truckProfile.axleLoad = 1500 // kg
truckProfile.maxSpeed = 60  // m/s
truckProfile.mass   = 3000  // kg

let preferences = RoutePreferencesObject()
preferences.setTruckProfile(truckProfile)
preferences.setTransportMode(.lorry) // crucial
let nav = NavigationContext(preferences: preferences)
nav.calculateRoute(withWaypoints: [departure, destination],
    statusHandler: { status in
        // handle status,
    }, completionHandler: { routes, code in
        // handle routes
    }
)

```

## Calculate caravan routes[​](#calculate-caravan-routes "Direct link to Calculate caravan routes")

Compute routes for caravans and trailers with size and weight restrictions.

Caravans or trailers may be restricted on some roads due to size or weight, yet still permitted on roads where trucks are not. The key difference from a truck route is the transport mode: use `.car` instead of `.lorry`.

```swift
let departure = LandmarkObject.landmark(
    withName: "Paris",
    location: CoordinatesObject.coordinates(withLatitude: 48.87126, longitude: 2.33787)
)
let destination = LandmarkObject.landmark(
    withName: "London",
    location: CoordinatesObject.coordinates(withLatitude: 51.4739, longitude: -0.0302)
)

var caravanProfile = TruckProfileDetails()
caravanProfile.height   = 180  // cm
caravanProfile.length   = 500  // cm
caravanProfile.width    = 200  // cm
caravanProfile.axleLoad = 1500 // kg

let preferences = RoutePreferencesObject()
preferences.setTruckProfile(caravanProfile)
preferences.setTransportMode(.car) // crucial — distinguishes caravan from truck

let nav = NavigationContext(preferences: preferences)
nav.calculateRoute(withWaypoints: [departure, destination],
    statusHandler: { status in
        // handle status,
    }, completionHandler: { routes, code in
        // handle routes
    }
)

```

> ⚠️ **WARNING**
>
> Set at least one of `height`, `length`, `width`, or `axleLoad` to a non-zero value for restrictions to take effect. If all fields are 0, a normal car route is calculated.

