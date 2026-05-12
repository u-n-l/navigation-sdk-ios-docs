# Weather Service

The `WeatherContext` class provides methods for retrieving current, hourly, and daily weather forecasts. Access the shared instance via `WeatherContext.shared()`.

## Get Current Weather Forecast[​](#get-current-weather-forecast "Direct link to Get Current Weather Forecast")

Use `requestCurrentForecast(_:completionHandler:)` to retrieve the current weather forecast. Provide an array of `CoordinatesObject` for the desired locations. The completion handler receives an array of `WeatherContextForecast` objects — one per coordinate.

```swift
let coordinates = CoordinatesObject.coordinates(withLatitude: 48.864716, longitude: 2.349014)

WeatherContext.shared().requestCurrentForecast([coordinates]) { code, forecasts in
    guard code == .kNoError else {
        print("Error: \(code)")
        return
    }
    print("Current forecast count: \(forecasts.count)")
}

```

> 🚨 **DANGER**
>
> Verify that each `WeatherContextForecast` contains a `WeatherContextConditions` and that `parameters` is non-empty. If data is unavailable for the specified location, the API may return an empty array.

> 📝 **INFO**
>
> The result contains as many `WeatherContextForecast` objects as coordinates provided to the request.

## Get Hourly Weather Forecast[​](#get-hourly-weather-forecast "Direct link to Get Hourly Weather Forecast")

Use `requestHourlyForecast(_:hours:completionHandler:)` to retrieve hourly weather forecasts. The completion handler receives a nested array — one inner array of `WeatherContextForecast` objects per coordinate, with each element representing a single hour.

```swift
let coordinates = CoordinatesObject.coordinates(withLatitude: 48.864716, longitude: 2.349014)

WeatherContext.shared().requestHourlyForecast([coordinates], hours: 24) { code, forecasts in
    guard code == .kNoError else {
        print("Error: \(code)")
        return
    }
    print("Hourly forecasts per location: \(forecasts.count)")
}

```

> 🚨 **DANGER**
>
> The number of requested hours must not exceed 240. Exceeding this limit results in an empty response and a `.kOutOfRange` error.

## Get Daily Weather Forecast[​](#get-daily-weather-forecast "Direct link to Get Daily Weather Forecast")

Use `requestDailyForecast(_:days:completionHandler:)` to retrieve daily weather forecasts. The completion handler receives a nested array — one inner array of `WeatherContextForecast` objects per coordinate, with each element representing a single day.

```swift
let coordinates = CoordinatesObject.coordinates(withLatitude: 48.864716, longitude: 2.349014)

WeatherContext.shared().requestDailyForecast([coordinates], days: 10) { code, forecasts in
    guard code == .kNoError else {
        print("Error: \(code)")
        return
    }
    print("Daily forecasts per location: \(forecasts.count)")
}

```

> 🚨 **DANGER**
>
> The number of requested days must not exceed 10. Exceeding this limit results in an empty response and a `.kOutOfRange` error.

## Get Weather Forecast with Timestamp[​](#get-weather-forecast-with-timestamp "Direct link to Get Weather Forecast with Timestamp")

Use `requestForecast(_:completionHandler:)` to retrieve weather forecasts for specific coordinates and time offsets. Provide an array of `TimeDistanceCoordinatesObject` items — each pairing a location with a timestamp in seconds relative to the current time. The completion handler receives a nested array of `WeatherContextForecast` objects, one inner array per item in the request.

```swift
let coordinates = CoordinatesObject.coordinates(withLatitude: 48.864716, longitude: 2.349014)

let tdCoord = TimeDistanceCoordinatesObject(
    coordinates: coordinates,
    distance: 0,
    timestamp: 2 * 24 * 3600  // 2 days in seconds
)

WeatherContext.shared().requestForecast([tdCoord]) { code, forecasts in
    guard code == .kNoError else {
        print("Error: \(code)")
        return
    }
    print("Forecast count: \(forecasts.count)")
}

```

> 📝 **INFO**
>
> The `timestamp` parameter in `TimeDistanceCoordinatesObject` specifies the time offset into the future for the forecast, expressed in seconds relative to the current time.

## Cancel Requests[​](#cancel-requests "Direct link to Cancel Requests")

Call `cancelRequests()` to abort all pending weather requests:

```swift
WeatherContext.shared().cancelRequests()

```
