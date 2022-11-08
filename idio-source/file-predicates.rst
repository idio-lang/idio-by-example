.. include:: ./global.rst

###############
File Predicates
###############

There are the usual pathname *predicates* such as ``f?``, ``d?`` and
``r?`` which are comparable to familiar shell predicates ``-f``,
``-d`` and ``-r``.

.. code-block:: idio
   :caption: :file:`file-predicates.idio`

   define (test-for fn) {
     (cond
      ((f? fn) {
        printf "%s is a file\n" fn
      })
      ((d? fn) {
        printf "%s is a directory\n" fn
      })
      (else {
        printf "%s is beyond knowledge\n" fn
      }))
   }

   fn := "words.txt"

   test-for "."

   ;; just in case
   if (f? fn) (delete-file fn)

   test-for fn
   touch fn
   test-for fn


.. code-block:: console

   $ idio file-predicates
   . is a directory
   words.txt is beyond knowledge
   words.txt is a file

.. include:: ./commit.rst
