# Traffic Events

The UNL Navigation SDK for iOS provides real-time traffic information about delays, incidents, and road restrictions that can affect routing and navigation.

**Event sources:**

* **UNL servers** - Provide up-to-date traffic data when online
* **User-defined roadblocks** - Restrict specific road segments or areas

**Impact zones:**

* **Path-based** - Follows the shape of a road segment
* **Area-based** - Covers a larger geographic area

Core entities for traffic handling:

* `TrafficEventObject` for generic traffic events
* `RouteTrafficEventObject` for route-specific traffic details
* `TrafficContext` for traffic configuration and roadblock management

***

## Enable traffic[​](#enable-traffic "Direct link to Enable traffic")

Use `TrafficContext` to enable traffic for route calculation:

```swift
// Create or access your TrafficContext
let trafficContext = TrafficContext()

// Enable online traffic
trafficContext.setUseTraffic(.useOnline)

// Or use offline-only traffic (local data)
trafficContext.setUseTraffic(.useOffline)

```

> 📝 **INFO**
>
> Traffic usage mode (none/online/offline) influences route calculation behavior when traffic-aware routing preferences are applied. Enable traffic before calculating routes to include traffic data in results.

***

## TrafficEventObject structure[​](#trafficeventobject-structure "Direct link to TrafficEventObject structure")

The `TrafficEventObject` class contains traffic event information:

| Method                       | Returns                          | Description                                  |
| ---------------------------- | -------------------------------- | -------------------------------------------- |
| `isRoadblock()`              | `Bool`                           | Whether event represents a roadblock         |
| `getDelay()`                 | `Int`                            | Estimated delay in seconds (`-1` if unknown) |
| `getLength()`                | `Int`                            | Length in meters of affected segment         |
| `getImpactZone()`            | `TrafficEventImpactZone`         | Path or Area impact zone                     |
| `getReferencePoint()`        | `CoordinatesObject?`             | Central coordinate for the event             |
| `getBoundingBox()`           | `RectangleGeographicAreaObject?` | Bounding rectangle                           |
| `getArea()`                  | `GeographicAreaObject?`          | Geographic area of the event                 |
| `getDescription()`           | `String`                         | Human-readable description                   |
| `getEventClass()`            | `TrafficEventClass`              | Classification of the event                  |
| `getEventSeverity()`         | `TrafficEventSeverity`           | Severity level                               |
| `getImage(_:)`               | `UIImage?`                       | Event icon/image at specified size           |
| `getPreviewURL()`            | `URL?`                           | Preview URL (empty for user roadblocks)      |
| `isUserRoadblock()`          | `Bool`                           | Whether event is user-defined                |
| `getAffectedTransportMode()` | `Int`                            | Affected transport modes bitset              |
| `getStartTime()`             | `TimeObject?`                    | UTC start time                               |
| `getEndTime()`               | `TimeObject?`                    | UTC end time                                 |
| `isActive()`                 | `Bool`                           | Whether event is currently active            |
| `isExpired()`                | `Bool`                           | Whether event has expired                    |
| `hasOppositeSibling()`       | `Bool`                           | Path event has opposite-direction sibling    |

***

## RouteTrafficEventObject structure[​](#routetrafficeventobject-structure "Direct link to RouteTrafficEventObject structure")

`RouteTrafficEventObject` extends `TrafficEventObject` with route-specific information:

| Method                               | Returns              | Description                                        |
| ------------------------------------ | -------------------- | -------------------------------------------------- |
| `getDistanceToDestination()`         | `Int`                | Distance in meters from event to route destination |
| `getFrom()`                          | `CoordinatesObject?` | Event start point on route                         |
| `getTo()`                            | `CoordinatesObject?` | Event end point on route                           |
| `getFromLandmark()`                  | `LandmarkObject?`    | Start point as landmark                            |
| `getToLandmark()`                    | `LandmarkObject?`    | End point as landmark                              |
| `hasTrafficEventOnDistance(_:)`      | `Bool`               | Whether delay exists within remaining distance     |
| `isInsideTrafficEventOnDistance(_:)` | `Bool`               | Whether current position is inside event           |
| `getDistanceFormatted()`             | `String`             | Formatted distance to destination                  |
| `getDelayTimeFormatted()`            | `String`             | Formatted delay duration                           |
| `getDelayDistanceFormatted()`        | `String`             | Formatted delay distance                           |

***

## Event classifications[​](#event-classifications "Direct link to Event classifications")

`TrafficEventClass` provides classification categories:

* `trafficEventClassOther` - General information
* `trafficEventClassLevelOfService` - Congestion level
* `trafficEventClassAccidents` - Accident/incident
* `trafficEventClassClosuresAndLaneRestrictions` - Closure or lane restriction
* `trafficEventClassRoadworks` - Construction/roadwork
* `trafficEventClassObstructionHazards` - Hazards on road
* `trafficEventClassRoadConditions` - Road surface condition
* `trafficEventClassDelays` - Traffic delay
* `trafficEventClassTrafficRestrictions` - Access restriction
* `trafficEventClassUserRoadblock` - User-defined roadblock

`TrafficEventSeverity` indicates impact level:

* `trafficEventSeverityStationary` - Stationary traffic
* `trafficEventSeverityQueuing` - Queuing traffic
* `trafficEventSeveritySlowTraffic` - Slow traffic flow
* `trafficEventSeverityPossibleDelay` - Possible delay
* `trafficEventSeverityUnknown` - Unknown severity
* `trafficEventSeverityFree` - Free traffic flow

***

> ⚠️ **WARNING**
>
> Traffic events are only available on routes calculated with traffic enabled. Enable traffic in `TrafficContext` before route calculation for traffic-aware routing.

***

Traffic events integrate seamlessly with routing and navigation, ensuring calculated routes account for real-time conditions and user-defined restrictions.
