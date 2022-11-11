.. include:: ./global.rst

#########
Constants
#########

Constants, here, means the (sets of) well-known values.  Most of them
are results indicating some state.

********
Booleans
********

The two booleans are ``#t`` for "true" and ``#f`` for "false."

In :lname:`Idio`, only ``#f`` is tested for in conditional expressions
which means that, in practice, *any other value* is equivalent to
``#t``.  This may sound odd but turns out to be very useful.

``#t``, itself, may seem a bit redundant, then, but it exists as the
nominal not-``#f`` value for expressions to return where something
not-``#f`` is required.

Unlike many programming languages, :lname:`Idio` does **not**
associate ``0`` (zero) or ``""`` (the empty string) with falsehood.
They are perfectly legitimate values and not false/invalid in any way.

****
Null
****

``#n`` is the common "null" value.  It is primarily used as a sentinel
value, notably marking the end of a proper list.

You can test if a value is ``#n`` with the ``null?`` predicate.

*************
Result Values
*************

Everything returns a value even when there is no obvious value to
return.  Hence there are several values returned from expressions
which are indicative but limited utility.  There are also some
pseudo-error values.

* ``#<unspec>`` is returned when there is no otherwise useful value to
  return

* ``#<void>`` is returned when no computation has taken place,
  notably, when a conditional expression has no fallback "else"
  clause.

  You can test for this value with the ``void?`` predicate.

* ``#<eof>`` is returned by reading functions when the end of a handle
  is reached

  You can test for this value with the ``eof?`` predicate.

These printed formats are of the form ``#<...>`` where we might get
something intelligible to humans in between the angle brackets.  These
are *invalid reader forms* which allow users to glean something useful
about an internal value when they print one out but not be able to
reconstruct the same thing by somehow feeding it back into
:lname:`Idio`.

This printed form is very common for compound objects.



.. include:: ./commit.rst
