# Get started with Alarms

This guide explains how to set up and configure the alarm system to monitor geographic areas, routes, and receive notifications for various events.

The alarm system offers monitoring and notification functionalities for different alarm types, such as boundary crossings, speed limit violations, and landmark alerts. You can configure parameters like alarm distance, overspeed thresholds, and whether monitoring should occur even without a route being followed.

The system monitors specific geographic areas or routes and triggers alarms when predefined conditions are met, such as crossing a boundary or entering or exiting a tunnel. It provides customization options for alarm behavior based on location (e.g., inside or outside city limits). You can implement `AlarmContextDelegate` to receive notifications about specific events, including monitoring state changes or when a landmark alarm is triggered.

The system supports interaction with various alarm types, including overlay item and landmark alarms, and offers an easy interface for both setting and getting alarm-related information.

> 💡 **TIP**
>
> Multiple `AlarmContext` instances and delegates can operate simultaneously, allowing you to monitor various events concurrently.

## Configure AlarmContext and delegate[​](#configure-alarmcontext-and-delegate "Direct link to Configure AlarmContext and delegate")

Create an `AlarmContext` instance and assign a delegate conforming to `AlarmContextDelegate`:

```swift
// Retain as a property to keep the alarm active
var alarmContext: AlarmContext?

func configureAlarms() {
    alarmContext = AlarmContext()
    alarmContext?.delegate = self
}

// MARK: - AlarmContextDelegate

func alarmContext(onBoundaryCrossed alarmContext: AlarmContext,
                  enteredArea arrayIn: [AlarmMonitoredAreaObject],
                  exitedAreas arrayOut: [AlarmMonitoredAreaObject]) {}

func alarmContext(onMonitoringStateChanged alarmContext: AlarmContext) {}

func alarmContext(onTunnelEntered alarmContext: AlarmContext) {}

func alarmContext(onTunnelLeft alarmContext: AlarmContext) {}

func alarmContext(onLandmarkAlarmsUpdated alarmContext: AlarmContext) {}

func alarmContext(onOverlayItemAlarmsUpdated alarmContext: AlarmContext) {}

func alarmContext(onLandmarkAlarmsPassedOver alarmContext: AlarmContext) {}

func alarmContext(onOverlayItemAlarmsPassedOver alarmContext: AlarmContext) {}

func alarmContext(_ alarmContext: AlarmContext, onHighSpeed limit: Double, insideCityArea: Bool) {}

func alarmContext(_ alarmContext: AlarmContext, onSpeedLimit speed: Double, limit: Double, insideCityArea: Bool) {}

func alarmContext(_ alarmContext: AlarmContext, onNormalSpeed limit: Double, insideCityArea: Bool) {}

func alarmContext(onEnterDayMode alarmContext: AlarmContext) {}

func alarmContext(onEnterNightMode alarmContext: AlarmContext) {}

func alarmContext(_ alarmContext: AlarmContext, onCountryLeft countryCode: String) {}

func alarmContext(_ alarmContext: AlarmContext, onCountryEntered code: String) {}

```

All `AlarmContextDelegate` methods are `@optional`. Implement only the callbacks relevant to your use case.

> ⚠️ **WARNING**
>
> The `AlarmContext` object must remain in memory for the duration of the monitoring period. Store it as a property on a long-lived object. Deallocating it will stop all alarms.

## AlarmContextDelegate methods[​](#alarmcontextdelegate-methods "Direct link to AlarmContextDelegate methods")

| Delegate method                                            | Description                                                              |
| ---------------------------------------------------------- | ------------------------------------------------------------------------ |
| `alarmContext(onBoundaryCrossed:enteredArea:exitedAreas:)` | Called when the user enters or exits monitored geographic areas          |
| `alarmContext(onMonitoringStateChanged:)`                  | Called when the alarm monitoring state changes                           |
| `alarmContext(onTunnelEntered:)`                           | Called when the user enters a tunnel                                     |
| `alarmContext(onTunnelLeft:)`                              | Called when the user exits a tunnel                                      |
| `alarmContext(onLandmarkAlarmsUpdated:)`                   | Called when the list of approaching landmarks changes                    |
| `alarmContext(onOverlayItemAlarmsUpdated:)`                | Called when the list of approaching overlay items changes                |
| `alarmContext(onLandmarkAlarmsPassedOver:)`                | Called when a monitored landmark has been passed                         |
| `alarmContext(onOverlayItemAlarmsPassedOver:)`             | Called when a monitored overlay item has been passed                     |
| `alarmContext(_:onHighSpeed:insideCityArea:)`              | Called when the user exceeds the speed limit by the configured threshold |
| `alarmContext(_:onSpeedLimit:limit:insideCityArea:)`       | Called when the speed limit on the current road segment changes          |
| `alarmContext(_:onNormalSpeed:insideCityArea:)`            | Called when the user returns to a normal speed                           |
| `alarmContext(onEnterDayMode:)`                            | Called when the location transitions to daylight conditions              |
| `alarmContext(onEnterNightMode:)`                          | Called when the location transitions to night conditions                 |
| `alarmContext(_:onCountryLeft:)`                           | Called when the user crosses a country border (leaving)                  |
| `alarmContext(_:onCountryEntered:)`                        | Called when the user crosses a country border (entering)                 |
