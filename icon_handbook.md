Icon Handbook
=============

Jon Hicks is an amazing designer.  His most famous work is probably the
icon for Firefox.  In this book, he walks us through the theory behind icons
and how to create icons.

A Potted History of Icons
=========================

Humans used to communicate via symbols and pictures: pictographs paintings and
petroglyphs carvings.  Eventually symbols came to represent abstract ideas -
the difference between pictograms and ideograms.

Isotype (International System of Typographic Picture Education) was an icon
system developed by Otto Neurath to be universal.  The pictograms developed for
Isotype are still used today.

The first computer icons appeared in 1974 on the Xerox Alto.  The Star, a
successor machine included the first GUI and used icons for files, directories,
disks, and more.

How We Use Icons
================

This chapter covers our uses from a universal language to conveying emotions.
It also covers when not to use icons.

On the web, icons add visual interest to text.  For software, icons can
represent functions.  You can remove text instructions all together and just
use icons.

Hicks uses a website navigation as an example of how to use icons poorly.
In the example, the icon and text are evenly spread apart:

    [i]   blog   [i]   portfolio   [i]   downloads   [i]   contact

But each icon is supposed to represent a navigation item.  It's better to use
proximity to tie the icons together with its text:

    [i] blog     [i] portfolio     [i] downloads     [i] contact

Icons can represent what users should expect when taking an action.  A play
button means expect a video.  A magnifying glass means expect zooming in.  A
PDF icon means expect downloading a PDF.

Chat applications use status icons so users can see others' status at a glance.
Status icons work well for file editors too (unsaved, pending, uploading).

Icons can be used to convey feedback.  A red X could mean a user error.  A green
checkmark means the last action was a success.

Some common mistakes for using icons:

* is it being used for just decoration?
* is it repeated so often that it loses its purpose?
* are there too many icons in a small space?
* is the concept too abstract for an icon?
* is the interface too overwhelming with lots of icons?

Favicons
========

Favicons are an ideal place to start because you need to achieve pixel crisp
artwork and clarity at small sizes.  They're 16px by 16px and are used to
represent websites.  When creating an icon, you need to answer:

1. What is the context?  Think backgrounds, themes, and platforms.
2. What sizes are needed?
3. What formats are needed?

ICO files are the most widely supported.  They can contain multiple bit depths
and resolutions.  PNG is more convenient.

When creating your icon, it's helpful to use a pixel grid.  For small icons,
make sure straight edges are kept within the grid.  Anything "halfway" will
appear blurred.

Some tips when designing a favicon:

* you might have to redraw your icon from scratch to fit a 16px square
* remember that your favicon will be on different backgrounds, it might be
  better to use a transparent color which changes depending on its background
* it's best not to use any perspective which requires more pixels
* if your logo won't fit into a square, just choose a portion of it
* IconBuilder is a handy application to create `.ico` files

Metaphor
========

This chapter follows a client scenario.  If a convention already exists, use it.
If not, create a new convention.

The first thing you need to ask when creating an icon is how to best represent
that tool, function, or direction?

To figure this out, you can break it down:

1. What is the icon for?
2. What is its context?
3. Who's it for?
4. Is there a single message?  Sometimes the goal is too dispersed and needs
   to be focused.
5. Does it need to even be an icon?

Some tips for figuring out if a metaphor already exists:

1. Use Google's image search
2. Use <http://thenounproject.com>
3. Use <http://iconfinder.com>

If you need to start from scratch, try doing a mind map.  Start with the message
you're trying to convey and then create new nodes which are related.  For
the "view" function, some nodes could be: eyes, detail/close-up, sight, literal
view.  Each node can have something that could represent it: actual eyes,
magnifying glass, telescope, binoculars, glasses, landscape.  Now ask the
following questions:

1. Would the icon be recognizable?
2. How much detail is required to make it recognizable?
3. Are there any negative connotations?

You can use combinations of pictograms to make your icon clearer.  A plus sign
with a document means "New Document".

The final part is to use color.  Here are some emotions evoked by different
colors:

* red => importance, warm, life, love, revolution, celebration, good luck.
         Also stop, problems, danger, warning, error.
* orange => warmth, energy.  Also cheap and RSS.
* yellow => joy, happiness, light, wisdom.  Also warning, forbidden, cowardice,
            decay, secure, highlight.
* green => life, nature, vitality, go, growth.  Also envy/madness.  Technical
           uses include sharing and correctness.
* purple => royalty, cruelty, arrogance
* blue => daytime, calm, information, corporate.  Also cold/corporate.
          Technical uses include selected, on, enabled.
* gray => luxury, sadness, disabled, off
* brown => comfort, nature, poverty
* black => sophistication, luxury, expensive, prosperity, death, mourning, evil,
           mystery, misery

Drawing Icons
=============

Icon Formats and Deployment
===========================

Application Icons
=================
