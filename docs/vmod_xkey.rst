..
.. NB:  This file is machine generated, DO NOT EDIT!
..
.. Edit vmod.vcc and run make instead
..

.. role:: ref(emphasis)

.. _vmod_xkey(3):

=========
vmod_xkey
=========

----------------------------------------
Surrogate keys support for Varnish Cache
----------------------------------------

:Manual section: 3

SYNOPSIS
========

import xkey [from "path"] ;

DESCRIPTION
===========

This vmod adds secondary hashes to objects, allowing fast purging on
all objects with this hash key.

You can use this to indicate relationships, a bit like a "tag". Then
clear out all object that have this tag set. Two good use cases are
news sites, where one might add all the stories mentioned on a
particular page by article ID, letting each article referenced create
an xkey header.

Similarly with an e-commerce site, where various SKUs are often
referenced on a page.

Hash keys are specified in the ``xkey`` response header. Multiple keys
can be specified per header line with a space
separator. Alternatively, they can be specified in multiple ``xkey``
response headers.

Preferably the secondary hash keys are set from the backend
application, but can also be set from VCL in ``vcl_backend_response``
as in the above example.

Example
-------

On an e-commerce site we have the backend application issue an xkey
header for every product that is referenced on that page. So the
header for a certain page might look like this::

    HTTP/1.1 OK
    Server: Apache/2.2.15
    xkey: 8155054
    xkey: 166412
    xkey: 234323

This requires a bit of VCL to be in place. The VCL can be found above.

Then, in order to keep the web in sync with the database, a trigger is
set up in the database. When an SKU is updated this will trigger an
HTTP request towards the Varnish server, clearing out every object
with the matching xkey header::

    GET / HTTP/1.1
    Host: www.example.com
    xkey-purge: 166412

Note the xkey-purge header. It is probably a good idea to protect
this with an ACL so random people from the Internet cannot purge your
cache.

Varnish will find the objects and clear them out, responding with::

    HTTP/1.1 200 Purged
    Date: Thu, 24 Apr 2014 17:08:28 GMT
    X-Varnish: 1990228115
    Via: 1.1 Varnish

The objects are now cleared.

CONTENTS
========

* :ref:`func_purge`
* :ref:`func_softpurge`

.. _func_purge:

INT purge(STRING)
-----------------

Prototype
	INT purge(STRING key)

Description
    Purges all objects hashed on `key`. Returns the number of objects that were
    purged.


.. _func_softpurge:

INT softpurge(STRING)
---------------------

Prototype
	INT softpurge(STRING key)

Description
	Performs a "soft purge" for all objects hashed on `key`.
	Returns the number of objects that were purged.

	A softpurge differs from a regular purge in that it resets an
	object's TTL but keeps it available for grace mode and conditional
	requests for the remainder of its configured grace and keep time.

