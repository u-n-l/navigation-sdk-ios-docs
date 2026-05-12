# Introduction

The UNL Navigation SDK for iOS provides offline functionality through map download capabilities.

Download maps for entire countries or specific regions to enable offline access. Users can search for landmarks, calculate routes, navigate, and explore maps without an internet connection.

> ⚠️ **WARNING**
>
> Overlays, live traffic information, and other online-dependent services are unavailable in offline mode.

The UNL Navigation SDK for iOS does not provide an automatic update mechanism for the map if using offline maps. Map updates must be triggered manually using the `MapsContext` update API. Automatic map updates are only available when no regions are downloaded, allowing the SDK to fetch the latest map version from the UNL servers. 

New map versions are released every few weeks globally, providing regular enhancements and improvements.

## Offline feature availability[​](#offline-feature-availability "Direct link to Offline feature availability")

### Core entities[​](#core-entities "Direct link to Core entities")

| Entity     | Offline availability                                                                                                     |
| ---------- | ------------------------------------------------------------------------------------------------------------------------ |
| Landmarks  | ✓ Fully available                                                                                                        |
| Markers    | ✓ Fully available                                                                                                        |
| Position   | ⚠️ Partial - Raw position data is always accessible, but map-matched position data requires downloaded or cached regions |
| Overlays   | ✗ Not available                                                                                                          |
| Routes     | ⚠️ Partial - traffic events are unavailable without internet connection                                                  |
| Navigation | ✓ Fully available if navigation starts on an offline-calculated route                                                    |

> 📝 **INFO**
>
> Map tiles are automatically cached based on your location, camera position, and calculated routes to enhance performance and offline accessibility.

### Map controller[​](#map-controller "Direct link to Map controller")

`MapViewController` methods function as expected in offline mode. Methods that request data from regions not covered or cached return empty results.

**Example:** Calling `getNearestLocations(_:)` with coordinates outside downloaded areas returns an empty array.

### Map styling[​](#map-styling "Direct link to Map styling")

You can set a new map style in offline mode if the style has been downloaded beforehand using `MapStyleContext` or if using a custom style.

> ⚠️ **WARNING**
>
> Styles containing extensive data, such as satellite or weather styles, may not display meaningful information when offline.

### Services[​](#services "Direct link to Services")

The following contexts are available offline within downloaded map regions:

* `NavigationContext`
* `SearchContext`
* `Routing`
* `LandmarkStoreContext`
* `PositionContext`

The following are **not supported** in offline mode:

* Overlay services

### SDK settings[​](#sdk-settings "Direct link to SDK settings")

Most SDK features function independently of internet connection status, except authorization-related functionalities.

> 🚨 **DANGER**
>
> SDK authorization requires an active internet connection. You cannot authorize the Navigation SDK for iOS without being online.

