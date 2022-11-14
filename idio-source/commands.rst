.. include:: ./global.rst

########
Commands
########

:lname:`Idio` will look for a external command on your :envvar:`PATH`
if the expression in functional position evaluates to a symbol (or a
string).  The arguments to the command should generally be symbols,
numbers, strings or lists of those.

Any of the function or arguments can be a variable, so long as it
evaluates to a symbol, string, etc..

Other :lname:`Idio` values, eg. ``#f`` have no sensible string form to
pass to an external command and the use of them will result in an
error.

.. code-block:: idio
   :caption: :file:`simple-commands.idio`

   fn := "foo.txt"
   touch fn
   ls -l fn

   rm fn

.. code-block:: console

   $ idio simple-commands
   -rw-rw-r--. 1 idf idf 0 Nov 14 13:32 foo.txt
		
**************
List Arguments
**************

If any of the arguments is a list they will be expanded out (by one
level) *in situ*:

.. code-block:: idio
   :caption: :file:`list-argument.idio`

   fns := map (function (base) {
     append-string base ".txt"
   }) '("foo" "bar" "baz")

   printf "fns is %s\n" fns

   ;; prefix and suffix fns
   echo 1 fns 2

.. code-block:: console

   $ idio list-argument
   fns is ("foo.txt" "bar.txt" "baz.txt")
   1 foo.txt bar.txt baz.txt 2
		
**********************
Argument-less Commands
**********************

:lname:`Idio` is a programming language first so the use of an
external command's name on its own, without arguments, will result in
just the symbol of the command's name.  To force the invocation of the
command we *must* wrap it in parentheses.

We can demonstrate this by having a function return the name of the
external command to use:

.. code-block:: idio
   :caption: :file:`single-command.idio`

   define (calculate-cmd) {
     ;; [[ *complex-processing* ]]

     ;; we don't *have* to quote date, here, but it clarifies that cmd
     ;; will evaluate to a symbol
     cmd := 'date

     ;; now return the value of cmd which is just a single variable on
     ;; its own
     cmd
   }

   to-do := (calculate-cmd)

   printf "trying %s\n" to-do
   ;; this returns the symbol date (to no-one) just like the last
   ;; expression in calculate-cmd above
   to-do					

   printf "trying (%s)\n" to-do
   (to-do)

.. code-block:: console

   $ idio single-command
   trying date
   trying (date)
   Mon 14 Nov 13:34:08 GMT 2022
		
.. include:: ./commit.rst
