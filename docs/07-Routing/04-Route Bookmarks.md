# Route Bookmarks

This guide explains how to store, manage, and retrieve route collections as bookmarks between application sessions using `RouteBookmarksObject`.

## Create a bookmarks collection[​](#create-a-bookmarks-collection "Direct link to Create a bookmarks collection")

Create a new bookmarks collection using `RouteBookmarksObject(fileName:folderName:)` with a unique name. If a collection with the same name already exists, it is opened:

```swift
let bookmarks = RouteBookmarksObject(fileName: "my_trips", folderName: nil)

```

Access the file path of the stored collection:

```swift
let path = bookmarks.getFilePath()

```

## Add routes[​](#add-routes "Direct link to Add routes")

Add a route to the collection using `add(_:list:preferences:overwrite:)`. Provide a unique name, a waypoints array, and optionally route preferences and an overwrite flag:

```swift
bookmarks.add(
    "Home to Office",
    list: [homeLandmark, officeLandmark],
    preferences: myPreferences,
    overwrite: false
)

```

**Parameters:**

* **`name`** — Unique route name.
* **`list`** — Array of `LandmarkObject` waypoints defining the route.
* **`preferences`** — Optional `RoutePreferencesObject`. Pass `nil` to store with no preferences.
* **`overwrite`** — Replace an existing route with the same name (default: `false`).

> 📝 **INFO**
>
> If a route with the same name exists and `overwrite` is `false`, the operation fails silently. Use `getBaseUniqueName(_:)` from `RouteBookmarksObject` to generate a guaranteed unique name from waypoints.

## Import routes from files[​](#import-routes-from-files "Direct link to Import routes from files")

Import multiple routes from a bookmarks file using `addTrips(_:skipDuplicates:)`. Returns the number of imported routes, or `0` on failure:

```swift
let count = bookmarks.addTrips("/path/to/bookmarks_file", skipDuplicates: true)
if count == 0 {
    print("Import failed — check the file path")
} else {
    print("\(count) trips imported successfully")
}

```

## Export routes to files[​](#export-routes-to-files "Direct link to Export routes to files")

Export a specific route to a file using `export(toFile:path:)` with the route index and destination path:

```swift
let success = bookmarks.export(toFile: 0, path: "/path/to/exported_route")
if success {
    print("Export successful")
} else {
    print("Export failed — route index may not exist or path may be unwritable")
}

```

**Common failure reasons:**

* Index out of bounds.
* Destination directory does not exist or is not writable.

## Access route details[​](#access-route-details "Direct link to Access route details")

Get the number of routes in the collection using `getSize()`:

```swift
let count = bookmarks.getSize()

```

Get details of a specific route by index:

```swift
let name       = bookmarks.getName(0)
let waypoints  = bookmarks.getWaypoints(0) // [LandmarkObject]
let prefs      = bookmarks.getPreferences(0)
let timestamp  = bookmarks.getTimestamp(0) // TimeObject?

```

> 📝 **INFO**
>
> `getTimestamp(_:)` returns when the route was added or last modified.

### Sort bookmarks[​](#sort-bookmarks "Direct link to Sort bookmarks")

Change the sort order using `setSortOrder(_:)`:

```swift
bookmarks.setSortOrder(.byDate)  // RouteBookmarksSortByDate (default) — most recent first
bookmarks.setSortOrder(.byName)  // RouteBookmarksSortByName — alphabetical

```

**Available `RouteBookmarksSort` values:**

| Value                      | Description                   |
| -------------------------- | ----------------------------- |
| `RouteBookmarksSortByDate` | Most recent first (default).  |
| `RouteBookmarksSortByName` | Alphabetical ascending order. |

### Configure auto-delete mode[​](#configure-auto-delete-mode "Direct link to Configure auto-delete mode")

When auto-delete mode is enabled, the underlying database file is deleted when the `RouteBookmarksObject` is deallocated:

```swift
bookmarks.setAutoDeleteMode(true)

```

## Update routes[​](#update-routes "Direct link to Update routes")

Update an existing route using `update(_:name:list:preferences:)` with the route index and updated values:

```swift
bookmarks.update(
    0,
    name: "Updated Name",
    list: [newStart, newEnd],
    preferences: newPrefs
)

```

> 📝 **INFO**
>
> The `update` method replaces all fields. To keep existing waypoints or preferences, first retrieve them with `getWaypoints(_:)` and `getPreferences(_:)`.

## Remove routes[​](#remove-routes "Direct link to Remove routes")

Remove a route by index using `remove(_:)`:

```swift
bookmarks.remove(0)

```

Clear all routes from the collection using `clear()`:

```swift
bookmarks.clear()

```
