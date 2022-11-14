.. include:: ./global.rst

#######################
Piping To/From Commands
#######################

It is often useful to be able to either write data to an external
command or read data from an external command.  For this there are
four *pipe* asynchronous command forms.

Two, ``pipe-into`` and ``pipe-from``, will return a *pipe handle*.
This is another variation on an *fd handle*.  As you might expect, you
cannot seek on a pipe handle.

The other two, ``named-pipe-from`` and ``named-pipe-into``, will
return the name of a file that can be opened for reading or writing
respectively.  This is the equivalent of :lname:`Bash`'s *Process
Substitution*.  Most operating systems will use the
:file:`/dev/fd/{n}`-style of filename but some will use real FIFOs.

************
pipe variant
************

.. code-block:: idio
   :caption: :file:`pipe-into.idio`

   oph := pipe-into nslookup

   printf "oph is %s\n" oph

   hprintf oph "google.com\n"

   close-handle oph

   ;; async command last in script
   (wait)

.. code-block:: console

   $ idio pipe-into
   oph is #<H opw!   5:"/dev/fd/5":1:0>
   Server:         127.0.0.53
   Address:        127.0.0.53#53

   Non-authoritative answer:
   Name:   google.com
   Address: 216.58.212.238
   Name:   google.com
   Address: 2a00:1450:4009:80b::200e

.. note::

   This example has the asynchronous command at the end of the script
   meaning :lname:`Idio` is likely to kill it as it exits before the
   command prints anything.  We need a call to ``wait`` to ensure it
   gets a chance to run to completion.

******************
named-pipe variant
******************

.. code-block:: idio
   :caption: :file:`named-pipe-into.idio`

   opn := named-pipe-into nslookup

   printf "opn is %s\n" opn

   oph := open-output-file opn

   hprintf oph "google.com\n"

   close-handle oph

   (wait)

.. code-block:: console

   $ idio named-pipe-into
   opn is /dev/fd/5
   Server:         127.0.0.53
   Address:        127.0.0.53#53

   Non-authoritative answer:
   Name:   google.com
   Address: 216.58.212.238
   Name:   google.com
   Address: 2a00:1450:4009:80b::200e

.. include:: ./commit.rst
