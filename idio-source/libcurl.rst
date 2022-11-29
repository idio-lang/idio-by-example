.. include:: ./global.rst

#######
libcurl
#######

``libcurl`` is a simple wrapper around the libcurl easy functions for
libcurl v7.73.0 or later.

``curl-easy-setopt`` accepts multiple keyword-argument pairs where the
keyword can be :samp:`:CURLOPT_{name}` or just :samp:`:{name}` where
:samp:`{name}` is a case-insensitive libcurl option.

There are two extra options, ``:reader`` and ``:writer``, for
supplying an input handle or output handle respectively when
performing a transfer.  The default is for libcurl to use *stdin* and
*stdout*.

***********
GET Example
***********

.. code-block:: idio
   :caption: :file:`libcurl-GET.idio`

   import libcurl

   curl := (curl-easy-init)

   ;; You can use booleans for (obvious?) boolean options
   ;; cf. curl -L
   curl-easy-setopt curl :FOLLOWLOCATION #t

   ;; In case the TLS cert is banjaxed
   ;; cf. curl -k
   curl-easy-setopt curl :SSL_VERIFYHOST #f :SSL_VERIFYPEER #f

   curl-easy-setopt curl :URL "https://idio-lang.org"

   ;; Here's the interesting bit.  If we want to collect the output we
   ;; need to supply an output handle.  In this example, we'll use a
   ;; string handle:
   osh := (open-output-string)

   curl-easy-setopt curl :writer osh

   curl-easy-perform curl

   str := get-output-string osh

   printf "The webpage was %d chars\n" (string-length str)

.. code-block:: console

   $ idio libcurl-GET
   The webpage was 5453 chars

As a slight spin on the above test, we could use a pipe handle for our
writer and ``wc -c`` as the pipeline to achieve something similar:

.. code-block:: idio
   :caption: :file:`libcurl-pipe.idio`

   import libcurl

   curl := (curl-easy-init)

   curl-easy-setopt curl :FOLLOWLOCATION #t
   curl-easy-setopt curl :SSL_VERIFYHOST #f :SSL_VERIFYPEER #f
   curl-easy-setopt curl :URL "https://idio-lang.org"

   printf "wc -c: "

   oph := pipe-into wc -c

   curl-easy-setopt curl :writer oph

   curl-easy-perform curl

   ;; XXX curl does not close the writer and wc -c will be blocked
   ;; reading stdin.
   close-handle oph


.. code-block:: console

   $ idio libcurl-pipe
   wc -c: 5464

Why do the counts differ by one?  Sphinx has added a
transiently-hidden Pilcrow, ¶ U+00B6, at the end of the title,
**Idio**.  :lname:`Idio` was counting characters and :program:`wc` was
counting bytes.  If your :program:`wc` supports it, you can try ``wc
-m`` to count characters instead.

For a PUT example, you would set ``:reader`` to an input handle and
set ``:UPLOAD`` to true.

.. include:: ./commit.rst
