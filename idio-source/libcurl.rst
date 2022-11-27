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

   $ idio lc
   The webpage was 5453 chars

For a PUT example, you would set ``:reader`` to a handle and set
``:UPLOAD`` to true.

.. include:: ./commit.rst
