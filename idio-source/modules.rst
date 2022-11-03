.. include:: ./global.rst

#######
Modules
#######

:lname:`Idio` supports a (far too) simple module mechanism which
creates a namespace and limits visibility of names within that
namespace to just those explicitly exported.

``import`` uses ``require`` in turn so the library file must be called
:file:`{name}.idio` and end with :samp:`provide {name}`.

Each ``import`` expression has its effects prepended to any existing
imports.  Any individual ``import`` expression retains the ordering of
the line.

Once a module has been *loaded* into :lname:`Idio` you can always
reference the module's exported names as :samp:`{module}/{name}`.

If you *import* a module, you are first ``require``'ing the module and
then adding the module's exported names into your own namespace.

.. code-block:: idio
   :caption: :file:`A.idio`

   module A

   export (
   	 writer
   )

   ;; libc is known to Idio but has not been imported into this
   ;; namespace, A, so we must use direct names
   define (the-actual-writer str) {
     ;; we pass Idio strings to libc/write
     libc/write libc/STDOUT_FILENO "A: "
     libc/write libc/STDOUT_FILENO str
   }

   define (writer str) {
     the-actual-writer str
   }

   provide A

.. code-block:: idio
   :caption: :file:`B.idio`

   module B

   export (
   	 writer
   )

   ;; add libc names to our namespace for convenience
   import libc

   define (the-actual-writer str) {
     write STDOUT_FILENO "B: "
     write STDOUT_FILENO str
   }

   define (writer str) {
     the-actual-writer str
   }

   provide B

.. code-block:: idio
   :caption: :file:`simple-module.idio`

   import A B
   ;; the same effect is achieved with
   ;;
   ;; import B
   ;; import A

   A/writer "called A\n"
   B/writer "called B\n"

   ;; name clash: A/writer will be preferred to B/writer because it was
   ;; first on the single import expression
   writer "called?\n"

.. code-block:: console

   $ idio factorial
   A: called A
   B: called B
   A: called?

****
libc
****

The ``libc`` namespace is full of inconvenient name clashes (``read``,
``write``, ``mkdir`` etc.) which makes it a tricky customer.

Much of the :lname:`Idio` library code does not ``import libc`` but
simply uses the direct names, ``libc/read`` etc..

.. include:: ./commit.rst
