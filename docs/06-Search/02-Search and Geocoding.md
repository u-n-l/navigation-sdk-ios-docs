# Search & Geocoding features

This guide explains how to convert coordinates to addresses and addresses to coordinates, work with hierarchical address search, build auto-suggestions, and search along routes with `SearchContext`.

> ⚠️ **WARNING**
>
> These are simplified code snippets. To make sure the `SearchContext` object works correctly, it must be retained in memory for the duration of the search request. If the `SearchContext` instance is deallocated before the request completes, the completion handler will not be called. Check the examples linked at the end of this page for reference implementations.

## Convert coordinates to addresses[​](#convert-coordinates-to-addresses "Direct link to Convert coordinates to addresses")

Search around a coordinate to retrieve a matching `LandmarkObject`, then extract structured address fields with `getAddressFieldName(with:)`.

```swift
let searchContext = SearchContext()
searchContext.setThresholdDistance(50)

let coordinates = CoordinatesObject.coordinates(withLatitude: 51.519305, longitude: -0.128022)

searchContext.searchAround(withLocation: coordinates) { results in
    guard let landmark = results.first else {
        print("No results found")
        return
    }

    let country = landmark.getAddressFieldName(with: .country)
    let city = landmark.getAddressFieldName(with: .city)
    let street = landmark.getAddressFieldName(with: .streetName)
    let streetNumber = landmark.getAddressFieldName(with: .streetNumber)

    let fullAddress = "\(streetNumber) \(street), \(city), \(country)"
    print(fullAddress)
}

```

Common address fields include:

* `.country`
* `.city`
* `.streetName`
* `.streetNumber`
* `.postalCode`
* `.state`
* `.district`
* `.countryCode`

## Convert addresses to coordinates[​](#convert-addresses-to-coordinates "Direct link to Convert addresses to coordinates")

Address search uses a hierarchical structure where each node is represented by a `LandmarkObject` and an `AddressLevelType`.

> 🚨 **DANGER**
>
> Address hierarchies vary by country. Some countries do not provide state or province levels. Use `hasAddressSearchState(withCountry:)` or move through the available levels dynamically.

### Search for countries[​](#search-for-countries "Direct link to Search for countries")

Use country-level search when you need the root landmark for a guided address-search flow.

```swift
let searchContext = SearchContext()

searchContext.addressSearchCountries(withQuery: "Germany") { results in
    if results.isEmpty {
        print("No results")
    } else {
        print("Countries found: \(results.count)")
    }
}

```

You can also get a country directly from its ISO code:

```swift
let searchContext = SearchContext()
let country = searchContext.addressSearchGetCountry(withIsoCode: "ESP")
print(country.getLandmarkName())

```

### Navigate the address hierarchy[​](#navigate-the-address-hierarchy "Direct link to Navigate the address hierarchy")

Search through the address structure from country to city, street, and house number using `addressSearch(withLandmark:level:query:completionHandler:)`.

`AddressLevelType` values include `noDetail`, `country`, `state`, `county`, `district`, `city`, `settlement`, `postalCode`, `street`, `streetSection`, `streetLane`, `streetAlley`, `houseNumber`, and `crossing`.

```swift
func searchAddress(
    searchContext: SearchContext,
    landmark: LandmarkObject,
    level: AddressLevelType,
    text: String,
    completion: @escaping (LandmarkObject?) -> Void
) {
    searchContext.addressSearch(withLandmark: landmark, level: level, query: text) { results in
        completion(results.first)
    }
}

```

Use this helper to walk down the hierarchy step by step:

```swift
let searchContext = SearchContext()
let country = searchContext.addressSearchGetCountry(withIsoCode: "ESP")

searchAddress(searchContext: searchContext, landmark: country, level: .city, text: "Barcelona") { city in
    guard let city else { return }

    searchAddress(searchContext: searchContext, landmark: city, level: .street, text: "Carrer de Mallorca") { street in
        guard let street else { return }

        searchAddress(searchContext: searchContext, landmark: street, level: .houseNumber, text: "401") { houseNumber in
            guard let houseNumber else { return }
            print(houseNumber.getLandmarkName())
        }
    }
}

```

## Implement auto-suggestions[​](#implement-auto-suggestions "Direct link to Implement auto-suggestions")

Provide real-time suggestions by reissuing a search whenever the search text changes. Cancel the previous request before starting a new one.

```swift
final class SearchViewController: UIViewController, UISearchBarDelegate {
    let searchContext = SearchContext()
    var suggestions: [LandmarkObject] = []

    func searchBar(_ searchBar: UISearchBar, textDidChange searchText: String) {
        updateSuggestions(for: searchText)
    }

    private func updateSuggestions(for text: String) {
        searchContext.cancelSearch()

        guard !text.isEmpty else {
            suggestions = []
            return
        }

        searchContext.setAllowFuzzyResults(true)

        let reference = CoordinatesObject.coordinates(withLatitude: 48.8566, longitude: 2.3522)

        searchContext.search(withQuery: text, location: reference) { [weak self] results in
            self?.suggestions = results
            // Reload your table view or collection view here.
        }
    }
}

```

Set `setAllowFuzzyResults(true)` to improve partial match behavior. Replace the reference coordinates with the user's position or the current map center for better ranking.

## Search along routes[​](#search-along-routes "Direct link to Search along routes")

Find landmarks and POIs around a route using `searchAlong(withRoute:query:completionHandler:)`.

Use `setThresholdDistance(_:)` to control how far from the route results may be returned.

```swift
let searchContext = SearchContext()
searchContext.setThresholdDistance(500)

searchContext.searchAlong(withRoute: route, query: "charging") { results in
    if results.isEmpty {
        print("No results")
    } else {
        print("Route search results: \(results.count)")
        for landmark in results {
            print(landmark.getLandmarkName())
        }
    }
}

```

`route` must be a valid `RouteObject`, for example returned by the routing APIs.

