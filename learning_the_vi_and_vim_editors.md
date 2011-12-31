Learning the vi and Vim Editors
===============================

A detailed guide to using Vi and Vim by Arnold Robbins.  These notes are a
quick summary of each chapter.

The vi Text Editor
==================

vi is short for visual editor.  It has an insert mode and a command mode.
Commands are case sensitive, not echoed, and do not required an "Enter".

When you open a file, vi copies it into a buffer and displays it for editing.
Saving copies the buffer contents back into the disk.  Use the `vi` command
to open a file for editing.

    $ vi practice

Hit `i` to enter insert mode.  Hit `esc` to enter command mode.  Quit vi by
typing `:q` and hitting enter.  Commands prefixed with a colon are ex
commands.

* `:w` writes a file
* `:wq` writes and quits
* `:!ls` lists files in current directory (! prefixes shell commands)
* `:e!` wipes edits and returns to original file
* `:q!` wipes edits and quits

Simple Editing
==============

The most important keystrokes let you enter insert and command mode: `i` and
`esc`.  In command mode, you can move the cursor via:

* `h, j, k, l` for left, down, up, right one space
* `0` moves to beginning of line
* `$` moves to end of line
* `+` and `-` moves to beginning of next/previous line
* `w` and `b` moves forwards and backwards by words
* `W` and `B` doesn't include punctuation
* number prefixed by `G` moves to line number
* number prefixing moves by multiples (`4l` moves 4 spaces right)

Some ways to insert text:

* `i` for insert mode
* `a` to append (insert mode, cursor goes after current character)
* `I` and `A` insert mode to beginning/end of line
* `o` and `O` to insert a blank line above/below current line
* `R` for overstrike mode
* numeric prefixes can be used (`50i*ESC` inserts 50 asterisks)

Some simple edit commands:

* `c` or `C` to change character or to end of line
* `cc` to change the entire line
* `r` replace a single character then go back to command mode
* `s` and `S` substitutes a character or whole line and stays in insert mode
* `~` changes the case of a letter
* `x` to delete a single character
* `d` followed by a movement to delete up to next position
* `D` to delete to end of the line
* `dd` to delete entire line
* `y`, `yy`, `Y` works like delete, but "yanks" or "copies" characters without
  deleting them
* `p` or `P` to "put" or "paste" yanked or deleted characters before or after
  the cursor
* `J` joins two lines together
* number prefixing edit commands for multiples (`4dd` deletes 4 lines)

Combining movements with edit commands is a great way to showcase vi's power:

    cw      # changes to the end of a word
    c2b     # changes back to words
    d$      # deletes to end of the line
    4dd20Gp # delete 4 lines, move to line 20, paste deleted lines

Repeating, undoing, and redoing commands:

* `.` repeats your last command
* `u` undos your last command in case you made an error
* `U` undos commands on the same line (once you move off of it, you can no
  longer use uppercase U.
* `Ctrl-R` redo and undone operation

Moving Around in a Hurry
========================

This chapter covers movement by screen, text blocks, search patterns, and
line numbers.

In normal mode, hitting `enter` will continue to move the cursor down one line.
Once it hits the bottom line, it will scroll your file.  Some more efficient
ways of scrolling:

* `^F` and `^B` scroll forward/backward one screen
* `^D` and `^U` scroll forward/backward half screen
* `^E` and `^Y` scroll forward/backward one line

You can also reposition the current screen without moving the cursor using `z`:

* `zEnter` moves current line to top of screen
* `z.` moves current line to middle of screen
* `z-` moves current line to bottom of screen
* Numeric prefix to use that line number instead of current (`20z.` moves
  cursor to line 20 and puts it in middle of screen)

Use `^L` to redraw the current screen.  You can move within a screen:

* `H` move to top line on screen
* `M` move to middle line on screen
* `L` move to last line on screen
* it might be easier to just use line number and `G`

Some handy movement commands are:

* `^` moves to first nonblank character of current line
* `n|` moves to column n of current line
* `e` or `E`  moves to end of word with or without punctuation
* `(` or `)` moves to beginning or end of sentence
* `{` or `}` moves to beginning or end of paragraph
* `[[` or `]]` moves to beginning or end of section

Use `/` to move by searches:

* `/pattern` will look for "pattern"
* `?pattern` to search backwards
* `n` or `N` to repeat searches forwards/backwards
* searching is a movement command that works with other commands (`d?move`
  deletes from the cursor to "move" pattern)

You can limit your search to the current line also:

* `fx` or `Fx` moves cursor to next/previous occurrence of x character
* `tx` or `Tx` moves cursor to right before/after next/previous occurrence
* `;` or `,` repeats command in same/opposite direction
* use numeric prefixes to skip occurrences

Use `^G` to see current line number and column number.  Use a number prefix and
`G` to go to a specific line number.

Beyond the Basics
=================

This chapter includes extra options when starting vi, using registers for copy
and deletes, and marking places within a file.

Some options when starting vi:

* `vi +n file` opens at line number n
* `vi + file` opens at last line
* `vi +/pattern file` opens at first occurrence of pattern
* `vi -R file` or `view file` for readonly
* `vi -r` or `vi -r file` to recover a file from a previous cash

Vi lets you place yanks into registers identified by letters, so you can paste
more than just the last deletion/yank.  Vi remembers the last nine deletions
using numbered buffers or registers.

* `"2p` will paste the second-to-last deletion
* `"1pu.u.u` etc... will cycle through your registers

You can yank/delete text into a specific register for later recall:

* `"dyy` yanks current line into register d
* `"dP` pastes from register d
* `:registers` to see a list of all current registers

You can mark your place in a file with `m`:

* `mx` marks position with the letter x
* `'x` moves cursor to first character of line of marked position
* ``x` moves cursor to marked position
* ```` moves to exact position of previous mark or context after move
* `''` moves to beginning of line of last mark or move
* `:marks` to see a list of all current marks

Introducing the ex Editor
=========================

ex is a line editor underlaying vi.  Back then, programmers would print a single
line to view it and edit.

    $ ex practice
    "practice" 6 lines, 320 characters
    :1p
    With a screen editor you can
    :s/screen/line/
    With a line editor you can
    :vi

Some ex commands you can use for editing:

* `:p` print lines
* `:nu` print lines and line numbers
* `:d` deletes lines
* `:m` moves lines
* `:co` or `:t` copies lines
* commands work on whatever line the cursor is on.  You can also prefix it with
  a single line number of line range (`:5d` deletes line 5, `:5,10d` deletes
  lines 5 to 10).  You can also specify using search patterns.

Some examples:

* `160,224m23` moves lines 160-224 to follow line 23
* `23,29co100` yanks lines 23-29 and pastes them after 100

Toggle line numbers with `:set number` and `:set nonumber`.  Print out lines
with a range followed by `#` like `:1,10#`.

Some special symbols you can use when specifying ranges:

* `.` represents the current line
* `$` represents the last line of the file
* `%` is the entire file
* `+` and `-` are numeric operators that can be performed on line numbers or
  symbols (`.,.+20` means next 20 lines).  The dot is optional and considered
  default.
* `/pattern/` is valid for ranges (`/pattern1/,/pattern2/+2` goes from the first
  pattern to the second plus two lines)
* Use `;` instead of `,` to switch the current line relative to the starting
  location (`100;+5` means lines 100 to 105)

You may only want to effect lines that include a pattern or does not include
a pattern:

* `:g/pattern/` are all lines including pattern
* `:g!/pattern/` are all lines not including the pattern
* `:60,124g/pattern/` all lines including pattern within lines 60-124

Ex commands can be combined with a vertical bar `|`.  It works like the Unix
semi-colon.  Ranges need to be specified for each command.

Some common ex commands to saving, reading, and quitting:

* `:w` writes the file
* `:w newfilename` for a new filename
* `:230,$w newfilename` only save a portion of the file
* `:340,$w >>newfilename` appends to file
* `:q` quits
* `:wq` or `:x` writes and quits
* `:read filename` or `:r filename` reads a file into the buffer
* `:e filename` opens the file in a new buffer
* `:e!` resets the current file
* `%` represents the current filename in a command (`:w %.new` saves a new
  file with extension ".new")
* `:e #` or `:b #` or `^^` switches back and forth between two buffers
* `:ls` to list current buffers
* `:b` followed by a number to switch buffers.  You can also use a filename.
  This works great with `tab` for autocompletion.

Global Replacement
==================

Advanced Editing
================

Vim (vi Improved); An Introduction
==================================

Major Vim Improvements over vi
==============================

Multiple Windows in Vim
=======================

Vim Scripts
===========

Graphical Vim (gvim)
====================

Vim Enhancements for Programmers
================================

Other Cool Stuff in Vim
=======================
