# Sass boilerplate
Building a collection of my common use Sass components and snippets, using SCSS syntax.
- [resetbase.scss](#resetbasescss)
- [animationtransition.scss](#animationtransitionscss)
- [respondwidth.scss](#respondwidthscss)
- [retina.scss](#retinascss)
- [vendor.scss](#vendorscss)

## resetbase.scss
[resetbase.scss](resetbase.scss) does the following:
- Applies a CSS reset / base page styles
- `box-sizing: border-box` for all elements
- `font-size` reset so `1em = 10px`
- Defines the obligatory clearfix
- Element hide/off canvas
- Text overflow with ellipsis helper
- Responsive image helper (images to their max width or container, preserving aspect ratio/height)

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

The `clearfix`, `hide`, `textoverflowellipsis` and `imgresponsive` classes have each been implemented as a [SASS placeholder](http://sass-lang.com/docs/yardoc/file.SASS_REFERENCE.html#placeholders), used with `@extend` in target styles, rather than littering markup with `class="clearfix"` / `class="hide"` / etc.:

```scss
.myclasswithclearfix {
	@extend %clearfix;
	border: 1px solid #000;
	color: #f00;
}
```

## animationtransition.scss
[animationtransition.scss](animationtransition.scss) for cross browser compatible CSS3 `animation`,  `@keyframes` and `transition` mixin's.

## respondwidth.scss
[respondwidth.scss](respondwidth.scss) media query mixin's for responsive width page layouts. As an example, how I typically implement a responsive pageframe/row using common device breakpoints/widths:

```scss
@import 'respondwidth';

// respond breakpoints
// 996px - Infin (996px)
// 768px - 995px (768px)
// 480px - 767px (456px)
// 0px	 - 479px (300px)
$respondWidthFull: 996px;
$respondWidthMicro: 768px;
$respondWidthNano: 480px;

.pageframe {
	margin: 0 auto;
	width: 996px;

	@include respondWidthFromUpTo($respondWidthMicro,$respondWidthFull) {
		width: 768px;
	}

	@include respondWidthFromUpTo($respondWidthNano,$respondWidthMicro) {
		width: 456px;
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
	width: 996px;
}

@media screen and (min-width: 768px) and (max-width: 995px) {
	.pageframe {
    	width: 768px;
	}
}

@media screen and (min-width: 480px) and (max-width: 767px) {
	.pageframe {
		width: 456px;
	}
}

@media screen and (max-width: 479px) {
	.pageframe {
		width: 300px;
	}
}
```

Note the `max-width` is deliberately set 1px lower than the width given to avoid clashing of each breakpoint range.

## retina.scss
[retina.scss](retina.scss) allows for the creation of styles (usually images) which are targeted only to "retina" style, high pixel density displays. Using the `@media` query created/devised by [Thomas Fuchs](https://gist.github.com/madrobby/4161897/).

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
