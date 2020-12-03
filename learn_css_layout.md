# Learn CSS Layout

Notes from [Learn CSS Layout](https://learnlayout.com).

# The "Display" Property

`display` is CSS's most important property for controlling layout. The default value for most
elements is `block` or `inline`.

`div` is the standard block-level element. Other common block level elements are: `p`, `form`,
`header`, `footer`, `section`, `main`, and more. Block elements take up the full width of the page,
by default.

`span` is the standard inline element. `a` is another common inline element. Inline elements don't
start a new line, two or more inline elements can exist on the same line.

Other values for display include `none`, which will hide the element from the page.

# Margin Auto

Using `auto` is a good way to create a column that's horizontally centered on the page:

```css
#main {
  width: 600px;
  margin: 0 auto;
}
```

# Max-Width

The above will make the column exactly `600px` wide. Using `max-width` instead of `width` will let
users resize their browser width with your content reflowing is layout, without a horizontal
scrollbar.

```css
#main {
  max-width: 600px;
  margin: 0 auto;
}
```

# The Box Model

All HTML elements can be considered boxes. The box model is used to describe the layout of each
element. From inside to outside, it includes:

* content - whose size can be set with the `width` and `height` CSS properties
* padding - padding between content/border
* border - the barrier between padding/margin
* margin - the space between different elements

Note that the width/height only describes the content, not the total width/height that the element
takes up on the page.

```css
.smaller {
  width: 500px;
  margin: 20px auto;
}
.bigger {
  width: 500px;
  margin: 20px auto;
  padding: 50px;
  border-width: 10px;
}
```

In this case, the `.smaller` element is narrower than `.bigger`, even though they both have the
same specified width.

# Box Sizing

Setting `box-sizing: border-box;` uses a different method (and probably more intuitive) for setting
the width/height. Using border box, the set values for width/height include the content/padding/border.

```css
.smaller {
  box-sizing: border-box;
  width: 500px;
  margin: 20px auto;
}
.bigger {
  box-sizing: border-box;
  width: 500px;
  margin: 20px auto;
  padding: 50px;
  border-width: 10px;
}
```

In this case, the two elements will have the same total width. Both are 500px.

Note possible values for `box-sizing` are: `content-box` (default), `border-box`, `initial`, or
`inherit`.

# Position

The position property lets you move where an element is. Possible values are:

* `static` - default value, it means don't manipulate the position and keep it where the browser wants it
* `relative` - use `top`/`right`/`left`/`bottom` to move the positive relative to where the browser
  wants it from its static location
* `fixed` - fixes the element to a specific position in the viewport, so even if the user scrolls
  the element stays sticky in place.
* `absolute` - positions element absolutely anchored to an ancestor element (closest ancestor that
  uses the relative position)

# Float

`float` is for wrapping text around images.

```css
img {
  float: right;
  margin: 0 0 1em 1em;
}
```

```html
<p>
  <img src="..." alt="..." />
  Lorem ipsum dolor sit amet...
</p>
```

# Clear

Clear is used to clear any previous floated elements. For example:

```html
<div class="box">...</div>
<section>...</section>
```

```css
.box {
  float: left;
  width: 200px;
  height: 100px;
  margin: 1em;
}
```

In the case above, the box will exist inside the section. Even though the section element exists
after the box. Use `clear` if you want section to restart on its own block/horizontal line. 

```css
section {
  clear: left; /* can also be both */
}
```

# Clearfix

Sometimes the floated element is taller than the element containing it, which causes it to overflow
its container. Use the clearfix hack to fix it:

```css
img {
  float: right;
}
.clearfix {
  overflow: auto;
  zoom: 1; /* only necessary for IE6 */
}
```

```html
<div class="clearfix">
  <img src="..." alt="..." />
  Lorem ipsum dolor sit amet...
</div>
```

# Media Queries

Use media queries to make responsive designs. Here's an example layout that changes from two columns
to one column when the browser is too narrow:

```css
@media (min-width:600px) {
  nav {
    float: left;
    width: 25%;
  }
  section {
    margin-left: 25%;
  }
}
@media (max-width:599px) {
  nav li {
    display: inline;
  }
}
```

```html
<nav>
  <ul>
    <li><a href="#">...</a></li>
  </ul>
</nav>
<section>
  Lorem Ipsum...
</section>
```

# Inline Block

Use `inline-block` to specify elements that should be inline (don't cause a page break) but still
have width/height. To create a grid of elements:

```css
.box {
  display: inline-block;
  width: 200px;
  height: 100px;
  margin: 1em;
}
```

# Inline Block Layout

`inline-block` elements are affected by the `veritcal-align` property. If you're using it for layout,
you'll want to set `vertical-align: top`. An example layout:

```css
nav {
  display: inline-block;
  vertical-align: top;
  width: 25%;
}
.column {
  display: inline-block;
  vertical-align: top;
  width: 75%;
}
```

```html
<nav>
  <ul>
    <li><a href="#">...</a></li>
  </ul>
</nav>
<div class="column">
  Lorem ipsum...
</div>
```

# Column

A new set of properties can be used to make multi-column text:

```css
.three-column {
  padding: 1em;
  -moz-column-count: 3;
  -moz-column-gap: 1em;
  -webkit-column-count: 3;
  -webkit-column-gap: 1em;
  column-count: 3;
  column-gap: 1em;
}
```
