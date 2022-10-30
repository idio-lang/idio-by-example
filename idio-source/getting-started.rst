.. include:: ./global.rst

###############
Getting Started
###############

There are no downloadable packages, you need to build :lname:`Idio`
from source.  The full instructions are `available
<https://idio-lang.org/get.html>`_ but the basics are:

.. code-block:: console

   git clone https://github.com/idio-lang/idio.git
   cd idio
   make

with the resultant executable as :file:`.local/bin/idio` which you can
run directly from there or add that :file:`bin` directory to your
:envvar:`PATH`.

********
Location
********

The :lname:`Idio` executable and libraries are designed to be handled
as a tarball so you can:

.. code-block:: console

   (cd .local; tar cf - bin lib) | (cd /some/where/else; tar xf -)

to put the :lname:`Idio` distribution elsewhere if having
:file:`/some/where/else/bin` on your :envvar:`PATH` is more
convenient.

Alternatively:

.. code-block:: console

   make user-install

will add the executable as :file:`$HOME/.local/bin/idio` where
:file:`$HOME/.local/bin` might already be on your :envvar:`PATH`.

**********
Test Suite
**********

You can run the test suite with:

.. code-block:: console

   make test

``make test`` explicitly uses :file:`.local/bin/idio` as the
executable.

:lname:`Idio` is regularly tested on these `systems
<http://idio-lang.org/get.html#supported-platforms>`_.

.. include:: ./commit.rst
