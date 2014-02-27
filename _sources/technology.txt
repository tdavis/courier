Technology
==========

This document describes the technology behind Courier.


High Performance and Reliability with the JVM
---------------------------------------------

Many thousands of hours have been dedicated to making Java's Virtual
Machine the most robust, reliable, compatible, high-performance VM
available.


Reactive with Akka
------------------

Courier is built on Akka_, an incredibly powerful platform for
building event-driven applications.


Robust with ZooKeeper
---------------------

Apache's ZooKeeper_ is a battle-tested service for highly reliable,
distributed coordination. Additionally, Courier leverages the
BookKeeper_ sub-project to power its highly-available, redundant WAL_.

Both of these services are well-documented, highly scalable, and
simple to deploy in a variety of environments. Both are built for
different, complimentary, focused purposes meaning they have simple
and predictable performance characteristics across hardware
configurations.


.. _Akka: http://akka.io/
.. _ZooKeeper: http://zookeeper.apache.org/
.. _BookKeeper: http://zookeeper.apache.org/bookkeeper/
.. _WAL: http://en.wikipedia.org/wiki/Write-ahead_logging
