# A Complete Guide to Flexbox

Notes on [A Complete Guide to Flexbox](https://css-tricks.com/snippets/css/a-guide-to-flexbox/).

A great resource to practice is [Flexbox Froggy](https://flexboxfroggy.com).

# Properties for the Parent (Flex Container)

## display

Enables flex context for all direct children. Can be block (flex) or inline (inline-flex).

```css
.container {
  display: flex; /* or inline-flex */
}
```

## flex-direction

Establishes **main axis** which will be horizontal or vertical. Also establishes the direction.
The **cross axis** is perpendicular to it.

```css
.container {
  flex-direction: row | row-reverse | column | column-reverse;
}
```

`row` is the default and flows left to right. `column` flows top to bottom. `*-reverse` keeps the
axis the same, but reverses flow.

## flex-wrap

Allow items to wrap into multiple lines.

```css
.container {
  flex-wrap: nowrap | wrap | wrap-reverse;
}
```

* `nowrap` is default, all items will be on one line.
* `wrap` wraps onto multiple lines
* `wrap-reverse` wrap onto multiple lines, from bottom to top

## flex-flow

Shorthand for `flex-direction` and `flex-wrap`.

```css
.container {
  flex-flow: column wrap; /* default is row nowrap */
}
```

## justify-content

Defines alignment along main axis, helps define how to distribute extra free space around items.

The safest values for most browsers are `flex-start`, `flex-end`, and `center`.

```css
.container {
  justify-content: flex-start | flex-end | center | space-between | space-around |
    space-evenly | start | end | left | right ... + safe | unsafe;
}
```

* `flex-start` items are packed towards the start of `flex-direction`
* `flex-end` items are packed towards the end of `flex-direction`
* `start` items are packed towards start of `writing-mode` direction
* `end` items are packed towards end of `writing-mode` direction
* `left` items are packed towards left edge, unless it conflicts with `flex-direction` in which case
  it will behave like start
* `right` items are packed towards right edge, unless it conflicts with `flex-direction` in which case
  it will behave like start
* `center` items are centered along the line
* `space-between` items are evenly distributed on line with space between, first/last item have no
  space between them and the edge
* `space-around` items are evenly distributed with equal space between them, first/last items will
  have space between them and edge -- but space between items will be greater than margins at edge
* `space-evenly` items are evenly distributed with equal space between all items and edges

A second keyword can be added: `safe` or `unsafe`, safe ensures you can't push an element that
renders off-screen so that user can't scroll to it.

## align-items

Defines how items are laid out along the cross axis.

```css
.container {
  align-items: stretch | flex-start | flex-end | center | baseline | first baseline |
    last baseline | start | end | self-start | self-end + ... safe | unsafe;
}
```

* `stretch` (default), stretch to fill container while still respecting min/max width
* `flex-start` / `start` / `self-start` items placed at start of cross axis, using the direction
  of either `flex-direction` or `writing-mode`
* `flex-end` / `end` / `self-end` items placed at end of cross axis, using direction of either
  `flex-direction` or `writing-mode`
* `center` items are centered
* `baseline` items are aligned to their baselines (bottom of text)

## align-content

Similar to justify content on the main axis, the `align-content` property defines alignment on
the cross axis. This only works on multi-line flex containers.

```css
.container {
  align-content: flex-start | flex-end | center | space-between | space-around | space-evenly |
    stretch | start | end | baseline | first baseline | last baseline + ... safe | unsafe;
}
```

* `normal` (default) items are packed in their default position
* `flex-start` / `start` / `self-start` items are placed at start of container
* `flex-end` / `end` / `self-send` items are placed at end of container
* `center` items are centered in container
* `space-between` items are evenly distributed, first/last item has no space between them and edges
* `space-around` items are evenly distributed, first/last item have space between them and edge --
  but space between items will be greater than margins at edge
* `space-evenly` items are evenly distributed, with space between all items/edge being the same
* `stretch` lines stretch to take up remaining space

# Properties for the Children (Flex Items)

## order

Items are laid out in source order by default. You can assign a number to each item though to
manipulate the order (laid out in ascending order).

```css
.item {
  order: 5; /* default is 0 */
}
```

## flex-grow

Lets an item grow, if necessary and possible. Uses unitless values as a proportion. So if all items
are set to `1`, all children will be equal in size. If one child has `2`, it will take up twice
as much space than the others.

```css
.item {
  flex-grow: 4; /* default 0 */
}
```

## flex-shrink

Lets an item shrink.

```css
.item {
  flex-shrink: 3; /* default 1 */
}
```

## flex-basis

Default size of flexible item before remaining space is distributed. Can be length or keyword.

```css
.item {
  flex-basis: 10px | 25em | 37rem | 42% | auto;
}
```

Default is `auto`, which uses the item's default width or height.

## flex

Shorthand for `flex-grow`, `flex-shrink`, and `flex-basis`. The second/third parameters are
optional. The default is `0 1 auto`.

```css
.item {
  flex: none | [ <flex-grow> <flex-shrink>? || <flex-basis> ];
}
```

## align-self

Allows the default alignment to be overridden for individual items. See `align-items` on the
container properties for more info.

```css
.item {
  align-self: auto | flex-start | flex-end | center | baseline | stretch;
}
```
