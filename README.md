# Sass boilerplate
Building a collection of my common use Sass mixins, functions and snippets. All written with SCSS syntax.

- [Revision history](#revision-history)
- [Library](#library)
	- [resetbase.scss](#resetbasescss)
	- [animationtransition.scss](#animationtransitionscss)
	- [collection.scss](#collectionscss)
	- [fontface.scss](#fontfacescss)
	- [respondwidth.scss](#respondwidthscss)
	- [retina.scss](#retinascss)
	- [vendor.scss](#vendorscss)

## Revision history
- 2015-03-24 - Major rewrite supporting [Sass 3.4.0](http://sass-lang.com/documentation/file.SASS_CHANGELOG.html#340_18_august_2014) and above only, along with dropping of IE8 support and various implementation changes.
- 2014-10-02 - Final release supporting [Sass 3.2.0](http://sass-lang.com/documentation/file.SASS_CHANGELOG.html#320_10_august_2012). Future revisions make strong use of features such as [Sass maps](http://sass-lang.com/documentation/file.SASS_CHANGELOG.html#sassscript_maps) and the [`@error`](http://sass-lang.com/documentation/file.SASS_REFERENCE.html#_7) directive. Release has been tagged as [final3.2](https://github.com/magnetikonline/sassboilerplate/tree/final3.2) if anyone is after it.
- 2013-03-31 - Initial release.

## Library

### resetbase.scss
[resetbase.scss](resetbase.scss) does the following:
- Applies a CSS "reset" (base page element styles).
- `box-sizing: border-box` applied to all elements.
- `font-size` reset so `1em = 10px`.
- The obligatory clearfix.
- Element hide/off canvas.
- Text overflow with ellipsis helper.
- Responsive image helper (images to their maximum width or container, preserving aspect ratio/height).
- Removal of the tap highlight color from Webkit based browsers on touch enabled devices, proves to be rather annoying at times (applies to iOS/Mobile Safari in particular).

Designed for use with all modern browsers and IE8+. The default `background`, `color` and `font-family` can be overridden via defined Sass variables:

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

The `clearFix`, `hide`, `textOverflowEllipsis`, `responsiveImage` and `tapHighlightRemove` style definitions have each been implemented as [Sass placeholders](http://sass-lang.com/docs/yardoc/file.SASS_REFERENCE.html#placeholders), used with the `@extend` directive in target styles, rather than littering HTML markup with things like `class="clearfix"` / `class="hide"`:

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

### animationtransition.scss
[animationtransition.scss](animationtransition.scss) mixins for cross browser compatible CSS3 `animation`, `@keyframes` and `transition`.

Includes `-webkit-` and `-o-` vendor prefixes (Firefox went prefix free from [version 16](http://caniuse.com/css-animation)).

### collection.scss
[collection.scss](collection.scss) is a series of `@function`s for fetching what I consider *collection* values, which are kept within Sass map data types.

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
- Omitting the key parameter from `fontSize()`, `sp()` and `zIndex()` will return the **first** item in each defined Sass map - considered your default.
- The `sp()` function accepts an optional *multiplier* float to adjust the given spacing map value.
- The `zIndex()` function accepts an optional *shift* integer to plus/minus the given `z-index` map value.
- Invalid map keys given to any of these functions will throw a fatal error.

### fontface.scss
[fontface.scss](fontface.scss) for cross browser compatible `@font-face` embedding of font types:
- `eot` (Internet Explorer)
- `svg` (iOS legacy)
- `ttf` (Safari, Android, iOS) and;
- `woff` (Standards)

The `fontFace()` function accepts optional `font-style` and `font-weight` arguments - both which default to `normal`.

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

### respondwidth.scss
[respondwidth.scss](respondwidth.scss) media query mixins for responsive width layouts.

Breakpoints are defined in a central `$respondWidthMap` Sass map variable. As an example, how I typically implement using common device breakpoints:

```scss
@import 'respondwidth';

// respond breakpoint
// 997px - Infin (960px suggested frame/row width)
// 769px - 996px (732px suggested frame/row width)
// 481px - 768px (444px suggested frame/row width)
// 0px	 - 480px (300px suggested frame/row width)
$respondWidthMap: (
	centi: 996px,
	micro: 768px,
	nano: 480px
);

.pageframe {
	margin: 0 auto;
	width: 960px;

	@include respondWidthFrom(centi) {
		padding: 0 50px;
	}

	@include respondWidthFromUpTo(micro,centi) {
		width: 732px;
	}

	@include respondWidthFromUpTo(nano,micro) {
		width: 444px;
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
- The generated `@media` query `min-width` is deliberately returned 1px *greater* than the defined `$respondWidthMap` map value to ensure isolation between each breakpoint range.
- Invalid breakpoint map keys given to any of these functions; or a `from` breakpoint greater than `upTo` given to `respondWidthFromUpTo()` will throw a fatal error.

### retina.scss
[retina.scss](retina.scss) allows for the creation of styles (typically images) which are targeted only to "retina" style, high pixel density displays. Using the `@media` query created by [Thomas Fuchs](https://gist.github.com/madrobby/4161897/) and implemented by [Bourbon](http://bourbon.io/docs/#hidpi-media-query).

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

@media (min--moz-device-pixel-ratio: 1.5),
	(-o-min-device-pixel-ratio: 3 / 2),
	(-webkit-min-device-pixel-ratio: 1.5),
	(min-device-pixel-ratio: 1.5),
	(min-resolution: 144dpi),
	(min-resolution: 1.5dppx) {
	.companylogo {
		background-image: url(companylogo2x.png);
		background-size: 200px;
	}
}
```

Note:
- The width parameter passed to `retinaImage()` is optional, will default to 100% (`background-size: 100%`) if not given.
- This is typically what you want if the background image is to span the full width of the element, allowing the browser to determine the natural scaled height (matching the aspect ratio).
- Providing a specific width I find is (usually) only needed for CSS spriting tasks.

Also included is a `retinaOnly()` mixin for adding any arbitrary CSS style rules:
```scss
@import 'retina';

.shinybutton {
	font-weight: bold;
	height: 200px;
	width: 100px;

	@include retinaOnly() {
		// reward for retina display is a red border
		border: 1px solid #f00;
	}
}
```

### vendor.scss
[vendor.scss](vendor.scss) holds mixins for several CSS properties that (still) require vendor prefixes:
- `backface-visibility($value)`, providing `-webkit-` vendor prefix.
- `linear-gradient($angle,$stops)`, providing `-webkit-` and `-o-` vendor prefixes with alternative (reversed) `$angle` syntax for `to top`, `to right`, etc.
- `transform($value)`, providing `-webkit-` and `-ms-` (required with [IE9](http://caniuse.com/#feat=transforms2d)) prefixes for `transform()`.
