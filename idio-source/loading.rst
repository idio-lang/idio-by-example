.. include:: ./global.rst

#############
Loading Files
#############

You can *load* source files into :lname:`Idio` with :samp:`load
"{file}"` which will search :envvar:`IDIOLIB` for the actual file,
:file:`{file}.idio`.  You should not use the filename extension with
``load``.

``load`` is naïve and will blindly run everything in the file each
time you call it.

.. code-block:: idio
   :caption: :file:`dummy1.idio`

   echo "this is dummy1"

.. code-block:: idio
   :caption: :file:`simple-load.idio`

   load "dummy1"
   load "dummy1"

.. code-block:: console

   $ idio simple-load
   this is dummy1
   this is dummy1

*****************
require / provide
*****************

:samp:`require {feature}` is slightly smarter in that it will remember
when :samp:`{feature}` has been successfully loaded and not
re-load the file if it has already done so.

To ensure that, the actually loaded source file,
:file:`{feature}.idio` must end with the expression: :samp:`provide
{feature}`.

:samp:`{feature}` is now a symbol rather than the string passed to
``load``.

.. code-block:: idio
   :caption: :file:`dummy2.idio`

   echo "this is dummy2"

   provide dummy2

.. code-block:: idio
   :caption: :file:`simple-require.idio`

   require dummy2
   require dummy2

.. code-block:: console

   $ idio simple-require
   this is dummy2

.. include:: ./commit.rst
