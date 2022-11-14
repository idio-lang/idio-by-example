.. include:: ./global.rst

###############
I/O Redirection
###############

:lname:`Idio` defines some reader *operators* for I/O redirection, the
familiar ``>`` etc..  Remember that, in :lname:`Idio`, whitespace is
important as ``>`` can be part of a valid symbol.

These are implemented by changing the current *input*, *output* or
*error* handle from or to some entity.

.. note::

   Whilst the operators are limited to manipulating *stdin*, *stdout*
   and *stderr* you can effect any other changes with calls to
   ``libc/dup2`` etc. directly.

********************
Standard Redirection
********************

:samp:`< {expr}` :samp:`> {expr}` :samp:`>> {expr}` :samp:`2> {expr}` :samp:`2>> {expr}`

Here, :samp:`{expr}` can evaluate to:

* a handle (*fd* or *string*)

* a string to be opened for input or output

* ``#n`` as a shorthand for the string ``"/dev/null"``

.. code-block:: idio
   :caption: :file:`standard-io.idio`

   fn := "foo.txt"

   ;; overwrite a file
   echo "hello" > fn

   fh := open-file fn "a"

   ;; append via a file handle
   echo "world" >> fh
   close-handle fh

   cat fn

   rm fn

.. code-block:: console

   $ idio standard-io
   hello
   world
		
Using Handles
=============

Using the redirection operators on a handle **does not** implicitly
rewind the handle.  All operators are effectively continuing from the
existing file/string position.

***************
I/O Duplication
***************

:samp:`<& {expr}` :samp:`>& {expr}` :samp:`2>& {expr}`

Here, whatever :samp:`{expr}` evaluates to must already exist, so:

* a handle (*fd* or *string*)

* a :lname:`C` ``int`` (or fixnum) representing the file descriptor to
  call ``libc/dup2`` with

.. code-block:: idio
   :caption: :file:`io-dup.idio`

   fn := "foo.txt"

   fh := open-output-file fn
   hprintf fh "hello\n"

   ;; append via a file handle
   echo "world" >& fh

   close-handle fh

   cat fn
   rm fn

.. code-block:: console

   $ idio io-dup
   hello
   world
		
.. include:: ./commit.rst
