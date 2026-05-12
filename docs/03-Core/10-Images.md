# Images

The UNL Navigation SDK for iOS uses `ImageObject` for plain SDK images and UIKit-native `UIImage` rendering for display. Some APIs expose `ImageObject` so you can control rendering yourself, while navigation-oriented APIs render `UIImage` directly.

***

## Understand the image model[​](#understand-the-image-model "Direct link to Understand the image model")

The SDK exposes three practical image paths:

* `ImageObject` for plain SDK images that you render on demand
* `AbstractGeometryImageObject` for schematic turn geometry with custom colors
* Direct `UIImage` rendering from APIs such as `NavigationInstructionObject` and `PositionObject`

Common sources of `ImageObject` include `ImageDatabaseObject`, `LandmarkObject`, `OverlayCategoryObject`, `MapDetailsContext`, and weather condition objects.

> 📝 **INFO**
>
> You can render `ImageObject` directly to `UIImage`, or use APIs that already return `UIImage`.

***

## ImageObject structure[​](#imageobject-structure "Direct link to ImageObject structure")

`ImageObject` is the base representation for SDK images.

| Method                                 | Returns       | Description                                                                         |
| -------------------------------------- | ------------- | ----------------------------------------------------------------------------------- |
| `init(dataBuffer:format:)`             | `ImageObject` | Create an image object from encoded image data                                      |
| `getType()`                            | `ImageType`   | Logical image kind (`Base`, `AbstractGeometry`, `RoadInfo`, `Signpost`, `LaneInfo`) |
| `getUid()`                             | `UInt32`      | Stable identifier for redraw optimization                                           |
| `isValid()`                            | `Bool`        | Checks whether the image can be rendered                                            |
| `isScalable()`                         | `Bool`        | Whether the underlying representation is scalable                                   |
| `getSize()`                            | `CGSize`      | Recommended/native size                                                             |
| `getAspectRatio()`                     | `CGSize`      | Aspect ratio expressed as width/height proportions                                  |
| `renderImageWithSize(_:)`              | `UIImage?`    | Render at an explicit size                                                          |
| `renderImageWithSize(_:scale:ppi:)`    | `UIImage?`    | Render with explicit scale and PPI                                                  |
| `renderAspectRatioImage(_:)`           | `UIImage?`    | Render using a target height while preserving aspect ratio                          |
| `renderAspectRatioImage(_:scale:ppi:)` | `UIImage?`    | Aspect-ratio render with explicit scale and PPI                                     |

`ImageFormat` supports `Bmp`, `Jpeg`, `Gif`, `Png`, `Tga`, `WebP`, and `AutoDetect`.

***

## Render Images[​](#render-images "Direct link to Render Images")

Use `renderImageWithSize(_:)` when you need an exact size. Use `renderAspectRatioImage(_:)` when icon proportions must stay stable. The following examples demonstrate this from a variety of image sources. The same workflow applies to all `ImageObject` instances, regardless of origin.

***

## Get and render image objects from the SDK[​](#get-and-render-image-objects-from-the-sdk "Direct link to Get and render image objects from the SDK")

Many SDK objects already provide `ImageObject`, so you can render them with the same workflow.

Common examples:

* `ImageDatabaseObject.getImageById(_:)`
* `ImageDatabaseObject.getPinStartImage()` and `getPinStopImage()`
* `LandmarkObject.getImage()` and `getExtraImage()`
* `OverlayCategoryObject.getImage()`

```swift
let imageDatabase = ImageDatabaseObject()

if let startPin = imageDatabase.getPinStartImage() {
	pinImageView.image = startPin.renderAspectRatioImage(40)
}

if let landmarkImage = landmark.getImage() {
	landmarkImageView.image = landmarkImage.renderImageWithSize(
		CGSize(width: 48, height: 48)
	)
}

if let categoryImage = overlayCategory.getImage() {
	categoryImageView.image = categoryImage.renderAspectRatioImage(28)
}

```

If you only need a ready-to-display landmark icon, `LandmarkObject` also provides `getLandmarkImage(_:)`, which returns a cached `UIImage` directly.

***

## Render abstract geometry images[​](#render-abstract-geometry-images "Direct link to Render abstract geometry images")

Use `AbstractGeometryImageObject` when you want a schematic turn image with your own colors. The most common source is `TurnDetailsObject.getAbstractGeometryImage()`.

```swift
if let turnDetails = instruction.getNextTurnDetails(),
   let geometryImage = turnDetails.getAbstractGeometryImage() {
	let image = geometryImage.getImage(
		CGSize(width: 72, height: 72),
		colorActiveInner: .white,
		colorActiveOuter: .systemBlue,
		colorInactiveInner: UIColor.white.withAlphaComponent(0.25),
		colorInactiveOuter: .lightGray
	)

	nextTurnImageView.image = image
}

```
