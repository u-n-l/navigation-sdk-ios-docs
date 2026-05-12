# Other alarms

Receive alerts for environmental changes such as entering or exiting tunnels, transitions between day and night, and country border crossings.

## Tunnel events[​](#tunnel-events "Direct link to Tunnel events")

Implement `alarmContextOnTunnelEntered(_:)` and `alarmContextOnTunnelLeft(_:)` to receive notifications when entering or exiting a tunnel:

```swift
func alarmContext(onTunnelEntered alarmContext: AlarmContext) {
    print("Tunnel entered")
}

func alarmContext(onTunnelLeft alarmContext: AlarmContext) {
    print("Tunnel left")
}

```

## Day and night transitions[​](#day-and-night-transitions "Direct link to Day and night transitions")

Receive notifications when your location transitions between day and night based on geographical region and seasonal changes:

```swift
func alarmContext(onEnterDayMode alarmContext: AlarmContext) {
    print("Day mode entered")
}

func alarmContext(onEnterNightMode alarmContext: AlarmContext) {
    print("Night mode entered")
}

```

## Country border crossings[​](#country-border-crossings "Direct link to Country border crossings")

Receive notifications when the user crosses a country border:

```swift
func alarmContext(_ alarmContext: AlarmContext, onCountryLeft countryCode: String) {
    print("Left country: \(countryCode)")
}

func alarmContext(_ alarmContext: AlarmContext, onCountryEntered code: String) {
    print("Entered country: \(code)")
}

```

Country codes are provided in ISO 3166-1 alpha-3 format.
