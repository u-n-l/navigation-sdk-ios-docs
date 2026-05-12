# Get started with positioning

Learn how to configure permissions and start receiving live position updates in iOS.

## Step 1: Add location permission[​](#step-1-add-location-permission "Direct link to Step 1: Add location permission")

Add the following key to your `Info.plist`:

```xml
<key>NSLocationWhenInUseUsageDescription</key>
<string>Location is needed for map localization and navigation.</string>

```

## Step 2: Ask for user consent[​](#step-2-ask-for-user-consent "Direct link to Step 2: Ask for user consent")

After declaring the usage description, request location authorization at runtime. iOS shows the system permission dialog only after you call the Core Location authorization API.

```swift
import CoreLocation

final class PositionPermissionHandler: NSObject, CLLocationManagerDelegate {
    private let locationManager = CLLocationManager()

    func requestPermission() {
        locationManager.delegate = self
        locationManager.requestWhenInUseAuthorization()
    }

    func locationManagerDidChangeAuthorization(_ manager: CLLocationManager) {
        switch manager.authorizationStatus {
        case .authorizedWhenInUse, .authorizedAlways:
            print("Location permission granted")
        case .denied, .restricted:
            print("Location permission denied")
        case .notDetermined:
            break
        @unknown default:
            break
        }
    }
}

let permissionHandler = PositionPermissionHandler()
permissionHandler.requestPermission()

```

If the user denies the request, iOS does not show the permission popup again automatically. In that case, direct the user to the app's Settings screen to enable location access manually.

## Step 3: Configure data source and position context[​](#step-3-configure-data-source-and-position-context "Direct link to Step 3: Configure data source and position context")

Create a live `DataSourceContext`, configure it for position updates, then bind `PositionContext` to it.

```swift
let source = DataSourceContext()
let config = DataSourceConfigurationObject()

config.setPositionAccuracy(.whenMoving)
config.setPositionActivity(.automotive)
config.setPositionDistanceFilter(0)

_ = source.setConfiguration(config, for: .position)
_ = source.start()

let positionContext = PositionContext(context: source)

```

## Step 4: Receive updates[​](#step-4-receive-updates "Direct link to Step 4: Receive updates")

Use `PositionContextDelegate` for continuous updates:

```swift
final class PositionHandler: NSObject, PositionContextDelegate {
    func positionContext(_ positionContext: PositionContext, didUpdatePosition position: PositionObject) {
        guard position.hasCoordinates() else { return }
        let c = position.getCoordinates()
        print("lat=\(c.latitude) lon=\(c.longitude)")
    }
}

let handler = PositionHandler()
positionContext.delegate = handler
positionContext.startUpdatingPositionDelegate(.improvedPosition)

```

## Read current snapshot[​](#read-current-snapshot "Direct link to Read current snapshot")

```swift
if let latest = positionContext.getPosition(.improvedPosition) {
    print("speed m/s = \(latest.getSpeed())")
}

```
