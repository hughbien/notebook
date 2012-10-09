Practical Common Lisp
=====================

An introduction to Lisp by Peter Seibel.  Use Lisp to get more done at a
faster development speed.  Seibel calls it the "programmable programming
language."

Lather, Rinse, Repeat: A Tour of the REPL
=========================================

There is no single standard Lisp implementation.  Instead, there's a standard
defined by ANSI.  One of the implementations is SBCL or Steel Bank Common Lisp.
To install it on Arch Linux:

    pacman -S sbcl

Seibel recommends using Lisp in a Box.  Try using the REPL:

    * 10
    10
    * (+ 2 3)
    5
    * "hello, world"
    "hello, world"
    * (format t "hello, world")
    hello, world
    NIL

You can define functions with `defun` keyword:

    * (defun hello-world () (format t "hello, world"))
    HELLO-WORLD
    * (hello-world)
    hello, world
    NIL

You can save the function within a file and run it:

    $ sbcl --script hello.lisp

If you start the interpreter, you can load any files:

    * (load "hello.lisp")

You can compile a file with `COMPILE-FILE` which results in a FASL file, it
stands for fast-load file.  `LOAD` can be used with the `*.fasl` file also:

    * (load (compile-file "hello.lisp"))

Practical: A Simple Database
============================

The `LIST` function creates a list from its arguments:

    * (list 1 2 3)
    (1 2 3)

A `property list` or `plist` works like a key value store:

    * (list :a 1 :b 2 :c 3)
    (:A 1 :B 2 :C 3)
    * (getf (list :a 1 :b 2 :c 3) :a)
    1

Let's make a function which creates a plist representation of a CD:

    * (defun make-cd (title artist rating ripped)
        (list :title title :artist artist :rating rating :ripped ripped))
    * (make-cd "Roses" "Kathy Mattea" 7 t)
    (:TITLE "Roses" :ARTIST "Kathy Mattea" :RATING 7) 

You can define a global variable with the `DEFVAR` macro.  By convention,
globals are named with surrounding asterisks.  Use the `PUSH` macro to add items
to a list.

    * (defvar *db* nil)
    * (defun add-record (cd) (push cd *db*))
    * (add-record (make-cd "Roses" "Kathy Mattea" 7 t))
    ((:TITLE "Roses" :ARTIST "Kathy Mattea" :RATING 7 :RIPPED T))

You can always type a variable name into the REPL to see what it contains.
Let's write a `dump-db` function to get a pretty print version.

    * (defun dump-db ()
        (dolist (cd *db*)
          (format t "~{~a:~10t~a~%~}~%" cd)))

The `DOLIST` macro loops through each element binding it to the variable `cd`.
`FORMAT` is then used to print it.

* `~a` is the aesthetic directive, it consumes one argument and prints it out
  in a human readable format.
* `~10t` means emit enough spaces to get to the 10th column (tabular)
* `~{` and `}~` means consume each element in the list and format

Let's write a prompt function to get data from the user:

    * (defun prompt-read (prompt)
        (format *query-io* "~a: " prompt)
        (force-output *query-io*)
        (read-line *query-io*))

`FORMAT` is used to print out the prompt, `FORCE-OUTPUT` is required in some
lisp implementations to print without a newline, `READ-LINE` will actually read
the user's input.  `*query-io*` is the stream connected to the terminal.

    * (defun prompt-for-cd ()
        (make-cd
          (prompt-read "Title")
          (prompt-read "Artist")
          (or (parse-integer (prompt-read "Rating") :junk-allowed t) 0)
          (y-or-n-p "Ripped [y/n]")))
    * (defun add-cds ()
        (loop (add-record (prompt-for-cd))
          (if (not (y-or-n-p "Another? [y/n]: ")) (return))))

Let's create a function to save our data structure to disk.  The
`WITH-OPEN-FILE` macro opens and writes to a file:

    * (defun save-db (filename)
        (with-open-file (out filename
                         :direction :output
                         :if-exists :supersede)
          (with-standard-io-syntax
            (print *db* out))))
    * (defun load-db (filename)
        (with-open-file (in filename
          (with-standard-io-syntax
            (setf *db* (read in))))))

`SETF` macro is an assignment operator.  Now let's make a function to query
records.  The `REMOVE-IF-NOT` function will filter out items in a list.

    * (remove-if-not #'evenp '(1 2 3 4 5 6 7 8 9 10))
    (2 4 6 8 10)

The `#'` notation is shorthand for a function pointer.  You can also use
anonymous functions:

    (lambda (x) (= 0 (mod x 2)))

Use the `EQUAL` function to determine if two strings are equal.

    * (defun select-by-artist (artist)
        (remove-if-not
          #'(lambda (cd) (equal (getf cd :artist) artist))
          *db*))

Instead of making a distinct function for each attribute, we can make a function
which takes a selector function as an argument:

    * (defun select (selector-fn)
        (remove-if-not selector-fn *db*))
    * (select #'(lambda (cd) (equal (getf cd :artist) "Dixie Chicks")))

Seibel notes that it's kind of gross looking, so let's make a factory:

    * (defun artist-selector (artist)
        #'(lambda (cd) (equal (getf cd :artist) artist)))
    * (select (artist-selector "Dixie Chicks"))

A quick course in keyword selector, function definitions can be like:

    (defun foo (&key a b c) (list a b c))
    (foo :a 1 :b 2 :c 3)  ==> (1 2 3)
    (foo :a 1 :c 3)       ==> (1 NIL 3)
    (foo)                 ==> (NIL NIL NIL)

Lisp allows you to define default parameters to use instead of `NIL`.  There's
also an optional `supplied-p` parameter which will be true or false depending
on if the function call supplied a parameter or not:

    (defun foo (&key a (b 20) (c 30 c-p)) (list a b c c-p))
    (foo :a 1 :b 2 :c 3)  ==> (1 2 3 T)
    (foo :a 1 :c 3)       ==> (1 20 3 T)
    (foo)                 ==> (NIL 20 30 F)

Now for the ultimate querying function:

    (defun where (&key title artist rating (ripped nil ripped-p))
      #`(lambda (cd)
          (and
            (if title    (equal (getf cd :title)  title)  t)
            (if artist   (equal (getf cd :artist) artist) t)
            (if rating   (equal (getf cd :rating) rating) t)
            (if ripped-p (equal (getf cd :ripped) ripped) t))))
    (select (where :rating 10 :ripped nil))

We can build an `update` function now using `MAPCAR` to map over `*db*` and
update records.  `MAPCAR` iterates through a list and runs a function, returning
a new list:

    (defun update (selector-fn &key title artist rating (ripped nil ripped-p))
      (setf *db*
            (mapcar
             #'(lambda (row)
                 (when (funcall selector-fn row)
                   (if title    (setf (getf row :title) title))
                   (if artist   (setf (getf row :artist) artist))
                   (if rating   (setf (getf row :rating) rating))
                   (if ripped-p (setf (getf row :ripped) ripped)))
                 row) *db*)))
    (update (where :artist "Dixie Chicks") :rating nil)

Making a delete function is easy:

    (defun delete-rows (selector-fn)
      (setf *db* (remove-if selector-fn *db*)))

Seibel introduces Lisp's macro system to remove duplication and efficiency.
In this case, there's duplication in our `where` and `update` functions for
each field:

    (if title (equal (getf cd :title) title) t)
    (if artist (equal (getf cd :artist) artist) t)
    ...

The Lisp compiler evaluates a macro by passing the arguments unevaluated which
returns a new expression.  A macro is created with `DEFMACRO`:

    * (defmacro backwards (expr) (reverse expr))
    * (backwards ("hello, world" t format))
      hello, world
      NIL

The expression `("hello, world" t format)` isn't valid Lisp code.  But that
doesn't matter.  At compile time, it gets reversed by Lisp and turns into
valid code.  What's interesting is that Lisp code uses the list data structure,
so functions like `REVERSE` works on source code too.

A single quote will stop evaluation of a single expression:

    * '(1 2 3)
    (1 2 3)
    
Backticks can be used to stop evaluation of an expression, except for sub
expressions preceded by a comma:

    `(1 2 (+ 1 2))  ==> (1 2 (+ 1 2))
    `(1 2 ,(+ 1 2)) ==> (1 2 3)

We can use this to make a function which returns a comparator:

    (defun make-comparison-expr (field value)
      `(equal (getf cd ,field) ,value))
    (defun make-comparison-list (fields)
      (loop while fields
        collecting (make-comparison-expr (pop fields) (pop fields))))
    (defmacro where (&rest clauses)
      `#'(lambda (cd) (and ,@(make-comparisons-list clauses))))

`,@` splices the expression into the enclosing list:

    `(and ,(list 1 2 3))  ==> (AND (1 2 3))
    `(and ,@(list 1 2 3)) ==> (AND 1 2 3)

`&rest` turns a function into a varargs function.

    * (select (where :title "Give Us a Break" :ripped t))
    ((:TITLE "Give Us a Break" :ARTIST "Limpopo" :ripped T))

Syntax and Semantics
====================

Functions
=========

Variables
=========

Macros: Standard Control Constructs
===================================

Macros: Defining Your Own
=========================

Practical: Building a Unit Test Framework
=========================================

Numbers, Characters, and Strings
================================

Collections
===========

They Called it LISP for a Reason: List Processing
=================================================

Beyond Lists: Other Uses for Cons Cells
=======================================

Files and File I/O
==================

Practical: A Portable Pathname Library
======================================

Object Reorientation: Generic Functions
=======================================

Object Reorientation: Classes
=============================

A Few FORMAT Recipes
====================

Beyond Exception Handling: Conditions and Restarts
==================================================

The Special Operators
=====================

Programming in the Large: Packages and Symbols
==============================================

LOOP for Black Belts
====================

Practical: A Spam Filter
========================

Practical: Parsing Binary Files
===============================

Practical: An ID3 Parser
========================

Practical: Web Programming with AllegroServe
============================================

Practical: An MP3 Database
==========================

Practical: A Shoutcast Server
=============================

Practical: An MP3 Browser
=========================

Practical: An HTML Generation Library, the Interpreter
======================================================

Practical: An HTML Generation Library, the Compiler
===================================================

Conclusion: What's Next?
========================
