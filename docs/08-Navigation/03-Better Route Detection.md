# Better route detection

Monitor traffic conditions and automatically evaluate alternative routes for optimal navigation. This feature provides real-time route adjustments, reducing travel time and improving efficiency in dynamic traffic environments.

## What you need[​](#what-you-need "Direct link to What you need")

> 📝 **INFO**
>
> **Prerequisites:**
>
> * Route computed with specific `RoutePreferencesObject` settings
> * Active traffic data on the route
> * Significant time gain (over 5 minutes) for alternative routes

## Step 1: Configure route preferences[​](#step-1-configure-route-preferences "Direct link to Step 1: Configure route preferences")

Configure the `RoutePreferencesObject` with the following required settings:

* `setTransportMode` — `RouteTransportModecar` or `RouteTransportModeLorry`
* `setAvoidTraffic` — `TrafficAvoidanceAll` or `TrafficAvoidanceRoadBlocks`
* `setRouteType` — `RouteTypeFastest`

```swift
let preferences = RoutePreferencesObject()
preferences.setRouteType(.fastest)
preferences.setAvoidTraffic(.all)
preferences.setTransportMode(.car)

let navigationContext = NavigationContext(preferences: preferences)
navigationContext.delegate = self

```

Add additional settings to `RoutePreferencesObject` during route calculation, provided they don't conflict with the required preferences above that are needed to test for better route detection.

### Additional requirements[​](#additional-requirements "Direct link to Additional requirements")

**Traffic data:**<br /> Traffic must be present on the active navigation route for callbacks to trigger.

**Time savings threshold:**<br /> Alternative routes must offer time savings exceeding five minutes to be considered. This ensures meaningful route adjustments.

> ⚠️ **WARNING**
>
> ❌ Better route detection will not function if the required conditions above are not met.

## Step 2: Register notification callbacks[​](#step-2-register-notification-callbacks "Direct link to Step 2: Register notification callbacks")

Implement the following `NavigationContextDelegate` methods to receive better route notifications:

* **`navigationContext(_:onBetterRouteDetected:branchingCoords:travelTime:delay:timeGain:)`** - Triggered when a better route is identified. Provides the new route, the branching coordinates, total travel time, traffic-induced delay, and time savings compared to the current route. A `timeGain` value of `-1` means the original route has roadblocks and time gain cannot be calculated.
* **`navigationContext(_:onBetterRouteInvalidated:)`** - Triggered when a previously detected better route is no longer valid. Occurs if the user deviates from the shared trunk, a new better alternative appears, or traffic conditions change.

> 📝 **INFO**
>
> You must manually manage and switch to the recommended route. The navigation context does not automatically switch routes.

```swift
func navigationContext(_ navigationContext: NavigationContext,
                       onBetterRouteDetected route: RouteObject,
                       branchingCoords: CoordinatesObject,
                       travelTime: Int,
                       delay: Int,
                       timeGain: Int) {
    print("Better route detected — travel time: \(travelTime) s, delay: \(delay) s, time gain: \(timeGain) s")
    // Store the better route and present it to the user for acceptance
}

func navigationContext(_ navigationContext: NavigationContext,
                       onBetterRouteInvalidated state: Bool) {
    print("Previously found better route is no longer valid")
}

```

## Step 3: Switch to the better route (optional)[​](#step-3-switch-to-the-better-route-optional "Direct link to Step 3: Switch to the better route (optional)")

To switch navigation to the detected better route, stop the current navigation and restart it with the new route:

```swift
func acceptBetterRoute(_ betterRoute: RouteObject) {
    navigationContext?.cancelNavigateRoute()

    navigationContext?.navigate(withRoute: betterRoute) { success in
        print("Switched to better route: \(success)")
    }
}

```

