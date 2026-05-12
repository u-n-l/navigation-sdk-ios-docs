# Landmarks vs Markers vs Overlays

When building map features, choose the entity that matches your data lifecycle and interaction model.

| Characteristic          | Landmarks                                  | Markers                                        | Overlays                                           |
| ----------------------- | ------------------------------------------ | ---------------------------------------------- | -------------------------------------------------- |
| Primary class           | `LandmarkObject`                           | `MarkerObject` + `MarkerCollectionObject`      | `OverlayInfoObject` + `OverlayItemObject`          |
| On map by default       | Yes, for enabled stores/categories         | No, must be added explicitly                   | Yes, when present in style and enabled             |
| Searchable              | Yes                                        | No                                             | Yes                                                |
| Route waypoint friendly | Yes                                        | No                                             | Indirect (convert to landmark/coordinates)         |
| Metadata richness       | High (address, contact, categories, media) | Low (geometry-focused)                         | High (preview data, categories, URLs)              |
| Creation model          | Local creation + persistent stores         | Local creation                                 | Style/server dataset driven                        |
| Offline behavior        | Yes                                        | Yes                                            | Supported when overlay offline data is available   |
| Best use                | POIs, user places, search/routing entities | Temporary graphics, shapes, visual annotations | Shared thematic datasets (safety, transit, social) |

## Quick selection guide[​](#quick-selection-guide "Direct link to Quick selection guide")

* Choose **landmarks** when you need searchable, categorizable places with full metadata.
* Choose **markers** when you need fast, fully-custom visual geometry on the map.
* Choose **overlays** when data is maintained as a shared dataset and controlled by style/service configuration.
