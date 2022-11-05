.. include:: ./global.rst

######
SHASUM
######

RFC6234_, US Secure Hash Algorithms, defines a number of functions for
performing SHA hash functions SHA-1, SHA-224, SHA-256, SHA-384, and
SHA-512.  :lname:`Idio` provides interfaces to calculate these for
strings, files and file descriptors.  The default algorithm is
``SHA256``.

.. code-block:: idio
   :caption: :file:`simple-shasum.idio`

   dev-null := "/dev/null"

   printf "SHA256SUM (%s) = %s\n" dev-null (shasum-file dev-null)
   printf "SHA256SUM (\"\")        = %s\n" (shasum-string "")

.. code-block:: console

   $ idio simple-shasum
   SHA256SUM (/dev/null) = e3b0c44298fc1c149afbf4c8996fb92427ae41e4649b934ca495991b7852b855
   SHA256SUM ("")        = e3b0c44298fc1c149afbf4c8996fb92427ae41e4649b934ca495991b7852b855

.. tip::

   Pass the preferred algorithm as an extra symbol argument, eg.

   .. code-block:: idio

      shasum-file dev-null 'SHA512

.. include:: ./commit.rst
