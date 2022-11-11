.. include:: ./global.rst

####################
Reading From Handles
####################

There are some simple functions for reading from handles (any kind of
input handle, file, string etc.):

* ``read-char`` reads one Unicode code point from the handle

* ``read-line`` reads one normal line (newline/ASCII LF terminated)
  from the handle.  The result does not include the newline.

* ``read-lines`` reads all remaining lines from the handle.  The
  result includes all the newlines.

All of these functions can provoke an End of File state for the handle
(including string handles!).  That will return the end of file value
whose printed form is ``#<eof>``.

You can test for the end of file value with the ``eof?`` predicate
and/or you can test the handle to see if End of File has been reached
with ``eof-handle?``.

:lname:`Idio` maintains some positional attributes when reading from a
handle: the current line accessed with ``handle-line``, starting from
1 (one); and the current position accessed with ``handle-pos``,
starting from 0 (zero).

You can rewind a handle with ``rewind-handle`` or jump to any position
with ``seek-handle``.  If you seek to anywhere other than position 0
then the line number status is lost.

.. warning::

   Seeking in files containing (non-single byte) Unicode code points
   is extremely dangerous as files are indexed by byte and therefore
   you are at risk of landing in the middle of a encoding sequence.

   Seeking in strings is OK as strings are indexed by code points.

.. code-block:: idio
   :caption: :file:`reading-handles.idio`

   ifh := open-input-file "/tmp/words.txt"

   printf "c@1 is %s\n" (read-char ifh)
   printf "c@2 is %s\n" (read-char ifh)
   printf "c*  is %s\n" (read-line ifh)

   printf "currently: line %s: pos %s\n" (handle-line ifh) (handle-pos ifh)

   ;; a handy condensed form of the handle's details
   printf "ifh is %s\n" ifh

   ;; position 4 is just before the fifth byte
   seek-handle ifh 4

   printf "c@4 is %s\n" (read-char ifh)

   ;; The example file has a trailing newline so we'll seek one position
   ;; before that
   seek-handle ifh -2 'end

   printf "e-2 is %s\n" (read-char ifh)

   ;; don't forget!
   close-handle ifh

.. code-block:: console

   $ echo -e "hello\nworld" > /tmp/words.txt

   $ idio reading-handles
   c@1 is h
   c@2 is e
   c*  is llo
   currently: line 2: pos 6
   ifh is #<H ofre   4:"/tmp/words.txt":2:6>
   c@4 is o
   e-2 is d

.. include:: ./commit.rst
