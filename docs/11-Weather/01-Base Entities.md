# Base Entities

Weather-related data is organized into distinct classes, each designed to encapsulate specific weather information.

The main classes include `WeatherContextForecast`, `WeatherContextConditions`, and `WeatherContextParameter`. This guide provides a detailed explanation of each class and its purpose.

## WeatherContextForecast[​](#weathercontextforecast "Direct link to WeatherContextForecast")

The `WeatherContextForecast` class retains data such as the forecast update timestamp, the geographic location, and a single set of forecast conditions for a specific time slot.

| Property            | Type                       | Description                            |
| ------------------- | -------------------------- | -------------------------------------- |
| `updateTimestamp`   | `TimeObject?`              | Forecast update timestamp              |
| `coordinatesObject` | `CoordinatesObject?`       | Geographic location                    |
| `conditions`        | `WeatherContextConditions` | Forecast conditions for this time slot |

## WeatherContextConditions[​](#weathercontextconditions "Direct link to WeatherContextConditions")

The `WeatherContextConditions` class retains weather conditions for a given timestamp.

| Property      | Type                        | Description                                                                                   |
| ------------- | --------------------------- | --------------------------------------------------------------------------------------------- |
| `type`        | `String`                    | For possible values see [Predefined parameter type values](#predefined-parameter-type-values) |
| `stamp`       | `TimeObject?`               | Timestamp for condition                                                                       |
| `imageObject` | `ImageObject?`              | Image representation                                                                          |
| `details`     | `String`                    | Description translated according to the current SDK language                                  |
| `daylight`    | `WeatherDaylight`           | Daylight condition                                                                            |
| `parameters`  | `[WeatherContextParameter]` | Parameter list                                                                                |

### WeatherDaylight[​](#weatherdaylight "Direct link to WeatherDaylight")

The `WeatherDaylight` enum indicates the daylight state for a given set of conditions.

| Value                         | Description                      |
| ----------------------------- | -------------------------------- |
| `WeatherDaylightNotAvailable` | Daylight condition not available |
| `WeatherDaylightDay`          | Daytime                          |
| `WeatherDaylightNight`        | Nighttime                        |

### Predefined parameter type values[​](#predefined-parameter-type-values "Direct link to Predefined parameter type values")

The following strings are the common values for `WeatherContextConditions.type` and `WeatherContextParameter.type`.

| Value               | Description               | Unit |
| ------------------- | ------------------------- | ---- |
| `"AirQuality"`      | Air quality index         | -    |
| `"DewPoint"`        | Dew point temperature     | °C   |
| `"FeelsLike"`       | Apparent temperature      | °C   |
| `"Humidity"`        | Relative humidity         | %    |
| `"Pressure"`        | Atmospheric pressure      | mb   |
| `"Sunrise"`         | Sunrise time              | -    |
| `"Sunset"`          | Sunset time               | -    |
| `"Temperature"`     | Current temperature       | °C   |
| `"UV"`              | UV index                  | -    |
| `"Visibility"`      | Visibility distance       | km   |
| `"WindDirection"`   | Wind direction            | °    |
| `"WindSpeed"`       | Wind speed                | km/h |
| `"TemperatureLow"`  | Daily minimum temperature | °C   |
| `"TemperatureHigh"` | Daily maximum temperature | °C   |

> 🚨 **DANGER**
>
> The `WeatherContext` may return data with varying parameter types, depending on data availability. A response might include only a subset of the values listed above.

## WeatherContextParameter[​](#weathercontextparameter "Direct link to WeatherContextParameter")

The `WeatherContextParameter` class contains weather parameter data.

| Property | Type     | Description                                                                                   |
| -------- | -------- | --------------------------------------------------------------------------------------------- |
| `type`   | `String` | For possible values see [Predefined parameter type values](#predefined-parameter-type-values) |
| `value`  | `Double` | Value                                                                                         |
| `name`   | `String` | Name translated according to the current SDK language                                         |
| `unit`   | `String` | Unit translated according to the current SDK language                                         |
