Beginning GIMP
==============

An introduction to the GNU Image Manipulation Program or GIMP by Guillermo
S. Romero.

Getting to Know GIMP
====================

The Toolbox window is GIMP's main window and includes most of the functions
you'll be using.  Its menu bar isn't tied to any particular image.  When you
activate a tool, its options will be displayed below.  If you accidentally
remove it, get it back with `File -> Dialogs -> Tools`.

When you edit an image, you'll get an Image window.  Near the bottom left,
there's a QuickMask tool and the bottom right has the Navigation control.

GIMP usually starts with one dialog - the Layers dialog.  This lets you edit,
re-arrange, create, or delete layers on your workspace.  You can view more
dialogs from `File -> Dialogs`.  Every dialog is movable and dockable.  Menus
also have a "tear-off", which lets you grab any menu and turn it into a window
of its own.

You can toggle the Menu Bar via `View -> Hide Menu Bar`.  Just right-click for
a context menu to get the same menu.

Under Tool Options, be sure to change the Scaling to Cubic or Sinc.  These use
more CPU power, but have higher quality.

You can customize your keyboard shortcuts with `Edit -> Keyboard Shortcuts...`.

Improving Digital Photos
========================

This chapter covers some basics like opening files, scaling, cropping, and
saving files.  I'll note them very quickly:

1. To open a file, use `File -> Open`
2. Use `Image -> Scale Image` to scale
3. Use the Cropping tool in the toolbox for cropping
4. Use `File -> Save` to save.  GIMP's file type is `XCF`

You've got a lot of options for formats.  For JPEGs, there are a few quality
settings with trade-offs.  Make sure you have `Show Preview` checked.  Remember
JPEG has lossy compression so it loses quality every time you save.  Some of
the advanced options are:

* Optimize reduces file size without reducing quality, no reason not to use it
* Progressive lets users see a poor quality version when downloading the image
  before the high quality replaces it
* Smoothing, use restart markers, subsampling, and DCT method have good defaults
  that you shouldn't need to adjust
* Save EXIF data will save metadata about the image like camera information
* XMP is Adobe's metadata just like EXIF
* Comment is where you can include plaintext like your name

The Crop tool is relatively straightforward to use.  Just select it from the
toolbox and drag the area to keep.  There are also some additional options like
cropping the current layer only, fixed aspect ratios, or using numerical fields
for cropping.

To control brightness or darkness, use `Tools -> Color Tools`.  You'll have
access to brightness/contrast dialogs and the levels dialog.  The levels dialog
lets you choose different channels - initially it's brightness but RGB channels
are also available.

The Curves dialog is another way to adjust brightness, contrast, and colors.
The horizontal bottom bar represents input and vertical left bar represents
output.  Click on any spot on the line and drag to adjust.

Some other adjustments you can make are Threshold (turns image into two tones
with a given threshold), Posterize (turns into an artistic poster),
Desaturate (removes color), Invert, and Auto (automated tools for photos).  The
Auto dialog can be broken down further:

* Equalize spreads the image colors out
* White Balance corrects color casts
* Color Enhance makes the colors more intense
* Normalize adjusts the exposure on underexposed images
* Stretch Contrast and Stretch HSV are like Normalize, but operate on the three
  color channels independently

The `Image -> Transform` menu lets you rotate or flip your image.

Sharpening can be done via the Sharpen or Unsharp Mask actions, both under
`Filters -> Enhance`.    Unsharp Mask is very simple to use:

* Radius effects distance that sharpening works
* Amount is how strong it is
* Threshold controls smoothness

Use `Filters -> Enhance -> Red Eye Removal` to remove red eyes.

Introduction to Layers
======================

Drawing
=======

Selection
=========

Erasing and Touching Up
=======================

Filters and Effects
===================

Color
=====

Advanced Drawing
================

Advanced Composition
====================

Plug-ins and Scripting
======================

Additional Topics
=================
