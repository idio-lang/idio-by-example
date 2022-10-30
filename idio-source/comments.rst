.. include:: ./global.rst

########
Comments
########

Most comments in :lname:`Idio` are *line comments* which start with a
semi-colon, ``;``, and run to the end of the line:

.. code-block:: idio

   ;; say something important

   echo "Hello World"	; this is it!

There's no functional difference between the number of semi-colons
forming the start of a line comment (the first one marks the start!)
but, historically, some code editors have used the number to
distinguish the *indentation level* of the comment:

#. one semi-colon for comments on the same line as a piece of code to
   be aligned towards the right margin

#. two semi-colons for comments to be aligned at the same indentation
   level as the adjacent code

#. three (or more) semi-colons to comments to be aligned at the left
   margin

**************
Block comments
**************

Putting semi-colons at the start of each line of a comment is okay for
a small-ish number of lines but at some point you'll want to comment
out entire blocks of code (or commentary!).  This is where
*block comments* come into their own.

Block comments are delimited by ``#*`` through to a (matching) ``*#``
and ignore line comments.  We can comment out the whole body of the
file with:

.. code-block:: idio

   #*

   Ignore the lot!

   ;; say something important

   echo "Hello World"	; this is it!

   *#

Block comments can be *nested*.  Notice, here, how the inner
block comment includes the line comment but that the semi-colon
doesn't break the nesting, it is just ignored:

.. code-block:: idio

   #*

   Ignore the lot!

   ;; say something important

   echo "Hello World"	#* ; nested block comment *#

   *#

If, for some reason, your existing line comment includes the end
block comment delimiter, you can *escape* it with a backslash:

.. code-block:: idio

   #*

   Ignore the lot!

   ;; say something important

   echo "Hello World"	; is this it? \*#  phew!

   *#

Semi-Literate Comments
======================

With a nod toward's Knuth's Literate Programming, :lname:`Idio`
support semi-literate comments which are ``#|`` (and a newline)
through to a (matching) ``|#``.  This idea has gone no further so
please don't use them.

Semi-literate comments behave similarly to block comments, including
nesting and both types are mutually aware.  The idea, here, is that
you can (block) comment out a semi-literate comment and,
correspondingly, include a block comment in a semi-literate comment.

*******************
Expression Comments
*******************

A final, rarely-used form of comment is the *expression comment*.
Here, ``#;`` comments out the next *expression* which might be more or
less than you think.

It is usually used to comment out a multiline expression:

.. code-block:: idio

   before thing
   #;(do
        some
	thing)
   after thing

.. include:: ./commit.rst
