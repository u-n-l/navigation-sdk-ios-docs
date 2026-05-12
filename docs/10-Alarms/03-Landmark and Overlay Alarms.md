# Landmark and overlay alarms

This guide explains how to configure notifications when approaching specific landmarks or overlay items within a defined proximity.

The `AlarmContext` can be configured to send notifications when approaching specific landmarks or overlay items within a defined proximity. This behavior can be tailored to trigger notifications exclusively during navigation or simulation modes, or while freely exploring the map without a predefined route.

Use cases include:

* Notify users about incoming reports such as speed cameras, police, accidents, or other road hazards
* Notify users when approaching points of interest, such as historical landmarks or scenic viewpoints
* Notify users about traffic signs such as stop and give way signs

> 💡 **TIP**
>
> You can search for landmarks along the active route and add them for monitoring. Be sure to account for potential route deviations.

> ⚠️ **WARNING**
>
> If notifications are not triggered via the delegate, make sure that:
>
> * The `AlarmContext` is properly initialized and kept alive
> * The alarm distance and `monitorWithoutRoute` are configured as needed
> * The landmark stores or overlays to be monitored are successfully added to the `AlarmContext`
> * Overlay items are on the correct side of the road — items on the opposite side will not trigger notifications

## Configure alarm distance[​](#configure-alarm-distance "Direct link to Configure alarm distance")

Set the distance threshold in meters for triggering notifications when approaching a landmark or overlay item:

```swift
alarmContext?.setAlarmDistance(200)

```

Retrieve the current alarm distance:

```swift
let distance = alarmContext?.getAlarmDistance() ?? 0

```

## Configure alarms without active navigation[​](#configure-alarms-without-active-navigation "Direct link to Configure alarms without active navigation")

By default, alarms are only triggered during active navigation or simulation. To enable notifications at all times regardless of navigation state:

```swift
alarmContext?.setMonitorWithoutRoute(true)

```

Retrieve the current state:

```swift
let isMonitoringWithoutRoute = alarmContext?.getMonitorWithoutRoute() ?? false

```

## Landmark alarms[​](#landmark-alarms "Direct link to Landmark alarms")

### Configure alarm callbacks[​](#configure-alarm-callbacks "Direct link to Configure alarm callbacks")

Implement `alarmContext(onLandmarkAlarmsUpdated:)` and `alarmContext(onLandmarkAlarmsPassedOver:)` to receive notifications when approaching landmarks and when they have been passed:

```swift
func alarmContext(onLandmarkAlarmsUpdated alarmContext: AlarmContext) {
    guard let alarmsObject = alarmContext.getLandmarkAlarmsObject() else { return }

    let positions = alarmsObject.getLandmarkPositions()
    // Sorted ascending by distance from the current position
    if let closest = positions.first {
        let landmark = closest.getLandmark()
        let distance = closest.getDistance()
        print("Approaching \(landmark.getLandmarkName()) — \(distance) m away")
    }
}

func alarmContext(onLandmarkAlarmsPassedOver alarmContext: AlarmContext) {
    print("A landmark was passed over")
}

```

> 📝 **INFO**
>
> `alarmContext(onLandmarkAlarmsUpdated:)` is continuously triggered once the threshold distance is exceeded, until the landmark is passed. `alarmContext(onLandmarkAlarmsPassedOver:)` is called once when the landmark is intercepted.

### Specify landmarks to monitor[​](#specify-landmarks-to-monitor "Direct link to Specify landmarks to monitor")

Add a `LandmarkStoreContext` to the `AlarmContext` landmark collection using `getLandmarkStoreCollection()`:

```swift
// Create a landmark store and add landmarks to it
let landmarkStore = LandmarkStoreContext(identifier: 0)

let landmark = LandmarkObject()
landmark.setCoordinates(CoordinatesObject.coordinates(withLatitude: 49.0576, longitude: 1.9705))
landmarkStore.addLandmark(landmark)

// Add all categories of this store to the alarm collection
alarmContext?.getLandmarkStoreCollection()?.addAllStoreCategories(landmarkStore.getId())

```

Multiple stores can be added simultaneously. Remove them using `removeAllStoreCategories(_:)` or `removeStoreCategoryId(_:categoryId:)`.

## Overlay alarms[​](#overlay-alarms "Direct link to Overlay alarms")

The workflow for overlay items mirrors that for landmarks, with comparable behavior and functionality. All notices specified for landmarks also apply to overlay items.

> ⚠️ **WARNING**
>
> To enable overlay alarms, a `MapViewController` must be present with a style that includes the overlay. The overlay must also be enabled for alarms to function.

### Configure alarm callbacks[​](#configure-alarm-callbacks-1 "Direct link to Configure alarm callbacks")

Implement `alarmContext(onOverlayItemAlarmsUpdated:)` and `alarmContext(onOverlayItemAlarmsPassedOver:)`:

```swift
func alarmContext(onOverlayItemAlarmsUpdated alarmContext: AlarmContext) {
    guard let alarmsObject = alarmContext.getOverlayItemAlarmsObject() else { return }

    let positions = alarmsObject.getOverlayItemPositions()
    // Sorted ascending by distance from the current position
    if let closest = positions.first {
        let overlayItem = closest.getOverlayItem()
        let distance = closest.getDistance()
        print("Approaching overlay item — \(distance) m away")
    }
}

func alarmContext(onOverlayItemAlarmsPassedOver alarmContext: AlarmContext) {
    print("An overlay item was passed over")
}

```

### Specify overlays to monitor[​](#specify-overlays-to-monitor "Direct link to Specify overlays to monitor")

Add overlays to the `AlarmContext` using `geOverlayMutableCollection()`. Overlays are specified by their overlay ID:

```swift
// Add the social reports overlay (e.g. speed cameras, hazards)
let socialReportsOverlayId = Int32(CommonOverlayIdentifier.socialReports.rawValue) // use the appropriate overlay ID
alarmContext?.geOverlayMutableCollection()?.add(Int32(socialReportsOverlayId))

```

Remove an overlay from monitoring:

```swift
alarmContext?.geOverlayMutableCollection()?.remove(Int32(socialReportsOverlayId))

```
