.. include:: ./global.rst

######
Expect
######

The ``expect`` module handles simple :manpage:`expect(1)`\ -like
behaviour.  The two main functions are ``exp-case`` and ``exp-send``.

One difference in behaviour from :manpage:`expect(1)` is that
``exp-timeout`` is -1 by default (as :ref-author:`Don Libes` has said
he should have done himself).

.. code-block:: idio
   :caption: :file:`simple-expect.idio`

   import expect

   spawn nslookup

   (exp-case
    (:re "> $" #t))

   ;; add a \r to the end of the sent string
   exp-send "server 8.8.8.8" :cr #t

   (exp-case
    (:re "> $" #t))

   exp-send "www.google.com.\r"

   ipv4_addrs := #n
   ipv6_addrs := #n

   ;; wait for the answer section
   (exp-case
    ("answer:" #t))

   (exp-case
    (:re "Address:[[:space:]]+([.:[:xdigit:]]+)" {
      addr := nth r.1 0
      (pattern-case
       addr
       ("*.*" {
         ipv4_addrs = pair addr ipv4_addrs
       })
       ("*:*" {
         ipv6_addrs = pair addr ipv6_addrs
       }))
      (exp-continue)
    })
    (:re "> $" {
      exp-send "exit\r"
    }))

   if (not (null? ipv4_addrs)) {
     printf "the IPv4 addrs are %s\n" ipv4_addrs
   }

   if (not (null? ipv6_addrs)) {
     printf "the IPv6 addrs are %s\n" ipv6_addrs
   }

.. code-block:: console

   $ idio simple-expect
   spawn nslookup
   server 8.8.8.8.8
   Default server: 8.8.8.8
   Address: 8.8.8.8#53
   www.google.com.m.
   Server:         8.8.8.8
   Address:        8.8.8.8#53

   Non-authoritative answer:
   Name:   www.google.com
   Address: 172.217.169.36
   Name:   www.google.com
   Address: 2a00:1450:4009:818::2004
   the IPv4 addrs are ("172.217.169.36")
   the IPv6 addrs are ("2a00:1450:4009:818::2004")

*********
Functions
*********

``spawn`` runs its arguments as a command in a pseudo-terminal device
and sets :var:`spawn-id`.

``exp-case`` has ``exp-case-before`` and ``exp-case-after`` siblings.

The clauses to these are

* :samp:`({str-kw}* {str} {body})` where :samp:`{str-kw}` can be:

  * ``:re`` to treat :samp:`{str}` as a regexp

  * ``:gl`` to treat :samp:`{str}` as a glob-style pattern

  * ``:ex`` to treat :samp:`{str}` as an exact string

  * ``:icase`` to use a case-insensitive match

* :samp:`({term-kw} {body})` where :samp:`{term-kw}` can be:

  * ``:eof`` to match if End of File is indicated

  * ``:timeout`` to match if the timeout is reached

  * ``:all`` to always match

If a match occurs the :samp:`{body}` is invoked with :var:`spawn-id`,
:var:`r` (the ``REG_VERBOSE`` ``regexec`` results) and :var:`prefix`
(the contents of :var:`spawn-id`'s buffer before the match) variables
in scope.

``exp-continue`` and ``exp-break`` are available in :samp:`{body}`.

``exp-wait`` will try to drain :var:`spawn-id` and potentially call
``exp-close``.

.. include:: ./commit.rst
