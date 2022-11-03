.. include:: ./global.rst

#######
Sorting
#######

:lname:`Idio` uses the Scheme SRFI-95_\ -style sorting and merging
functions.

Here the primary sorting API is :samp:`sort {seq} {less?}
[{accessor}]` which uses the comparator :samp:`{less?}` to order the
elements of the sequence :samp:`{seq}` (a list or array).

:samp:`{less?}` doesn't actually have to order the first less than the
second but just be consistent.  Here we'll use the numeric "greater
than" function as our ordering function.

.. code-block:: idio
   :caption: :file:`simple-sort.idio`

   nums := list 1 7 5 3 9

   define (my-gt n1 n2) {
     n1 gt n2
   }

   printf "nums high to low = %s\n" (sort nums my-gt)

.. code-block:: console

   $ idio simple-sort
   nums high to low = (9 7 5 3 1)

.. tip::

   If you wanted to use ``gt`` directly then you need to escape its
   *operator* functionality from the reader, so ``(sort nums \gt)``.

********
Accessor
********

The optional :samp:`{accessor}` allows you to use the elements of
:samp:`{seq}` as keys into any other data structure and
:samp:`{less?}` will operate on the result of calling
:samp:`({accessor} {element})`.

In this example we want to sort some strings by their length.  Here we
can use ``string-length`` as the :samp:`{accessor}` which will return
a number as a proxy for the string:

.. code-block:: idio
   :caption: :file:`accessor-sort.idio`

   strs := '("apple"
   	  "banana"
   	  "chocolate")

   printf "strs longest to shortest = %s\n" (sort strs \gt string-length)

.. code-block:: console

   $ idio accessor-sort
   strs longest to shortest = ("chocolate" "banana" "apple")

Sorting By File Attributes
==========================

This accessor technique is used for sorting files by some of their
:manpage:`stat(2)` attributes.

When ``sort-atime`` (and siblings) is passed a list of filenames it
first ``libc/stat``\ s each file and constructs a table of the results
indexed by the file name.  Subsequently, the accessor function can
look up the results, access the ``st_atime`` member and use the
``C/<`` comparator to order the results.

We can try that with the Unicode files in :file:`utils/Unicode` in the
source distribution.

.. code-block:: idio
   :caption: :file:`unicode-sort.idio`

   files := glob "utils/Unicode/*"
   sorted-files := sort-size files

   for file in sorted-files {
     ls -l file
   }

Note that :program:`ls` will re-sort any list of files it is given
hence for our example we need to pass each file individually resulting
in the less elegant output:

.. code-block:: console

   $ idio unicode-sort
   -rw-rw-r--. 1 idf idf 281 Nov  2 16:12 utils/Unicode/README
   -rw-rw-r--. 1 idf idf 22781 Jan 28  2022 utils/Unicode/UTF-8-test.txt
   -rw-rw-r--. 1 idf idf 96863 Nov  2 16:12 utils/Unicode/GraphemeBreakProperty.txt
   -rw-rw-r--. 1 idf idf 132360 Nov  2 16:12 utils/Unicode/PropList.txt
   -rw-rw-r--. 1 idf idf 1053943 Nov  2 16:12 utils/Unicode/DerivedCoreProperties.txt
   -rw-rw-r--. 1 idf idf 1913704 Nov  2 16:12 utils/Unicode/UnicodeData.txt

.. include:: ./commit.rst
