# Sass boilerplate

Building a collection of my common use Sass mixins, functions and snippets. All written with SCSS syntax.

- [Installation](#installation)
- [Revision history](#revision-history)
- [Library](#library)
	- [resetbase.scss](#resetbasescss)
	- [animationtransition.scss](#animationtransitionscss)
	- [collection.scss](#collectionscss)
	- [fontface.scss](#fontfacescss)
	- [respondwidth.scss](#respondwidthscss)
	- [retina.scss](#retinascss)
	- [vendor.scss](#vendorscss)

## Installation

```sh
$ npm install sassboilerplate
```

## Revision history

- `2021-05-18` Another update to Retina media query evaluation [method](https://css-tricks.com/snippets/css/retina-display-media-query/).
- `2017-12-05` Update to Retina media query evaluation [method](https://gist.github.com/marcedwards/3446599).
- `2017-03-15` Published initial [npm package](https://www.npmjs.com/package/sassboilerplate).
- `2015-03-24` Major rewrite supporting [Sass 3.4.0](http://sass-lang.com/documentation/file.SASS_CHANGELOG.html#3_4_0__18_August_2014_) and above only, along with dropping of IE8 support and various implementation changes.
- `2014-10-02` Final release for [Sass 3.2.0](http://sass-lang.com/documentation/file.SASS_CHANGELOG.html#320_10_august_2012). Future revisions make strong use of features such as [Sass maps](http://sass-lang.com/documentation/file.SASS_CHANGELOG.html#sassscript_maps) and the [`@error`](http://sass-lang.com/documentation/file.SASS_REFERENCE.html#_7) directive. Release has been tagged as [final3.2](https://github.com/magnetikonline/sassboilerplate/tree/final3.2) if anyone is after it.
- `2013-03-31` Initial release.

## Library

### [`resetbase.scss`](resetbase.scss)

Provides the following:

- `box-sizing: border-box` applied to all elements.
- CSS "reset" to base page elements.
- `font-size` reset so `1em = 10px`.
- The obligatory clearfix.
- Element hide/off canvas.
- Text overflow with ellipsis helper.
- Responsive image helper (images to their maximum width or container, preserving aspect ratio/height).
- Removal of the tap highlight color from Webkit based browsers on touch enabled devices, proves to be rather annoying at times (applies to iOS/Mobile Safari in particular).

Designed for use with all modern browsers and IE8+. The default `background`, `color` and `font-family` can be overridden via Sass variables:

```scss
$resetBase_baseBackground: #000;
$resetBase_baseColor: #fff;
$resetBase_baseFontFamily: Arial,'Helvetica Neue',Helvetica,sans-serif;

@import 'resetbase';
```

...which would generate:

```css
body,button,input,select,td,textarea,th {
  color: #fff;
  font-family: Arial,'Helvetica Neue',Helvetica,sans-serif;
}

body {
  background: #000;
  font-size: 62.5%;
  line-height: 1;
}
```

The `clearFix`, `hide`, `textOverflowEllipsis`, `responsiveImage` and `tapHighlightRemove` definitions are implemented as [Sass placeholders](http://sass-lang.com/docs/yardoc/file.SASS_REFERENCE.html#placeholders) and used with the `@extend` directive, rather than littering HTML markup with things like `class="clearfix"` / `class="hide"`:

```scss
.myclasswithclearfix {
  @extend %clearFix;
  border: 1px solid #000;
  color: #f00;
}

.mybutton {
  @extend %tapHighlightRemove;
  color: #f00;
  display: inline-block;
  font-weight: bold;
  height: 20px;
}
```

### [`animationtransition.scss`](animationtransition.scss)

Mixins for CSS3 `animation`, `@keyframes` and `transition` defintions.

Includes `-webkit-` vendor prefix (Firefox went prefix free since [version 16](https://caniuse.com/#feat=css-animation)).

### [`collection.scss`](collection.scss)

A series of `@function`s for fetching what I consider *collection* values, defined within Sass map data types.

Currently for colors, font sizes, spacing (margin/padding/etc.) and z-indexes:

```scss
@import 'collection';

$colorMap: (
  blue: #1d90e0,
  grey: #898b8d,
  orange: #da4300
);

$fontSizeMap: (
  base: 1.2em,
  title: 1.3em
);

$spacingMap: (
  base: 20px,
  double: 40px
);

$zIndexMap: (
  base: 1,
  modal: 200,
  ceiling: 1000
);

.firstclass {
  color: color(blue);
  font-size: fontSize();
  margin-left: sp();
  z-index: zIndex(ceiling);
}

.secondclass {
  color: color(orange);
  font-size: fontSize(title);
  margin-left: sp(double);
  z-index: zIndex();
}

.zindexadjust {
  z-index: zIndex(modal,-5);
}
```

...generating:

```css
.firstclass {
  color: #1d90e0;
  font-size: 1.2em;
  margin-left: 20px;
  z-index: 1000;
}

.secondclass {
  color: #da4300;
  font-size: 1.3em;
  margin-left: 40px;
  z-index: 1;
}

.zindexadjust {
  z-index: 195;
}
```

Note:

- Omitting map key argument from `fontSize()`, `sp()` and `zIndex()` will return the **first** value in each Sass map.
- Function `sp($key[,$multiplier])` optional `$multiplier` float parameter allows for adjustment of the requested spacing map value.
- Function `zIndex($key[,$shift])` optional `$shift` integer parameter allows increment/decrement to returned `z-index` map value.
- Invalid map keys given to any function will throw a fatal [Sass error](http://sass-lang.com/documentation/file.SASS_REFERENCE.html#_7).

### [`fontface.scss`](fontface.scss)

Cross browser compatible `@font-face` embedding of webfont files:

- `eot` (Internet Explorer)
- `svg` (iOS legacy)
- `ttf` (Safari, Android, iOS) and;
- `woff` (Standards)

Implemented `@font-face` definition credit to [Chris Coyier](https://css-tricks.com/snippets/css/using-font-face/).

The `fontFace()` function accepts optional `font-style` and `font-weight` arguments - both which default to `normal`:

```scss
@import 'fontface';
@include fontFace('MyFont','font/myfont');
```

...generating:

```css
@font-face {
  font-family: "MyFont";
  src: url("font/myfont.eot");
  src: url("font/myfont.eot?#iefix") format("embedded-opentype"),
    url("font/myfont.woff") format("woff"),
    url("font/myfont.ttf") format("truetype"),
    url("font/myfont.svg#MyFont") format("svg");
  font-style: normal;
  font-weight: normal;
}
```

### [`respondwidth.scss`](respondwidth.scss)
Media query mixins to assist with responsive width page layouts.

Breakpoints are defined in a `$respondWidthMap` map variable. As an example, how I typically implement using common device breakpoints:

```scss
@import 'respondwidth';

// respond breakpoint
// 997px - Infin (960px suggested frame/row width)
// 769px - 996px (732px suggested frame/row width)
// 481px - 768px (444px suggested frame/row width)
// 0px   - 480px (300px suggested frame/row width)
$respondWidthMap: (
  centi: 996px,
  micro: 768px,
  nano: 480px
);

.pageframe {
  margin: 0 auto;
  width: respondWidthGet(centi,-36px);

  @include respondWidthFrom(centi) {
    padding: 0 50px;
  }

  @include respondWidthFromUpTo(micro,centi) {
    width: respondWidthGet(micro,-36px);
  }

  @include respondWidthFromUpTo(nano,micro) {
    width: respondWidthGet(nano,-36px);
  }

  @include respondWidthUpTo(nano) {
    width: 300px;
  }
}
```

...generating:

```css
.pageframe {
  margin: 0 auto;
  width: 960px;
}

@media screen and (min-width: 997px) {
  .pageframe {
    padding: 0 50px;
  }
}

@media screen and (min-width: 769px) and (max-width: 996px) {
  .pageframe {
    width: 732px;
  }
}

@media screen and (min-width: 481px) and (max-width: 768px) {
  .pageframe {
    width: 444px;
  }
}

@media screen and (max-width: 480px) {
  .pageframe {
    width: 300px;
  }
}
```

Note:

- The generated `@media` query `min-width` is deliberately returned 1px *greater* than the `$respondWidthMap` map value defined to provide isolation between each breakpoint range.
- Invalid breakpoint map keys given to any of these functions; or a `from` breakpoint greater than `upTo` given to `respondWidthFromUpTo()` will throw a fatal Sass error.
- Function `respondWidthGet($key[,$shift])` returns the defined width for a given `$key`, useful for dependent calculations. An optional `$shift` integer parameter allows for increment/decrement to map value.

### [`retina.scss`](retina.scss)

Allows for creation of styles (typically images) which are targeted to "retina" high pixel density displays.

Implemented `@media` query credit to [Chris Coyier](https://css-tricks.com/snippets/css/retina-display-media-query/).

An example using `retinaImage()`:

```scss
@import 'retina';
$logoWidth: 200px;

.companylogo {
  background: url(companylogo.png) no-repeat 0 0;
  height: 200px;
  width: $logoWidth;

  @include retinaImage('companylogo2x.png',$logoWidth);
}
```

...generating:

```css
.companylogo {
  background: url(companylogo.png) no-repeat 0 0;
  height: 200px;
  width: 200px;
}

@media only screen and (-webkit-min-device-pixel-ratio: 1.3),
  only screen and (-o-min-device-pixel-ratio: 13/10),
  only screen and (min-resolution: 120dpi) {
  .companylogo {
    background-image: url(companylogo2x.png);
    background-size: 200px;
  }
}
```

Note:

- Width argument passed to `retinaImage()` is optional, defaulting to 100% (`background-size: 100%`) if not defined.
- A `100%` width is _usually_ suitable if the image is to span the full background width of the element, allowing the browser to determine a scaled height keeping the aspect ratio.
- Providing a specific width is (typically) only needed for CSS spriting tasks.

Also included is `retinaOnly()` for arbitrary CSS style blocks:

```scss
@import 'retina';

.shinybutton {
  font-weight: bold;
  height: 200px;
  width: 100px;

  @include retinaOnly() {
    // reward for retina is a red border
    border: 1px solid #f00;
  }
}
```

### [`vendor.scss`](vendor.scss)

Mixins for several CSS properties that (still) require vendor prefixes:

- `backface-visibility($value)`, providing `-webkit-` vendor prefix.
- `linear-gradient($angle,$stops)`, providing `-webkit-` vendor prefix with alternative (reversed) `$angle` syntax for `to top`, `to right`, etc.
- `transform($value)`, providing `-webkit-` and `-ms-` (required with [IE9](https://caniuse.com/#feat=transforms2d)) prefixes for `transform()`.
