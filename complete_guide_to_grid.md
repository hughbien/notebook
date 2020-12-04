# A Complete Guide to Grid

Notes on [A Complete Guide to Grid](https://css-tricks.com/snippets/css/complete-guide-grid/).

A good resource to practice is [Grid Garden](https://cssgridgarden.com).

# Introduction

CSS Grid Layout is a 2d layout system. Flexbox is intended for simpler one-dimensional layouts,
not complex 2d ones. Flexbox and Grid work well together.

# Basics and Browser Support

Most browsers have shipped unprefixed support for CSS Grid. IE10/11 support it but with an outdated
implementation/syntax.

Container element needs to have `display: grid` set. Also the `grid-template-columns` and
`grid-template-rows` sizes should be set. Child elements are placed onto the grid with `grid-column`
and `grid-row`.

# Important Terminology

**Grid Container** - the element containing all items, which are direct children elements.

**Grid Item** - the direct descendant that will exist on the grid.

**Grid Line** - dividing lines on the grid, can be either vertical (column grid lines) or
horizontal (row grid lines).

**Grid Cell** - space between two adjacent row lines and two adjacent column lines.

**Grid Track** - space between two adjacent grid lines, a whole row or column.

**Grid Area** - total space surrounded by four grid lines, composed of any number of grid cells.

# Most Powerful Lines in Grid

To set fluid width columns that break into more/less columns as space is available:

```css
.grid {
  display: grid;
  grid-template-columns: repeat(auto-fill, minmax(200px, 1fr));
  grid-gap: 1rem;
}
```

# Grid Properties for Parent

## display

Defines element as grid container.

```css
.container {
  display: grid | inline-grid;
}
```

## grid-template-columns, grid-template-rows

Defines track size and space between columns/rows.

* track-size - length, percentage, or fraction (fr unit). Fraction represents the fraction of the
  free space in the grid.
* line-name - arbitrary name of your choosing.

```css
.container {
  grid-template-columns: 40px 50px auto 50px 40px;
  grid-template-rows: 25% 100px auto;
}
```

In the example above: there are five columns. The left/right-most columns the 40px wide. The
next columns in are 50px. The center column takes up whatever space is left. There are three rows.
First row takes up a quarter of the space. Second row is 100px tall. Last row takes up remaining
space.

You can also name the column/row lines:

```css
.container {
  grid-template-columns: [first] 40px [line2] 50px [line3] auto [col4-start] 50px [five] 40px [end];
  grid-template-rows: [row1-start] 25% [row1-end] 100px [third-line] auto [last-line];
}
```

A line can have more than one name (separated by space). You can use the `repeat()` notation too:

```css
.container {
  grid-template-columns: repeat(3, 20px [col-start]);
} /* is equivalent to */
.container {
  grid-template-columns: 20px [col-start] 20px [col-start] 20px [col-start];
}
```

The `fr` unit lets you use up the free space of the grid. For example:

```css
.container {
  grid-template-columns: 1fr 1fr 1fr; /*each column takes up one third of grid container */
}
```

Free space is calculated after any non-flexible items.

## grid-template-areas

Defines areas on the grid, which can be later referenced with the children's `grid-area` property.
The syntax providers a visualization of the grid.

Values can be a `<grid-area-name>`, `.` which signatures an empty grid cell, or `none`.

```css
.container {
  display: grid;
  grid-template-columns: 50px 50px 50px 50px;
  grid-template-rows: auto;
  grid-template-areas:
    "header header header header"
    "main main . sidebar"
    "footer footer footer footer";
}
.item-a {
  grid-area: header;
}
.item-b {
  grid-area: main;
}
.item-c {
  grid-area: sidebar;
}
.item-d {
  grid-area: footer;
}
```

Using this syntax, lines are automatically named with `-start` and `-end` suffixes, like
`header-start` or `footer-end`.

## grid-template

Shorthand for setting grid-template rows, columns, and areas.

```css
.container {
  grid-template:
    [row1-start] "header header header" 25px [row1-end]
    [row2-start] "footer footer footer" 25px [row2-end]
    / auto 50px auto;
} /* is equivalent to */
.container {
  grid-template-rows: [row1-start] 25px [row1-end row2-start] 25px [row2-end];
  grid-template-columns: auto 50px auto;
  grid-template-areas: 
    "header header header" 
    "footer footer footer";
}
```

## column-gap, row-gap, grid-column-gap, grid-row-gap

Specify the sizes of the grid lines. These are the width of the gutters.

```css
.container {
  /* standard way */
  column-gap: 10px;
  row-gap: 15px;

  /* old way */
	grid-column-gap: 10px;
	grid-row-gap: 15px;
}
```

## gap, grid-gap

Shorthand for `row-gap` and `column-gap`.

```css
.container {
  /* standard */
  gap: <grid-row-gap> <grid-column-gap>;

  /* old */
  grid-gap: <grid-row-gap> <grid-column-gap>;
}
```

## justify-items

Aligns grid items along the row axis. Use `align-items` to align along the column axis.

```css
.container {
  justify-items: start | end | center | stretch;
}
```

## align-items

Aligns grid items along the column axis. Use `justify-items` to align along the row axis.

```css
.container {
  align-items: start | end | center | stretch;
}
```

## place-items

Shorthand for both `align-items` and `justify-items`. Example:

```css
.container {
  place-items: start center; /* start of row, center or column */
}
```

## justify-content, align-content, place-content

Similar to the `*-items` counterpart, these work with the entire grid inside of the grid container
(moving the entire grid as one as opposed to each individual item).

Possible values include: `start`, `end`, `center`, `stretch`, `space-around`, `space-between`,
`space-evenly`.

## grid-auto-columns, grid-auto-rows

Specifies the size of any auto-generated grid tracks. These "implicit" tracks are created when
there are more grid items than cells.

```css
.container {
  grid-auto-columns: <track-size> ...;
  grid-auto-rows: <track-size> ...;
}
```

## grid-auto-flow

The algorithm to use for implicit items. Possible values:

* `row` - fill each row in turn, adding new rows as necessary (default)
* `column` - fill each column in turn, adding new columns as necessary
* `row|column dense` attempt to fill in holes earlier in the grid if smaller items come up later

## grid

Shorthand for setting properties in a single declaration:

* `grid-template-rows`
* `grid-template-columns`
* `grid-template-areas`
* `grid-auto-rows`
* `grid-auto-columns`
* `grid-auto-flow`

# Grid Properties for Children

`float`, `display: inline-block`, `display: table-cell`, `vertical-align`, and `column-*` properties
have no effect on a grid item.

## grid-column-start, grid-column-end, grid-row-start, grid-row-end

Determines a grid item's location within the grid. Acepted values are:

* `<line>` a number or name
* `span <number>` span across provided number of grid tracks
* `span <name>` span across until it hits next line with provided name
* `auto` auto placement or default span of 1

Examples:

```css
.item-a {
  grid-column-start: 2;
  grid-column-end: five; /* columns define how wide it is */
  grid-row-start: row1-start;
  grid-row-end: 3; /* rows defines tall it is */
}

.item-b {
  grid-column-start: 1;
  grid-column-end: span col4-start; /* keep going until this line */
  grid-row-start: 2;
  grid-row-end: span 2; /* keep going for 2 rows */
}
```

## grid-column, grid-row

Shorthand for `grid-column-start` + `grid-column-end`, and `grid-row-start` + `grid-row-end`.

```css
.item {
  grid-column: <start-line> / <end-line> | <start-line> / span <value>;
  grid-row: <start-line> / <end-line> | <start-line> / span <value>;
}
```

## grid-area

Gives an item a name so that it can be referenced by a template created with the `grid-template-areas`
property. Can be used as a shorthand for `grid-row/column-start/end` properties.

```css
.item {
  grid-area: <name> | <row-start> / <column-start> / <row-end> / <column-end> ;
}
```

Examples:

```css
.item-d {
  grid-area: header;
}
.item-d {
  grid-area: 1 / col4-start / last-line / 6;
}
```

## justify-self, align-self, place-self

Just like `justify-items`, `align-items`, and `place-items` properties for the grid container --
except this only applies to the grid item. `place-self` is a shorthand for justify and align.
