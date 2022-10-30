.. include:: ./global.rst

###########
Hello World
###########

The code for a simple *hello world* program is:

.. code-block:: idio
   :caption: :file:`hello-world.idio`

   echo "Hello World"

Save that in a file called :file:`hello-world.idio`.

:lname:`Idio`, by default, will only look in some standardized library
directories which probably doesn't include the directory where you've
just saved that file.  We can extend the places :lname:`Idio` will
search for library files in by setting the environment variable
:envvar:`IDIOLIB` with a colon-separated list of directories:

.. code-block:: console

   $ export IDIOLIB=$PWD

   $ idio hello-world
   Hello World

********
Thoughts
********

:lname:`Idio` doesn't have an ``echo`` function so it has looked for
an external command called :program:`echo` on your :envvar:`PATH`.

There are, of course, other ways to print strings which we'll come to
in due course but the obvious one is:

.. code-block:: idio

   printf "Hello World\n"

This *is* using an :lname:`Idio` function called ``printf`` (rather
than the external command, :program:`printf`) which supports printing
out :lname:`Idio` values.

.. include:: ./commit.rst
