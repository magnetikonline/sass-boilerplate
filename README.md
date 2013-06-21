# Sass boilerplate
Building a collection of my common use Sass components and snippets, using SCSS syntax.
- [resetbase.scss](#resetbasescss)
- [animationtransition.scss](#animationtransitionscss)
- [retinaonly.scss](#retinaonlyscss)
- [vendor.scss](#vendorscss)

## resetbase.scss
[resetbase.scss](resetbase.scss) applies a CSS reset, base page styles, `box-sizing: border-box` for all elements, font-size reset so `1em = 10px` and defines the obligatory clearfix. Designed for use with all modern browsers and IE8+.

Default `background`, `color` and `font-family` can be overridden:

```scss
$resetBase_baseBackground: #000;
$resetBase_baseColor: #fff;
$resetBase_baseFontFamily: Arial,'Helvetica Neue',Helvetica,sans-serif;

@import 'resetbase.scss';
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

The `clearfix` class has been implemented as a [SASS placeholder](http://sass-lang.com/docs/yardoc/file.SASS_REFERENCE.html#placeholders) which is used with `@extend` in target styles, rather than littering HTML markup with `class="clearfix"`:

```scss
.myclasswithclearfix {
	@extend %clearfix;
	border: 1px solid #000;
	color: #f00;
}
```

## animationtransition.scss
[animationtransition.scss](animationtransition.scss) for cross browser compatible CSS3 `animation`,  `@keyframes` and `transition` mixin's.

## retinaonly.scss
[retinaonly.scss](retinaonly.scss) allows for the creation of styles which will only be targeted to "retina" style, high pixel density display. Using the `@media` query created by [Thomas Fuchs](https://gist.github.com/madrobby/4161897/).

An example:

```scss
@import 'retinaonly.scss';
$logoWidth: 200px;

.companylogo {
	background: url(companylogo.png) no-repeat 0 0;
	height: 200px;
	width: $logoWidth;

	@include retinaOnly() {
		background-image: url(companylogo2x.png);
		background-size: $logoWidth;
	}
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

## vendor.scss
[vendor.scss](vendor.scss) holds mixin's for CSS styles that (still) require vendor prefixes - currently:
- `border-radius($value)`
- `opacity($value)`, supplying `filter: alpha...` and `zoom: 1` syntax for improved IE8 compatibility.
