Easy Start Guide for Recursive Servers
======================================

This section provides the minimum amount of information to setup a
working DNSSEC-aware recursive server, also known as a validating
resolver. A validating resolver performs validation for each remote
response received, following the chain of trust to verify the answers it
receives are legitimate through the use of public key cryptography and
hashing functions.

.. _enabling-validation:

Enabling DNSSEC Validation
--------------------------

So how do we turn on DNSSEC validation? Well, firstly you may not need
to reconfigure your name server at all, since recent versions of BIND
packages and distributions have been shipped with DNSSEC validation
enabled by default. Before making any configuration changes, check
whether or not you already have DNSSEC validation by following steps
described in `??? <#how-to-test-recursive-server>`__.

In earlier versions of BIND including, at the time of writing
(mid-2020), the current extended support version (9.11), DNSSEC
validation has to be explicitly enabled. To do this, you only need to
add one line to the ``options`` section of your configuration file:

::

   options {
        ...
        dnssec-validation auto;
        ...
    };

Restart ``named`` or use ``rndc reconfig``, and your recursive server is
now happily validating each DNS response. If this does not work for you,
and you have already verified DNSSEC support as described in
`??? <#dnssec-support-in-bind>`__, you most likely have some other
network-related configurations that need to be adjusted. Take a look at
`??? <#network-requirements>`__ to make sure your network is ready for
DNSSEC.

.. _effect-of-enabling-validation:

Effect of Enabling DNSSEC Validation
------------------------------------

Once DNSSEC validation is enabled, any DNS response that does not pass
the validation checks will result in the domain name not getting
resolved (often a SERVFAIL status seen by the client). If everything has
been configured properly, this is good: it means that an end-user has
been protected against a malicious attack.

However, if there is a DNSSEC configuration issue (sometimes outside of
the administrator's control), a specific name, or sometimes entire
domains, may "disappear" from DNS, in that it becomes unreachable
through that resolver. For the end user, the issue may manifest itself
as name resolution being slow or failing altogether, some parts of a URL
not loading, or the web browser displaying some error message indicating
that the page cannot be displayed at all. For example, if root name
servers were misconfigured with the wrong information about ``.org``, it
could cause all validation for ``.org`` domains to fail. To the end
users, it would appear that no one could get to any ``.org`` web
sites [1]_. Should you encounter DNSSEC-related problems, don't be
tempted to disable validation: there is almost certainly a solution that
leaves validation enabled. A basic troubleshooting guide can be found in
`??? <#dnssec-troubleshooting>`__.

.. [1]
   Of course, something like this could happen reasons other than
   DNSSEC, for example the root publishing the wrong addresses for the
   ``.org`` nameservers.
