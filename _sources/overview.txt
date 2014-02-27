Overview
========

This document provides a high-level overview of the two primary
components in a Courier network: *Publishers* and *Subscribers*.


Publishers
----------

.. note::

   It's easy enough to publish messages to a Courier network
   anonymously <link>, but Publishers who register themselves are far
   more useful; only they are discussed in this section.


Registering a Publisher allows Courier to track it throughout the
system—informing Subscribers of the sender, providing statistics on
Publisher activity, and exposing an audit trail of events.

Publishers are identified by a string composed of two parts, separated
by a colon (:)

#. A GUID string as described by UUID type 4 and
#. An optional, short alphanumeric string describing the publisher.
   This component is designed to provide a human-memorable
   representation of the Publisher, but does not uniquely identify it
   within the system.

An example is ``7916887e-186a-4a4e-b64c-51370d5db3d8:users-service``.

.. note::

   Both variations of the ID are valid; you do not *need* to include
   the label component in headers, parameters, etc.


Registration
~~~~~~~~~~~~

The most straight-forward way to register a Publish is directly via
the ``/register`` endpoint:

.. http:post:: /courier/publisher

   Register a Publisher

   **Response**:

   .. sourcecode:: http

      HTTP/1.1 303 See Other
      X-Courier-Publisher-Id: 7916887e-186a-4a4e-b64c-51370d5db3d8:users-service
      Location: https://example.com/courier/publisher/7916887e-186a-4a4e-b64c-51370d5db3d8:users-service/

   :form label: Optional human-readable label for the Publisher; may
                not exceed 50 characters (it will be silently
                truncated in this case)
   :reqheader Content-Type: Must be
                            *application/x-www-form-urlencoded* or
                            *multipart/form-data*.

This endpoint will create the Publisher and respond with the location
of the new Publisher's metadata.

JIT Registration
++++++++++++++++

Most Courier endpoints also support so-called "Just-In-Time"
registration, which will create a new Publisher as part of an
operation, if and only if:

#. No *X-Courier-Publisher-Id* header is present in the request and,
#. The query parameter ``jit-register=true`` is present in the request.

For example:

.. http:post:: /courier/publish/?jit-register=true

   JIT registration via event publishing.

   **Request**:

   .. sourcecode:: http

      POST /courier/publish/?jit-register=true HTTP/1.1
      Content-type: application/json

      [... event data ...]

   **Response**:

   .. sourcecode:: http

      HTTP/1.1 201 Created
      X-Courier-Publisher-Id: 7916887e-186a-4a4e-b64c-51370d5db3d8
      [... other headers for the endpoint ...]

For simplicity and ease of use, this method does not handle
labeling. The response will be as expected for the particular endpoint
you use; the only difference will be the addition of the
*X-Courier-Publisher-Id* header, which should be stored by the client.

Identification
~~~~~~~~~~~~~~

Whenever making a publish-based request to Courier, simply include
*X-Courier-Publisher-Id* to ensure your Publishers are tracked and
audited. Any requests *not* including this header will be attributed
to the special Publisher ID, *anonymous*.

.. note::

   Detailed documentation of Publish/Publisher endpoints is available
   in :doc:`pub`.


Subscribers
-----------

The consumer side of the producer/consumer system, Subscribers are
similar to Producers in many ways.

* Subscribers **must** be registered; anonymous subscriptions are not possible.
* Subscribers identify themselves via the *X-Courier-Subscriber-Id* header.
* Subscriber activity is tracked; you can see how long a Subscriber
  has been around, their average latency, error percentage, and more.

