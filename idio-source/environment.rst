.. include:: ./global.rst

#####################
Environment Variables
#####################

When :lname:`Idio` starts, all environment variables are instantiated
as variables with *dynamic scope*, tagged as *environ* and referencing
strings.  A small number, :envvar:`PATH`, :envvar:`HOME` etc., will be
set if no such value exists in the initial environment.

You manipulate environment variables as normal variables, they're just
strings.

Whenever an external command is about to be launched a new environment
is calculated from the set of variables in scope tagged as environ
variables.

.. code-block:: idio
   :caption: :file:`simple-env.idio`

   ;; USER is a variable so we'll get everything with my login id in it
   env | grep USER

   puts "\nTry again\n\n"

   ;; grep will now be passed the string USER as an argument
   env | grep 'USER

.. code-block:: console

   $ idio simple-env
   ...
   PATH=/home/idf/.local/bin:...
   HOME=/home/idf
   LOGNAME=idf
   PWD=/home/idf/src/idio.git
   USER=idf
   _=/home/idf/.local/bin/idio
   OLDPWD=/home/idf
   IDIOLIB=/home/idf/.local/lib/idio/0.3
   ...

   Try again

   USER=idf
		
*************************
Extending the Environment
*************************

You can create a new environment variable with the ``:*`` operator
(cf. the ``:=`` operator for lexical variables).

If you use ``:*`` at the top level (ie. outside of a block) then it is
a regular environment variable like any other.

Inside a block, though, these definitions become *transient* with a
lifetime to the end of the block.

For new environment variables this works as you would expect.  For
existing environment variables, this new definition *shadows* the
existing one (for the lifetime of the block).

Finally, in a block, ``!*`` will transiently *unset* an existing
environment variable.

.. code-block:: idio
   :caption: :file:`extend-env.idio`

   define (reporter msg) {
     puts msg
     (newline)
     if (not (env | grep ^USER)) (puts "Nothing to see here...\n")
     puts "\n"
   }

   reporter "initial state"

   {
     USERNAME :* "Bob"

     reporter "with a new environment variable"
   }

   reporter "back to initial state"

   {
     USER :* "Sue"

     reporter "with a shadowed environment variable"
   }

   reporter "back to initial state"

   {
     !* USER

     reporter "with an environment variable unset"
   }

   reporter "back to initial state"

   USER = "Rita"

   reporter "just a regular variable"

.. code-block:: console

   $ idio extend-env
   initial state
   USER=idf

   with a new environment variable
   USERNAME=Bob
   USER=idf

   back to initial state
   USER=idf

   with a shadowed environment variable
   USER=Sue

   back to initial state
   USER=idf

   with an environment variable removed
   Nothing to see here...

   back to initial state
   USER=idf

   just a regular variable
   USER=Rita

		
.. include:: ./commit.rst
