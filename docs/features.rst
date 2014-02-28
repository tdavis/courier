Feature Overview
================

This document provides a high-level overview of the following core features:

* HTTP/WebSocket-based communication
* Flexible subscription models support *HTTP Push*, *HTTP Pull*, and
  *WebSocket* delivery methods
* Robust data and delivery guarantees
* High throughput and low resource utilization


HTTP/WebSocket Communication
----------------------------

Instead of inventing yet another wire protocol, Courier uses
HTTP—along with WebSockets for full-duplex communication.

Additionally, Courier does not demand you use a particular
serialization format. At the time of this writing it supports JSON
with optional GZIP encoding, which is good enough for most use
cases. Support for other "command and control" serializations are on
the roadmap, though in any case we're happy to deliver messages using
whatever *Content-Type* you'd like!


Push, Pull, or Stream
---------------------

Have your messages pushed to an HTTP URL, pulled from Courier at your
leisure, or streamed over a persistent connection via the WebSocket_
protocol, it's up to you!


Data and Delivery Guarantees
----------------------------

Courier is built on the back of replicated, highly-available systems
that guarantee a consistent, correct view of data so long as a quorum
exists. A response from Courier means your event was stored
successfully and redundantly.

In addition to storage guarantees, Courier also offers *delivery*
guarantees. Every Subscriber that exists at the point of publishing
will receive the message. Courier also supplies an audit log of every
message received by each Subscriber, along with how long it took to
receive a reply, how many times sending was retried, etc. Every
individual subscription may define its own policy, including:

* Frequency and total number of retries
* How large a message queue may become
* How long the subscriber should remain offline before Courier gives up
* How un-deliverable messages should be handled

And so forth. Policy options differ based on the type of subscription
(push, pull, or stream).

.. note::

   While Courier itself is *not* `High-Availability (HA)`_ yet, its
   supporting services are. Courier supplies end-to-end *durable*
   storage, meaning your messages won't be lost if Courier itself
   crashes or otherwise disappears, making it ideal for volatile Cloud
   environments.


High Throughput; Low Utilization
--------------------------------

Courier is an Event-driven_ system, meaning it can handle orders of
magnitude more concurrent messages and subscribers than most other
systems. It can effortlessly scale up to **millions of messages per
second** on a single machine while maintaining a small memory
footprint.

.. note::

   Additionally, Courier's supporting services scale horizontally—or
   near to it—meaning overall throughput can be continuously improved
   simply by adding more machines!


.. _WebSocket: http://en.wikipedia.org/wiki/WebSocket
.. _full-duplex: http://en.wikipedia.org/wiki/Duplex_(telecommunications)
.. _Evented: http://www.eventedapi.org/
.. _Event-driven: http://en.wikipedia.org/wiki/Event-driven_architecture
.. _High-Availability (HA): http://en.wikipedia.org/wiki/High_availability
