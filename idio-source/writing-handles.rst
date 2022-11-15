.. include:: ./global.rst

##################
Writing To Handles
##################

All of the printing functions support writing to handles (any kind of
output handle, file, string etc.).

Hiding in plain sight has been that ``printf`` has been implicitly
writing to the "current output handle".  Here, ``printf ...`` is a
simple wrapper to

``hprintf (current-output-handle) ...``

where ``hprintf`` is like :manpage:`fprintf(3)` in that it takes a
handle as a parameter.  Similarly, ``eprintf`` sends its output to the
current error handle.

* ``write-char`` writes one Unicode code point to the handle

* ``write`` writes the *reader form* of its argument to the handle

  This only affects strings and Unicode code points.  The normal
  "display" form is the UTF-8 encoding of the string or code point.
  The reader form is ``"foo"`` or ``#\a`` suitable for the reader to
  read back in.

* ``puts`` writes a single string to the handle

* ``display`` writes the display form of a value to the handle

  The display form of many values might be an invalid reader form, for
  example, a handle:

  .. code-block:: idio

     #<H ofw!iF   1:"*stdout*":1:0>

* ``printf`` and siblings implement formatted values with a familiar
  feel

.. code-block:: idio
   :caption: :file:`writing-handles.idio`

   fh := open-file "/tmp/words.txt" "r+"

   while #t {
     ;; remember pos so we can seek correctly
     pos := handle-pos fh
     cp := read-char fh

     (cond
      ((eof? cp) {
        (break)
      })
      ((Uppercase? cp) {
        ;; cp is not a fixnum so no comparators
        cv := unicode->integer cp

        ;; Only ASCII in case the Uppercase UTF-8 encoding is a different
        ;; number of bytes
        if (cv lt #x80) {
          seek-handle fh pos
          write-char (->Lowercase cp) fh
        }
      }))
   }

   close-handle fh

.. code-block:: console

   $ echo -e "\u0126eLLo\nWorld" > /tmp/words.txt
   $ cat /tmp/words.txt
   ĦeLLo
   World

   $ idio writing-handles
   $ cat /tmp/words.txt
   Ħello
   world

.. include:: ./commit.rst
