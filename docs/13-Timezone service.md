# Timezone service

The `TimezoneContext` class provides functionality for retrieving time zone information based on coordinates and a UTC timestamp, using built-in offline data.

> 📝 **INFO**
>
> The iOS SDK provides an offline timezone lookup that uses built-in SDK data. Update the SDK regularly to keep the timezone data current.

## Understand the TimezoneResultObject structure[​](#understand-the-timezoneresultobject-structure "Direct link to Understand the TimezoneResultObject structure")

`TimezoneResultObject` represents the result of a timezone lookup and exposes the following methods:

| Method            | Return type      | Description                                                                          |
| ----------------- | ---------------- | ------------------------------------------------------------------------------------ |
| `getDstOffset()`  | `Int`            | Daylight Saving Time (DST) offset in seconds                                         |
| `getUtcOffset()`  | `Int`            | Raw UTC offset in seconds, excluding DST. Can be negative                            |
| `getOffset()`     | `Int`            | Total offset relative to UTC in seconds (`dstOffset` + `utcOffset`). Can be negative |
| `getStatus()`     | `TimezoneStatus` | Status of the response — see values below                                            |
| `getTimezoneId()` | `String`         | Timezone identifier (e.g., `"Europe/Paris"`, `"America/New_York"`)                   |
| `getLocalTime()`  | `TimeObject?`    | Local time for the requested timezone                                                |

### TimezoneStatus values[​](#timezonestatus-values "Direct link to TimezoneStatus values")

The `TimezoneStatus` enum indicates the result of a timezone lookup:

| Value                             | Description                                                  |
| --------------------------------- | ------------------------------------------------------------ |
| `TimezoneStatusSuccess`           | Request completed successfully                               |
| `TimezoneStatusInvalidCoordinate` | Provided coordinates were invalid or out of range            |
| `TimezoneStatusWrongTimezoneId`   | Provided timezone identifier was malformed or not recognized |
| `TimezoneStatusWrongTimestamp`    | Provided timestamp was invalid or could not be parsed        |
| `TimezoneStatusNotFound`          | No timezone found for the given input                        |

## Retrieve timezone by coordinates[​](#retrieve-timezone-by-coordinates "Direct link to Retrieve timezone by coordinates")

Use `getOfflineTimezoneInfo(_:time:)` on `TimezoneContext.sharedInstance()` to retrieve timezone information based on geographic coordinates and a UTC `TimeObject`:

```swift
let location = CoordinatesObject.coordinates(withLatitude: 55.626, longitude: 37.457)

let utcTime = TimeObject()
utcTime.setYear(2025)
utcTime.setMonth(7)
utcTime.setDay(1)

let result = TimezoneContext.sharedInstance().getOfflineTimezoneInfo(location, time: utcTime)

if result.getStatus() == .success {
    print("Timezone ID: \(result.getTimezoneId())")
    print("UTC offset: \(result.getUtcOffset()) seconds")
    print("DST offset: \(result.getDstOffset()) seconds")
    print("Total offset: \(result.getOffset()) seconds")

    if let localTime = result.getLocalTime() {
        print("Local time year: \(localTime.getYear()) month: \(localTime.getMonth()) day: \(localTime.getDay())")
    }
} else {
    print("Timezone lookup failed: \(result.getStatus())")
}

```

> ⚠️ **WARNING**
>
> The offline method uses built-in timezone data that may become outdated. Update the SDK regularly to refresh timezone data.
