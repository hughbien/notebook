Game Programming All in One
===========================

By Jonathan S. Harbour, this book covers 2D game programming using the cross
platform Allegro framework with the C language.  The sample code uses Allegro 4,
which is outdated.  Allegro 5 has a new, backwards-incompatible API.  Since the
example code uses an old version of Allegro, I'll skim through it to learn
general ideas instead of taking precise notes.

Demystifying Game Development
=============================

This chapter is an overview of game development - it doesn't include anything
technical.

Harbour says the game industry employs millions of workers, most are specialized
in their own fields.  All of this is for entertainment.

There's little difference between programming for a particular console or a PC,
it's all based on C or C++.  The console SDKs include libraries that you link
into your program.  Many companies now produce the same games for multiple
platforms.

2D games are not dead, what sets them apart are fantastic gameplay.  It's best
to find a niche and use it to develop a great game.

This book focuses on the Allegro game library, originally developed by Shawn
Hargreaves for the Atari ST.  Allegro abstracts the operating system so your
code can compile on any supported platforms.

Getting Started with the Allegro Game Library
=============================================

Download Allegro from <http://alleg.sourceforge.net/>.  Install instructions
for your OS should be on there also.  Learn how to compile Allegro programs
here: <http://wiki.allegro.cc/index.php?title=Compiling_Allegro_Programs>.

Harbour gives us a step-by-step guide on setting up several C++ IDEs with
Allegro.  He then gives us an example program to compile to make sure everything
works.

The first function used is:

    int allegro_init();

This initializes the library and sets the `allegro_id` information which
contains information like what version of Allegro you're using:

    extern char allegro_id[];

At the end of the main function, use `allegro_exit()` to exit allegro.  After
the main function, the macro `END_OF_MAIN()` is required for cleanup.  You
can print out system information with:

    extern char allegro_id[];
    printf(Allegro version = %s\n", allegro_id);

2D Vector Graphics Programming
==============================

A **graphics primitive** is a function that draws a geometric shape.  Video
cards were designed to render geometric primitives with special effects.  It
renders triangles made up of vertices.  

**Blit** means bit-block transfer, a method of transferring a chunk of memory -
usually from system memory to the video card.  The **pixel** is the smallest
element on a screen.

`allegro_init` creates a global screen pointer called `screen`.  It uses
double buffering (render one screen on the monitor, another off-screen, and
switch between them).

The following program initializes full-screen video mode with Allegro 4:

    #include <stdlib.h>
    #include "allegro.h"

    int main(void) {
      allegro_init();
      install_keyboard();
      int ret = set_gfx_mode(GFX_AUTODETECT_WINDOWED, 640, 480, 0, 0);
      if (ret != 0) { 
        allegro_message(allegro_error); 
        return 1;
      }
      //display screen resolution
      textprintf(screen, font, 0, 0, makecol(255, 255, 255), 
                 "%dx%d", SCREEN_W, SCREEN_H);
      while (!key[KEY_ESC]);
      allegro_exit();
      return 0;
    }
    END_OF_MAIN()

`set_gfx_mode` detects the current computer's graphics settings and sets the
program's window.  `allegro_message` can be used for an alert box.
`allegro_error` is a global variable containing the most recent error message.
`textprintf` displays text in any video mode.

The next program draws text to the screen:

    include "allegro.h"
    int main(void) {
      char *filename = "allegro.pcx";
      BITMAP *image;
      int ret;
      allegro_init();
      install_keyboard();
      set_color_depth(16);

      ret = set_gfx_mode(GFX_AUTODETECT_WINDOWED, 640, 480, 0, 0); 
      if (ret != 0) {
        allegro_message(allegro_error);
        return 1; 
      }

      image = load_bitmap(filename, NULL); 
      if (!image) {
        allegro_message("Error loading %s", filename);
        return 1;
      }

      blit(image, screen, 0, 0, 0, 0, SCREEN_W, SCREEN_H);
      destroy_bitmap(image);
      textprintf_ex(screen,font,0,0,1,-1,"%dx%d",SCREEN_W,SCREEN_H);
      while (!keypressed());
      allegro_exit();
      return 0;
     }
     END_OF_MAIN() 

The simplest pixel drawing function is:

    void putpixel(BITMAP *bmp, int x, int y, int color)

To draw random pixels onto the screen:

    while(!key[KEY_ESC]) {
      //set a random location
      x = 10 + rand() % (SCREEN_W-20);
      y = 10 + rand() % (SCREEN_H-20);

      //set a random color
      red = rand() % 255;
      green = rand() % 255;
      blue = rand() % 255;
      color = makecol(red,green,blue);

      //draw the pixel
      putpixel(screen, x, y, color); 
    }

To draw lines use `hline` or `vline`:

    void hline(BITMAP *bmp, int x1, int y, int x2, int color)
    void vline(BITMAP *bmp, int x, int y1, int y2, int color)

Draw rectangles with:

    void rect(BITMAP *bmp, int x1, int y1, int x2, int y2, int color)
    void rectfill(BITMAP *bmp, int x1, int y1, int x2, int y2, int color)

Allegro also provides a callback feature, just use `do_line`:

    void do_line(BITMAP *bmp, int x1, y1, x2, y2, int d, void (*proc))

Your callback should have the format:

    void doline_callback(BITMAP *bmp, int x, int y, int d)

Draw circles and ellipses with:

    void circle(BITMAP *bmp, int x, int y, int radius, int color)
    void circlefill(BITMAP *bmp, int x, int y, int radius, int color)
    void ellipse(BITMAP *bmp, int x, int y, int rx, int ry, int color)
    void ellipsefill(BITMAP *bmp, int x, int y, int rx, int ry, int color)

These each have their own callback facility, just prefix each function with
`do_`.

The `spline` function draws curves based on four (x,y) input points:

    void spline(BITMAP *bmp, const int points[8], int color)

Triangles can be drawn with three points:

    void triangle(BITMAP *bmp, int x1, y1, x2, y2, x3, y3, int color)

And polygons can be drawn with:

    void polygon(BITMAP *bmp, int vertices, const int *points, int color)

You can use `floodfill` to fill in random regions within your program:

    void floodfill(BITMAP *bmp, int x, int y, int color)

There are three functions for primary text output:

    void textout_ex(BITMAP *bmp, const FONT *f, const char *s,
                    int x, int y, int color, int bg)
    void textout_centre_ex(BITMAP *bmp, const FONT *f, const char *s,
                           int x, int y, int color, int bg)
    void textout_right_ex(BITMAP *bmp, const FONT *f, const char *s,
                          int x, int y, int color, int bg)

Each of these have a `printf`-like equivalent.  These take a format string
and variable arguments:

    void textprintf_ex(BITMAP *bmp, const FONT *f, int x, int y, 
                       int color, int bg, const char *fmt, ...);
    void textprintf_centre_ex(BITMAP *bmp, const FONT *f, int x, int y, 
                              int color, int bg, const char *fmt, ...);
    void textprintf_right_ex(BITMAP *bmp, const FONT *f, int x, int y, 
                             int color, int bg, const char *fmt, ...);

Writing Your First Allegro Game
===============================

Getting Input from the Player
=============================

Mastering the Audible Realm
===========================

Basic Bitmap Handling and Blitting
==================================

Introduction to Sprite Programming
==================================

Sprite Animation
================

Advanced Sprite Programming
===========================

Programming the Perfect Game Loop
=================================

Programming Tile-Based Scrolling Backgrounds
============================================

Creating a Game World: Editing Tiles and Levels
===============================================

Loading Native Mappy Files
==========================

Vertical Scrolling Arcade Games
===============================

Horizontal Scrolling Platform Games
===================================

The Importance of Game Design
=============================

Using Datafiles to Store Game Resources
=======================================

Playing Movies and Cut Scenes
=============================

Introduction to Artificial Intelligence
=======================================

Multi-Threading
===============

Publishing Your Game
====================
