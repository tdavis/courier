The Why And When Of Courier
===========================

This document describes why Courier was created and what it's good for.


When Services Attack
--------------------

You've built your product around a Service-Oriented Architecture. You
start with a handful of services and life is simple; they communicate
arbitrarily,

* Indirectly via a database,
* Directly via HTTP endpoints,
* Occasionally via queues, etc.

Eventually, you expand to hundreds of services! Now dozens of apps
need to know, *Was a user added to the system? Did a widget shipment
go out for delivery?*

Seemingly out of nowhere, your *Users* service is under siege. The
*Widget Shipment* team wants to support pub/sub, but some consumer
services aren't comfortable with the idea of missed messages while
others are concerned about being able to process all those messages in
real-time.

Real-World Inspiration
~~~~~~~~~~~~~~~~~~~~~~

The *Courier* authors have seen how this problem is usually solved in
the real-world: using more scalable versions of the same ad-hoc
systems we started with! Database queries are replaced with WAL_
scrapers, HTTP endpoints are converted to pull-based workers, and
common system APIs are hidden behind multi-language clients that try
(often opaquely and unsuccessfully) to present a consistent consumer
interface.


Courier To The Rescue!
----------------------

Courier solves this problem by supplying a *flexible* and *robust*
Publish/Subscribe system that doesn't require complex client
libraries, opaque wire protocols, or demand the usual "fire and
forget" mentality of the classic Pub/Sub model.

If you've built your business on SOA and are struggling with brittle
service boundaries (or want to get ahead of them), Courier may be for
you. If you've gotten this far, head over to :doc:`features` to find
out of Courier is right for you.


.. _WAL: http://en.wikipedia.org/wiki/Write-ahead_logging
