# flutter_svg

[![Pub](https://img.shields.io/pub/v/flutter_svg.svg)](https://pub.dartlang.org/packages/flutter_svg) [![Coverage Status](https://coveralls.io/repos/github/dnfield/flutter_svg/badge.svg?branch=master)](https://coveralls.io/github/dnfield/flutter_svg?branch=master)

<!-- markdownlint-disable MD033 -->
<img src="https://raw.githubusercontent.com/dnfield/flutter_svg/7d374d7107561cbd906d7c0ca26fef02cc01e7c8/example/assets/flutter_logo.svg?sanitize=true" width="200px" alt="Flutter Logo which can be rendered by this package!">
<!-- markdownlint-enable MD033 -->

Draw SVG files using Flutter.

## Getting Started

Basic usage (to create an SVG rendering widget from an asset):

```dart
final String assetName = 'assets/image.svg';
final Widget svg = SvgPicture.asset(
  assetName,
  semanticsLabel: 'Acme Logo'
);
```

You can color/tint the image like so:

```dart
final String assetName = 'assets/up_arrow.svg';
final Widget svgIcon = SvgPicture.asset(
  assetName,
  colorFilter: ColorFilter.mode(Colors.red, BlendMode.srcIn),
  semanticsLabel: 'A red up arrow'
);
```

The default placeholder is an empty box (`LimitedBox`) - although if a `height`
or `width` is specified on the `SvgPicture`, a `SizedBox` will be used instead
(which ensures better layout experience). There is currently no way to show an
Error visually, however errors will get properly logged to the console in debug
mode.

You can also specify a placeholder widget. The placeholder will display during
parsing/loading (normally only relevant for network access).

```dart
// Will print error messages to the console.
final String assetName = 'assets/image_that_does_not_exist.svg';
final Widget svg = SvgPicture.asset(
  assetName,
);

final Widget networkSvg = SvgPicture.network(
  'https://site-that-takes-a-while.com/image.svg',
  semanticsLabel: 'A shark?!',
  placeholderBuilder: (BuildContext context) => Container(
      padding: const EdgeInsets.all(30.0),
      child: const CircularProgressIndicator()),
);
```

If you'd like to render the SVG to some other canvas, you can do something like:

```dart
import 'package:flutter_svg/flutter_svg.dart';
final String rawSvg = '''<svg ...>...</svg>''';
final PictureInfo pictureInfo = await vg.loadPicture(SvgStringLoader(rawSvg), null);

// You can draw the picture to a canvas:
canvas.drawPicture(pictureInfo.picture);

// Or convert the picture to a an image:
final ui.Image image = pictureInfo.picture.toImage(...);

pictureInfo.picture.dispose();
```

The `SvgPicture` helps to automate this logic, and it provides some convenience
wrappers for getting assets from multiple sources. Unlike the `vector_graphics`
package, this package _does not render the data to an `Image` at any point_.
This carries a performance penalty for some common use cases, but also allows
for more flexibility around scaling.

See [main.dart](/../master/example/lib/main.dart) for a complete sample.

## Precompiling and Optimizing SVGs

The vector_graphics backend supports SVG compilation which produces a binary
format that is faster to parse and can optimize SVGs to reduce the amount of
clipping, masking, and overdraw. The SVG compilation is provided by
[`package:vector_graphics_compiler`](https://pub.dev/packages/vector_graphics_compiler).

```sh
dart run vector_graphics_compiler -i assets/foo.svg -o assets/foo.svg.vec
```

The output `foo.svg.vec` can be loaded using the default constructor of
`SvgPicture`.

```dart
import 'package:flutter_svg/flutter_svg.dart';
import 'package:vector_graphics/vector_graphics.dart';

final Widget svg = SvgPicture(
  const AssetBytesLoader('assets/foo.svg.vec')
);
```

### Check SVG compatibility

An SVG can be tested for compatibility with the vector graphics backend by
running the compiler locally to see if any errors are thrown.

```sh
dart run vector_graphics_compiler -i $SVG_FILE -o $TEMPORARY_OUTPUT_TO_BE_DELETED --no-optimize-masks --no-optimize-clips --no-optimize-overdraw --no-tessellate
```

## Recommended Adobe Illustrator SVG Configuration
- In Styling: choose Presentation Attributes instead of Inline CSS because CSS is not fully supported.
- In Images: choose Embded not Linked to other file to get a single svg with no dependency to other files.
- In Objects IDs: choose layer names to add every layer name to svg tags or you can use minimal,it is optional.
![Export configuration](https://user-images.githubusercontent.com/2842459/62599914-91de9c00-b8fe-11e9-8fb7-4af57d5100f7.png)

## SVG sample attribution

SVGs in `/assets/w3samples` pulled from [W3 sample files](https://dev.w3.org/SVG/tools/svgweb/samples/svg-files/)

SVGs in `/assets/deborah_ufw` provided by @deborah-ufw

SVGs in `/assets/simple` are pulled from trivial examples or generated to test
basic functionality - some of them come directly from the SVG 1.1 spec. Some
have also come or been adapted from issues raised in this repository.

SVGs in `/assets/wikimedia` are pulled from [Wikimedia Commons](https://commons.wikimedia.org/wiki/Main_Page)

Android Drawables in `/assets/android_vd` are pulled from Android Documentation
and examples.

The Flutter Logo created based on the Flutter Logo Widget © Google.

The Dart logo is from
[dartlang.org](https://github.com/dart-lang/site-shared/blob/master/src/_assets/images/dart/logo%2Btext/horizontal/original.svg)
© Google

SVGs in `/assets/noto-emoji` are from [Google i18n noto-emoji](https://github.com/googlei18n/noto-emoji),
licensed under the Apache license.

Please submit SVGs that can't render properly (e.g. that don't render here the
way they do in chrome), as long as they're not using anything "probably out of
scope" (above).
