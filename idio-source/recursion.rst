.. include:: ./global.rst

#########
Recursion
#########

:lname:`Idio` supports recursive functions.  In fact, they are the
basis of all looping constructs.

Here's the factorial function:

.. code-block:: idio
   :caption: :file:`factorial.idio`

   define (fact n) {
     if (n le 0) {
       1
     } {
       n * (fact (n - 1))
     }
   }

   printf "fact(7)   = %d\n" (fact 7)
   printf "fact(200) = %d\n" (fact 200)

.. code-block:: console

   $ idio factorial
   fact(7)   = 5040
   fact(200) = 788657867364790503552363213932185062295135977687173263294742
	       533244359449963403342920304284011984623904177212138919638830
	       257642790242637105061926624952829931113462857270763317237396
	       988943922445621451664240254033291864131227428294853277524242
	       407573903240321257405579568660226031904170324062351700858796
	       178922222789623703897374720000000000000000000000000000000000
	       000000000000000

(Yes, that result is 375 digits long and been chopped to fit the screen.)

**********************
Tail Call Optimisation
**********************

:lname:`Idio` is *tail call optimised* meaning that if the last
expression evaluated is a function call then it doesn't increase the
size of the stack.

That sounds very useful for recursive functions however you should
understand if your recursive call is the last function call.

In the factorial example, the call to the recursive function ``fact``
is just an argument to the ``*`` function which is the actual last
function call.  Here, then, we don't get the expected benefits of TCO
and can run out of memory quite easily.

.. include:: ./commit.rst
