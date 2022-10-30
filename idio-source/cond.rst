.. include:: ./global.rst

####
Cond
####

Cascading alternate conditions using ``if`` becomes a bit clumsy for
which there is a powerful solution, ``cond``.

``cond`` extends the syntax by allowing the use of :samp:`({condition}
{consequent})` tuples and accepts a final clause where the
:samp:`{condition}` is the keyword ``else``.

The result of a cond expression is that of the last expression
evaluated or ``#<void>`` if no clauses match.

As these are usually going to be on multiple lines for readability the
whole expression is normally wrapped in parentheses:

.. code-block:: idio

   (cond
    (cond1 conseq1)
    (cond2 conseq2)
    (cond3 conseq3)
    (else  conseq4))

.. code-block:: idio
   :caption: :file:`simple-cond.idio`

   define (foo a) {

     (cond
      ((a gt 0) {
        printf "a is positive\n"
      })
      ((a lt 0) {
        printf "a is negative\n"
      })
      (else {
        printf "a is zero\n"
      }))
   }

   foo 10
   foo -10
   foo 0

.. code-block:: console

   $ idio simple-cond
   a is positive
   a is negative
   a is zero

**
=>
**

An advanced use in ``cond`` is the ``=>`` form where the tuple becomes
:samp:`({condition} => {f})` where :samp:`f` is a function that
accepts one argument.

Here, the :samp:`{condition}` is evaluated and if the result is "true"
then :samp:`{f}` is invoked with the result.

It is a form of *anaphoric if* expression roughly equivalent to:

.. parsed-literal::

   tmp := *condition*
   if tmp {
     *f* tmp
   }

.. code-block:: idio
   :caption: :file:`cond.idio`

   ht := #{ }
   ht.'a = "apple"
   ht.'b = "banana"

   define (foo k) {

     ;; hash-ref takes an optional "default" argument to return if the
     ;; key doesn't exist (otherwise it raises a condition)
     ;;
     ;; If k does exist in ht then the function will be called with
     ;; whatever hash-ref returns.  If k doesn't exist in ht then
     ;; hash-ref returns #f and we try the next clause.
     (cond
      ((hash-ref ht k #f) => (function (v) {
        printf "%s is %s\n" k v
      }))
      (else {
        printf "%s is unknown\n" k
      }))
   }

   foo 'b
   foo 24

.. code-block:: console

   $ idio cond
   b is banana
   24 is unknown

.. include:: ./commit.rst
