# Refactoring UI

Notes on [Refactoring UI](https://refactoringui.com) by Adam Wathan and Steve Schoger.

# Starting from Scratch

**Start with a feature, not a layout**

An app is a collection of features. It's difficult to design a shell (layout, navigation, container)
without knowing the features first. Start with the functionality. For example, a flight booking
service will need: fields for cities, fields for dates, a button. Start with that form. You might
not even need the rest of the layout.

**Detail comes later**

In the early stages, don't spend too much time on details like typefaces, shadows, icons, etc...
For now, make low-fidelity mockups. Design in grayscale, which will force you to use
spacing/contrast/size to design well.

**Don't design too much**

Work in short cycles. Design a simple version of the next feature. When you're happy, make it real.
Get feedback by making it real (eg edge cases). Iterate until you're satisfied, then restart the
cycle with the next feature. If a feature is nice-to-have, design it later. You can always build on
top of your simple version.

**Choose a personality**

Are you trying to be secure and professional? Or be fun and playful? Personality is conveyed through
font choice (serif for elegant, rounded sans serif for playful, neutral sans serif), color (safe blue,
expensive gold, fun pink), or border-radius.

**Limit your choices**

Design with constraints. Put systems in place in advance. For example, pick a restrictive color
palette or type scale. Systematize everything: font size, weight, line height, color, margin, padding,
width, height, box shadows, border radius, border width, opacity.

# Hierarchy is Everything

**Not all elements are equal**

Visual hierarchy is used to show how important elements are in relation to each other. When everything
competes for attention, design feels noisy and chaotic. Deliberately de-emphasize secondary/tertiary
information, highlight the important items.

**(Font) size isn't everything**

Don't rely purely on font size. Use font weight and color too. Make important info bold and less
important info have a lighter font weight. Use softer colors for supporting text. Try to stick to
2/3 colors: dark for primary (headlines), grey for secondary (date), lighter grey for tertiary
(copyright). Two font weights should be enough: normal and heavier. Stay away from font weights
under 400 for UI work, they're too hard to read.

**Don't use grey text on colored backgrounds**

Grey on white works fine, which reduces contrast. To do the same on colored backgrounds, you want to
make the text closer to the background color. This can be achieved with reduced opacity --
unfortunately this washes out the text too. Choose a color with the same hue, adjust the saturation
and lightness.

**Emphasize by de-emphasizing**

You can make elements stand out more by de-emphasizing its surrounding elements. For example, try
making a selected nav item a bold primary color and the unselected nav items grey.

**Labels are a last resort**

Try not to use the `label: value` format when displaying information. For example, a contact card
doesn't need `name: Erin Lindford`. You can just put `Erin Lindford` and emphasize it. If a piece
of data isn't clear, clarify the text. Instead of `In stock: 12` try `12 left in stock` and emphasize
the 12. If you want to add a label, make sure they're secondary. Emphasize the data and de-emphasize
the label. The only time you want to emphasize a label is if you know the user is searching for
that specific label, perhaps in an information dense UI.

**Separate visual hierarchy from document hierarchy**

Don't let the elements you use influence how to style it. It might make sense for one header to be
an `h1` and another to be an `h3`, but that doesn't mean the `h1` needs to be a larger font-size.

**Balance weight and contrast**

Bold text feels emphasized because it's more densely packed -- it covers more surface area in the
same amount of pixels. Icons can be "heavy" also, especially placed next to some text. You can
counter this by de-emphasizing the icon, try giving it a softer color. This works for 1px borders
too.

**Semantics are secondary**

Semantics are important, but it shouldn't be prioritized over hierarchy. Every action sits on a
pyramid of importance. Instead of coloring buttons as red for delete, blue for edit, green for
publish -- just have one primary color for publish, secondary lighter color for edit, and make
the delete a link with no background-color or border. If a destructive action isn't the primary
action, it doesn't need to be big/red/bold.

# Layout and Spacing

**Start with too much white space**

White space should be removed, not added. Start out by giving a UI too much space, then remove
the whitespace until you're satisfied.

**Establish a spacing and sizing system**

Limit yourself to a set of spaces/sizes in advance. Use an exponential scale of sizes for better
contrast. For example in pixels: 4, 8, 12, 16, 24, 32, 48, 64, 96, 128, 192, 256, 384, 512. It
lets you design faster by quickly grabbing a number, instead of tweaking sizes by single pixels.
You'll also notice better consistency in your design.

**You don't have to fill the whole screen**

Just because you have space doesn't mean you need to fill it. If you only need 600px of width, just
use that. Different elements on the page can be different widths too. If you can't design for a
large canvas, shrink the canvas. Try designing for mobile first. If it feels weird as a wide UI,
consider splitting it up into multiple narrow columns. Opposite holds true: don't try to cram into a
small space either.

**Grids are overrated**

Some elements makes sense to stay fixed with, while other elements might expand in width. For example,
side navigations or avatars should stay fixed width. Don't be a slave to the grid. Embrace fluid and
fixed widths.

**Relative sizing doesn't scale**

As a general rule, large elements on a large screen need to shrink faster than elements that are
already fairly small -- when moving to a small screen. Sizes can scale independently of each other.
Not all elements need to be relatively sized compared to another element. The same holds true for
padding on buttons, larger sizes should have more padding relatively speaking.

**Avoid ambiguous spacing**

Make it obvious which elements are grouped together with spacing. Labels should be closer to their
inputs then the previous input on a form. Same with headers.

# Designing Text

**Establish a type scale**

Most UIs have too many font sizes. Systematize your font size selections. Pick exponential font
sizes, with many options at the smaller sizes scaling out to bigger gaps as the font sizes grow.
For example in pixels: 12, 14, 16, 18, 20, 24, 30, 36, 48, 60, 72, etc... Don't use ems, which are
relative to the current font size. Stick to px or rem.

**Use good fonts**

Play it safe with a neutral sans-serif, like Helvetica or system fonts. Ignore typefaces with
less than five weights. Optimize for legibility. Fonts meant for headlines have tighter letter
spacing and shorter x-height, fonts meant for small sizes have wider letter spacing and taller
x-height. Steal other people's font selections.

**Keep your line length in check**

Aim for 45-75 characters per line. A width of 20-35em gets you in the right ballpark.

**Baseline, not center**

Vertical align text by their baseline (imaginary line that letters rest on). The wider a line of
text is, the bigger the line-height needs to be. Narrow content can use line-height of 1.5 while
while content might need line-height of 2. Small fonts require more line-height also.

**Not every link needs a color**

Colored links make sense in a lot of text content. But in a UI, it's less necessary. You might
just want a heavier font weight or darker color.

**Align with readability in mind**

Vast majority of text should be left-aligned for English readers. Center alignment works for
headlines and short blocks of text. But for longer content, stick with left-alignment. If you want
to center-align, try to keep the text short. Right-align numbers in table cells. If you justify-align,
remember to turn on `hyphens: auto`.

**Use letter-spacing effectively**

Trust the typeface designer and leave letter-spacing alone (most of the time). Headlines may need
to have tighter letter-space. All caps may need wider letter-space.

# Working with Color

**Ditch hex for HSL**

HSL or Hue Saturation Lightness is more readable. Hue is a color's position on the color wheel,
where 0deg is red, 120deg is green, and 240deg is blue. Saturation is how vivid a color is. 0% is
grey (no color) and 100% is vibrant/intense. Lightness is how close it is to white or black. 0% is
pure black, 100% is pure white, 50% is pure color at given hue. There's also HSB, where B stands for
brightness -- 0% brightness is black and 100% brightness is white.

**You need more colors than you think**

You can't build everything with just five colors. A good color palette will have three categories:
greys (for text, backgrounds, panels, form controls), primary colors (for actions, navigation),
and accent colors (for eye-grabbing UI elements). Each category should have multiple shades.

**Define your shades up front**

Create a color palette with three categories: primary, neutral, accents. Then fill each category
with multiple shades. Neutrals are greys/whites/blacks. There might be multiple accents.

To fill in each category, choose a base color first. Let's call this shade 500. Then pick the
edge shades: 900/100; dark and light variants. Then fill in the gaps.

**Don't let lightness kill your saturation**

As a color gets closer to 0/100% lightness, impact of saturation gets weaker. If you don't want
lighter shades of a color to look washed out, increase the saturation as you increase/decrease
the lightness.

Also hues have inherent brightness to it. If you want to adjust a color's lightness, just adjusting
the lightness value will affect its intensity. You may want to rotate its hue. To make a color lighter,
try rotating the hue instead.

**Greys don't have to be grey**

True grey has a saturation of 0%. In practice, you can take a color and turn down its saturation.
If you want a UI to feel cooler: saturate a bit of blue. If you want it to feel warmer: use a bit
of yellow or orange. Increase the saturation as you move away from 50% lightness.

**Accessible doesn't have to mean ugly**

WCAG guidelines says text under 18px should have a contrast ratio of at least 4.5:1. Larger text
can have a contrast ratio of at least 3:1. Use dark colored text on a light colored background
for UI components that utilize colored backgrounds.

**Don't rely on color alone**

Add icons to colors to indicate different categories. This helps with accessibility.

# Creating Depth

**Emulate a light source**

Light comes from above. For buttons/raised elements: use inset shadows at the inside-top of an
element, usually lighter colors. Use normal shadows at the outside-bottom of elements, usually
darker colors. For inset elements: the inside-top should have a darker inset shadow. The
inside-bottom should have a lighter shadow.

**Use shadows to convey elevation**

Small shadows with a tight blur radius make elements feel slightly raised. Larger shadows with a
higher blur radius make them feel much closer to the user. Raise elements to convey that they're
interactive. They're useful for buttons, dropdowns, or modals. Make an elevation system of shadows,
just like your font sizes and colors.

Shadows are a great way to give feedback to users on UI elements too, like buttons. A normal button
should be raised closer to the user than a clicked button.

**Shadows can have two parts**

`box-shadow` can have multiple shadows:
`box-shadow 0 4px 6px rgba(0,0,0,.7), 0 5px 15px rgba(0,0,0,.1);`.

The first shadow is larger/softer, with a large vertical offset and blur radius. It's the shadow
cast behind an object by direct light source. The second shadow is tighter/darker, with less vertical
offset and smaller blur radius. It's the shadow underneath the object.

**Even flat designs can have depth**

Flat designs can convey depth with color. Lighter objects feel closer to us. Solid shadows with no
blur radius also work.

**Overlap elements to create layers**

Overlap elements create a feeling of layers, which gives depth to a design. Overlap forms on top of
different sections. Make a component taller than its background. Overlap avatars (and add an invisible
border around each round avatar image) for depth.

# Working with Images

**Use good photos**

Bad photos ruin a design. Hire a professional photographer or use high quality stock photos.

**Text needs consistent contrast**

When placing text over an image, it may ruin the contrast. Increase contrast by adding a semi
transparent overlay to the background image. Or lower the image contrast. Or colorize the image
by lowering image contrast, desaturating the image, and adding a solid fill using "multiply" blend
mode. Or adding text shadow.

**Everything has an intended size**

Don't naively scale up icons and use them, even if they're vector. The icons were designed for a
specific size. If you only have small icons but need to use them in a large element, try putting
them in a shape with background color.

Don't scale down screenshots. Text becomes unreadable. Use a partial screenshot or use a screenshot
of a smaller layout. Or an illustration.

**Beware user-uploaded content**

Control the size and aspect ratio of your user-uploaded images. Use the `background-size` and
`cover` CSS properties. Prevent background bleeds (when image background bleeds into your page
background) with a subtle inner box shadow.

# Finishing Touches

**Supercharge the defaults**

Try replacing list bullets with icons. Checkmark icons are a good generic bullet. Quote icons
are great for blockquotes. Try adding a custom underline that partially overlaps with link text.
Or use custom checkboxes and radio buttons.

**Add color with accent borders**

Single borders with accents are a great way to add visual flair. Either at the top, bottom, or left
side.

**Decorate your backgrounds**

Add flair by adding a background color. This can be used to emphasize panels or adding contrast to
different sections of a page. Or use a repeating pattern.

**Don't overlook empty states**

If your app depends on user-generated content, don't forget about what it looks like when the user
first starts using it (and there's no content yet!). Consider having a special case, adding an
illustration and a large call to action to `+ Add Contact` or something similar. Feel free to remove
unnecessary UI elements too, for example, table headers aren't necessary if there's nothing in the
table yet.

**Use fewer borders**

Table borders aren't necessary around the entire table. Try a box shadow. Or a different background
color for different UI elements (like a search bar). Or instead of borders between each table row,
add more vertical spacing.

**Think outside the box**

For example, a single list of a dropdown menu could be broken out into multiple columns with icons
for each element and a description. Table rows don't need to only have a single row of text, cells
could have subtext. Radio buttons don't need to be text only, they can be grouped together elements
like cards.

# Leveling Up

Some tips for leveling up:

* Look for decisions you wouldn't normally make when using other designers' UIs
* Rebuild your favorite interfaces
* Continually study other people's works that inspire you with a careful eye
