# Sass boilerplate
Building a collection of my common use Sass components and snippets, using SCSS syntax.
- [resetbase.scss](#resetbasescss)
- [animationtransition.scss](#animationtransitionscss)
- [fontface.scss](#fontfacescss)
- [respondwidth.scss](#respondwidthscss)
- [retina.scss](#retinascss)
- [vendor.scss](#vendorscss)

## resetbase.scss
[resetbase.scss](resetbase.scss) does the following:
- Applies a CSS reset / base page styles
- `box-sizing: border-box` for all elements
- `font-size` reset so `1em = 10px`
- The obligatory clearfix
- Element hide/off canvas
- Text overflow with ellipsis helper
- Responsive image helper (images to their maximum width or container, preserving aspect ratio/height)
- Removal of the tap highlight color from Webkit based browsers on touch enabled devices, proves to be rather annoying at times - iOS/Mobile Safari in particular.

Designed for use with all modern browsers and IE8+. The default `background`, `color` and `font-family` can be overridden:

```scss
$resetBase_baseBackground: #000;
$resetBase_baseColor: #fff;
$resetBase_baseFontFamily: Arial,'Helvetica Neue',Helvetica,sans-serif;

@import 'resetbase';
```

...which would generate:

```css
body,button,input,select,td,th,textarea {
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

## animationtransition.scss
[animationtransition.scss](animationtransition.scss) for cross browser compatible CSS3 `animation`, `@keyframes` and `transition` mixin's.

## fontface.scss
[fontface.scss](fontface.scss) for cross browser compatible `@font-face` embedding of the full `eot` (IE), `woff` (standards), `ttf` (Safari, Android, iOS) and `svg` (legacy iOS) stack:

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

## respondwidth.scss
[respondwidth.scss](respondwidth.scss) media query mixin's for responsive width page layouts. As an example, how I typically implement a responsive pageframe/row using common device breakpoints/widths:

```scss
@import 'respondwidth';

// respond breakpoint
// 997px - Infin (960px)
// 769px - 996px (732px)
// 481px - 768px (444px)
// 0px	 - 480px (300px)
$respondWidthCenti: 996px;
$respondWidthMicro: 768px;
$respondWidthNano: 480px;

.pageframe {
	margin: 0 auto;
	width: 960px;

	@include respondWidthFromUpTo($respondWidthMicro,$respondWidthCenti) {
		width: 732px;
	}

	@include respondWidthFromUpTo($respondWidthNano,$respondWidthMicro) {
		width: 444px;
	}

	@include respondWidthUpTo($respondWidthNano) {
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

Note the `max-width` is deliberately set 1px lower than the width given to give isolation between each breakpoint width range.

## retina.scss
[retina.scss](retina.scss) allows for the creation of styles (usually images) which are targeted only to "retina" style, high pixel density displays. Using the `@media` query created by [Thomas Fuchs](https://gist.github.com/madrobby/4161897/) and implemented by [Bourbon](http://bourbon.io/docs/#hidpi-media-query).

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

@media (min--moz-device-pixel-ratio: 1.5), (-o-min-device-pixel-ratio: 3 / 2), (-webkit-min-device-pixel-ratio: 1.5), (min-device-pixel-ratio: 1.5), (min-resolution: 144dpi), (min-resolution: 1.5dppx) {
	.companylogo {
		background-image: url(companylogo2x.png);
		background-size: 200px;
	}
}
```

**Note:** The width parameter passed to `retinaImage()` is optional, will default to 100% (`background-size: 100%`) if not given. This is typically what you want if the background image is to span the full width of the element allowing the browser to determine the scalled height (matching the aspect ratio). Providing a specific width is usually only needed for CSS sprites/etc.

Also included is a `retinaOnly()` mixin for adding any arbitrary CSS style rules:
```scss
@import 'retina';

.shinybutton {
	font-weight: bold;
	height: 200px;
	width: 100px;

	@include retinaOnly() {
		// reward for having a retina display is a red border
		border: 1px solid #f00;
	}
}
```


## vendor.scss
[vendor.scss](vendor.scss) holds mixin's for CSS styles that (still) require vendor prefixes - currently:
- `border-radius($value)`
- `linear-gradient($angle,$stops)`, providing `-webkit` prefix with alternative (reversed) `$angle` syntax for `to top`, `to right`, etc.
- `opacity($value)`, supplying `filter: alpha(opacity=XX)` and `zoom: 1` syntax for IE8 compatibility.
