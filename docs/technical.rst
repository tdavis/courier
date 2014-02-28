Technical Overview
==================

This document provides a technical overview of Courier's internal operation.


Message Pipeline
----------------

Let's first cover how a message flows through the system.


Message Receipt
~~~~~~~~~~~~~~~

A message is received by Courier and written to the WAL. This step
must complete successfully before a response is sent to the client.

.. digraph:: receipt

   graph [compound=true];
   rankdir="LR";
   Client -> HTTP;
   subgraph cluster0 {
        label="Receipt Transaction";
        style=filled;
        color=gainsboro;
        subgraph cluster1 {
            style=filled;
            color="#2980B9";
            fontcolor=white;
            label="WAL Replication";
            node[color=white,style=filled];
            DiskN;
            dots [label="...",shape="plaintext",height=0,fixedsize=true,style=empty];
            Disk1;
        }
        HTTP -> WAL;
        WAL -> dots [lhead=cluster1];
  }
  HTTP -> Client;

The WAL stores the actual message to disk redundantly. Each message
type (or "event name") is saved to its own log file. Periodically the
log file is closed for writing, meaning it's time to process the
messages.


Publish to Subscribers
~~~~~~~~~~~~~~~~~~~~~~

Outstanding messages will be pushed to Subscribers (or stored
elsewhere, in the case of Pull-based subscriptions)—concurrently to as
many as we can without overloading the WAL clients.

.. digraph:: tosubs

   graph [compound=true];
   rankdir="LR";
   subgraph cluster0 {
       style=filled;
       color="#2980B9";
       fontcolor=white;
       label="WAL Client Pool";
       node[color=white,style=filled];
       WALClient1;
       waldots [label="...",shape="plaintext",height=0,fixedsize=true,style=empty];
       WALClientN;
   }
   subgraph cluster1 {
       style=filled;
       color="#2980B9";
       fontcolor=white;
       label="Subscriber Workers";
       node[color=white,style=filled];
       Worker1 [label="Worker 1"];
       dots [label="...",shape="plaintext",height=0,fixedsize=true,style=empty];
       WorkerN [label="Worker N"];
   }

   WALClient1 -> Worker1 [lhead=cluster1,ltail=cluster0,minlen=2,arrowhead=inv];
   waldots -> dots [lhead=cluster1,ltail=cluster0,minlen=2,arrowhead=inv];
   WALClientN -> WorkerN [lhead=cluster1,ltail=cluster0,minlen=2,arrowhead=inv];

   sub [label="(Push) Subscriber 1"]

   Worker1 -> sub;
   Worker1 -> sub;
   Worker1 -> sub;
   Worker1 -> sub;

  }

Each active Subscriber has their own personal "Worker". This is just
a more recognizable name for an Actor_—a pseudo-process that deals
solely with a given Subscriber. (Responses are automatically routed
to the proper Worker by way of the :term:`Subscriber ID`.)

The Worker's primary goal is to shed outstanding messages as quickly
as possible, whether that means forwarding them along to the
Subscriber or moving them to non-ephemeral storage.

.. note::

   By default, Courier will not wait for a response from "Message 1"
   before sending "Message 2", and so on. For :term:`Pull`
   subscriptions, it will attempt to persist outstanding messages
   asynchronously if supported by the storage system. Rate limits may
   be set for these operations at any time on a per-Subscriber basis.

   Ordered delivery for both :term:`Push` and :term:`Pull` is
   supported, but off by default.


.. _Actor: http://en.wikipedia.org/wiki/Actor_model
