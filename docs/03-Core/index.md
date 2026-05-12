# Core

This section covers the core runtime entities used across the Navigation SDK for iOS: geographic primitives, positions, landmarks, markers, overlays, routes, navigation instructions, traffic events, and image rendering APIs.

You can use these guides as a reference path from low-level map entities to real-time navigation data.

- [Base Entities](../docs/03-Core/01-Base%20Entities.md) -
  This page covers the fundamental building blocks of the iOS SDK: coordinates, paths, and geographic areas.
- [Positions](../docs/03-Core/02-Positions.md) - This page covers position handling in iOS using PositionObject and PositionContext.
- [Landmarks](../docs/03-Core/03-Landmarks.md) - A landmark is a rich point-of-interest entity represented by LandmarkObject.
- [Markers](../docs/03-Core/04-Markers.md) - A marker is a visual geometry represented by MarkerObject.
- [Overlays](../docs/03-Core/05-Overlays.md) - An overlay is an additional map layer with data stored on UNL servers, accessible in both online and offline modes.
- [Landmarks vs Markers vs Overlays](../docs//03-Core/06-Landmarks%20vs%20Markers%20vs%20Overlays.md) - When building map features, choose the entity that matches your data lifecycle and interaction model.
- [Routes](../docs//03-Core/07-Routes.md) - A route represents a navigable path between two or more landmarks (waypoints), including distance, estimated time, and navigation instructions.
- [Navigation Instructions](../docs//03-Core/08-Navigation%20Instructions.md) - The UNL Navigation SDK for iOS provides real-time navigation guidance through NavigationInstructionObject.
- [Traffic Events](../docs//03-Core/09-Traffic%20Events.md) - The UNL Navigation SDK for iOS provides real-time traffic information about delays, incidents, and road restrictions that can affect routing and navigation.
- [Images](../docs/03-Core/10-Images.md) - The UNL Navigation SDK for iOS uses ImageObject for plain SDK images and UIKit-native UIImage rendering for display.