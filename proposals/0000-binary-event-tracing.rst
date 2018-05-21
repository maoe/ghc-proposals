Binary user event logging
==============

.. proposal-number:: Leave blank. This will be filled in when the proposal is
                     accepted.
.. trac-ticket:: Leave blank. This will eventually be filled with the Trac
                 ticket number which will track the progress of the
                 implementation of the feature.
.. implemented:: Leave blank. This will be filled in with the first GHC version which
                 implements the described feature.
.. highlight:: haskell
.. header:: This proposal is `discussed at this pull request <https://github.com/ghc-proposals/ghc-proposals/pull/0>`_.
            **After creating the pull request, edit this file again, update the
            number in the link, and delete this bold sentence.**
.. sectnum::
.. contents::

This is a proposal that introduces a new primop called ``traceEventBinary#`` in GHC so that the user can log arbitrary binary data in a eventlog efficiently.

The type signature of the primop is:

.. code-block:: haskell

  traceEventBinary# :: Addr# -> Int# -> State# s -> State# s

Motivation
------------
Currently all user tracing functions in the event logging framework are limited to null-terminated strings.

.. code-block:: haskell

  traceEvent# :: Addr# -> State# s -> State# s
  traceMarker# :: Addr# -> State# s -> State# s

Both of above expect a poniter to a null-terminated string. If the user wants to log some application specific events using the event logging framework, s/he needs to encode his/her data in some way and null-terminate it. This is inconvenient and also inefficient.

Proposed Change Specification
-----------------------------
The ``traceEventBinary#`` primop has the following type:

.. code-block:: haskell

  traceEventBinary# :: Addr# -> Int# -> State# s -> State# s

The first argument of type ``Addr#`` is a pointer to the binary data being written to an eventlog. The second argument of type ``Int#`` is length of the data.

This function emits the binary data into an eventlog with a new event type called ``EVENT_USER_BINARY`` if user tracing is enabled.

Effect and Interactions
-----------------------
With the new ``traceEventBinary#`` primop, one can define a new tracing function that takes a ``ByteString`` in a library:

.. code-block:: haskell

  traceEventBinary :: ByteString -> a -> a
  traceEventBinaryIO :: ByteString -> IO ()

Similarly in the ``ghc-events`` library,
Discuss possibly contentious interactions with existing language or compiler features.

Costs and Drawbacks
-------------------
Give an estimate on development and maintenance costs. List how this effects learnability of the language for novice users. Define and list any remaining drawbacks that cannot be resolved.


Alternatives
------------
List existing alternatives to your proposed change as they currently exist and discuss why they are insufficient.


Unresolved questions
--------------------
Explicitly list any remaining issues that remain in the conceptual design and specification. Be upfront and trust that the community will help. Please do not list *implementation* issues.

Hopefully this section will be empty by the time the proposal is brought to the steering committee.


Implementation Plan
-------------------
I (Mitsutoshi Aoe) am interested in implementing the proposal.

There is a prototype implementation of this proposal at

- changes to GHC_
- library code (ghc-trace-events_)

.. _GHC: https://github.com/maoe/ghc/tree/traceEventBinary
.. _ghc-trace-events: https://github.com/maoe/ghc-trace-events/tree/feature/traceEventBinary
