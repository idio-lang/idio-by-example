.. include:: ./global.rst

##############
String Handles
##############

Handles are the values that represent I/O in :lname:`Idio`.  Obviously
that means files and pipes and other traditional operating system I/O
entities but also *strings* which is more unusual.

We can create input (to be read from) and output (to be written to)
string handles which can subsequently be manipulated almost
identically to regular I/O handles.

Having written to an output string handle we must call
``get-output-string`` to get the accumulated string back.  You might
compare with this having to run :program:`cat` to retrieve the output
you had written to a file.

.. code-block:: idio
   :caption: :file:`input-string-handles.idio`

   ;; normally the input string would not be static
   ish := open-input-string "hello\nworld\n"

   printf "line 1 is %s\n" (read-line ish)
   printf "line 2 is %s\n" (read-line ish)

   ;; good housekeeping
   close-handle ish

.. code-block:: console

   $ idio input-string-handles
   line 1 is hello
   line 2 is world

Let's try reading from a regular file, writing it out to a string
handle then printing the content of the string handle.

.. warning::

   This assumes that :file:`output-string-handles.idio` is in the current
   directory.  Adjust as appropriate for your system.

   Ordinary files are not searched for on :envvar:`IDIOLIB`.

.. code-block:: idio
   :caption: :file:`output-string-handles.idio`

   ;; open ourselves -- inception!
   fh := open-input-file "output-string-handles.idio"

   ;; create our destination string
   osh := (open-output-string)

   while #t {
     ln := handle-line fh
     line := read-line fh

     if (eof? fh) (break) {
       hprintf osh "line %2d: %s\n" ln line
     }
   }

   ;; otherwise we waste a file descriptor
   close-handle fh

   printf "%s" (get-output-string osh)

   ;; osh would be garbage collected when it went out of scope
   close-handle osh

.. code-block:: console

   $ idio output-string-handles
   line  1: ;; open ourselves -- inception!
   line  2: fh := open-input-file "output-string-handles.idio"
   line  3: 
   line  4: ;; create our destination string
   line  5: osh := (open-output-string)
   line  6: 
   line  7: while #t {
   line  8:   ln := handle-line fh
   line  9:   line := read-line fh
   line 10: 
   line 11:   if (eof? fh) (break) {
   line 12:     hprintf osh "line %2d: %s\n" ln line
   line 13:   }
   line 14: }
   line 15: 
   line 16: ;; otherwise we waste a file descriptor
   line 17: close-handle fh
   line 18: 
   line 19: printf "%s" (get-output-string osh)
   line 20: 
   line 21: ;; osh would be garbage collected when it went out of scope
   line 22: close-handle osh

.. include:: ./commit.rst
