# Multiplayer Networking Overview

This document describes how the Minecraft: Legacy Console Edition client organises its multiplayer
network communication stack and how packets flow between the socket layer and gameplay systems.

## Core connection lifecycle

### Construction and teardown
A `Connection` instance owns the client socket, stream wrappers, threading primitives, and worker
threads that service network I/O. The constructor initialises mutexes, queues, and the compression
context, wires buffered input/output streams, and launches dedicated read/write threads named after
connection type for profiling visibility.[^conn-init] Teardown cleans up by closing streams, waiting
for worker completion, and finalising the mutexes the object owns.[^conn-dtor]

### Read/write threads
`Connection::runRead` loops while the connection is marked running, repeatedly invoking
`readTick()` to drain the socket and signalling a per-thread event so that other systems can wake up
when new data arrives. `runWrite` mirrors this behaviour: it repeatedly invokes `writeTick()` until
no data remains to flush, using an event to block efficiently between bursts of outbound traffic and
forcing the buffered output stream to flush before the loop sleeps.[^conn-threads]

### Outgoing queue management
Outbound packets are fed into `mOutgoingQueue` (for immediate send) or `mSlowOutgoingQueue` (for
rate-limited traffic). `send()` timestamps the connection for keep-alive scheduling, tracks the
estimated byte budget, and optionally deduplicates map updates before queueing. `queueSend()` is a
thin wrapper that always targets the slow queue.[^conn-send] `writeTick()` pulls from these queues,
serialises packets using `Packet::writePacket`, updates per-packet accounting arrays, and handles
rate limiting for the slow queue by honouring a configurable delay and optional fake lag window.
Packets flagged for delayed delivery are written into a secondary buffer before being flushed out of
band.[^conn-write]

### Incoming processing and keep-alives
`readTick()` uses `Packet::readPacket` to decode the next message from the wire. When a packet is
produced it is enqueued for later handling and its byte contribution is tracked.[^conn-read]
`Connection::tick()` enforces flow control (closing the link on protocol violations or timeouts),
schedules automatic keep-alive packets if no traffic was sent during the last second, reorders certain
player action packets, and dispatches up to 1,000 enqueued packets per frame through the registered
`PacketListener`. Once the outgoing queue has been flushed, additional disconnect bookkeeping ensures
that the listener receives final disconnect callbacks.[^conn-tick]

## Packet registration and metadata

### Packet identifiers
`EPacketType` enumerates every packet understood by the client, assigning stable numeric identifiers
used on the wire. The naming convention encodes the direction (clientbound vs. serverbound) and the
feature domain for each packet.[^packet-type]

### Factory registration
`Packet::staticCtor()` registers every known packet class with a creation factory and a set of metadata
flags via the `Packet::map` helper. The helper stores the factory in `sPacketsMap` and records boolean
attributes in secondary sets. The registration table covers the entire handshake, gameplay, world
streaming, inventory, mini-game, and debug packet surface exposed by the console edition client.[^packet-map]

### Packet utilities
The base `Packet` class stores creation timestamps (used for artificial lag) and exposes helpers for
serialising UTF-16 strings. Default implementations of behaviour such as estimated packet size,
invalidations, or asynchronous handling are stubs that derived packet types override where necessary.[^packet-base]

## Packet consumption interface

`PacketListener` defines the callback surface that gameplay systems implement to consume network
traffic. For each registered packet type there is a corresponding `handle…` method; the default
implementation simply reports the packet as unhandled so concrete listeners can opt into specific
messages. The interface also exposes disconnect notifications and a capability probe for asynchronous
handling.[^packet-listener]

## Further reading

* `packet-ids.md` — exhaustive packet ID catalogue grouped by feature domain.

[^conn-init]: `src/Minecraft.Client/net/minecraft/network/Connection.cpp`, lines 33-132.
[^conn-dtor]: `src/Minecraft.Client/net/minecraft/network/Connection.cpp`, lines 52-89.
[^conn-threads]: `src/Minecraft.Client/net/minecraft/network/Connection.cpp`, lines 134-200.
[^conn-send]: `src/Minecraft.Client/net/minecraft/network/Connection.cpp`, lines 208-240.
[^conn-write]: `src/Minecraft.Client/net/minecraft/network/Connection.cpp`, lines 242-298.
[^conn-read]: `src/Minecraft.Client/net/minecraft/network/Connection.cpp`, lines 305-320.
[^conn-tick]: `src/Minecraft.Client/net/minecraft/network/Connection.cpp`, lines 357-439.
[^packet-type]: `src/Minecraft.Client/net/minecraft/network/PacketType.h`, lines 3-127.
[^packet-map]: `src/Minecraft.Client/net/minecraft/network/protocol/Packet.cpp`, lines 176-374.
[^packet-base]: `src/Minecraft.Client/net/minecraft/network/protocol/Packet.cpp`, lines 129-174.
[^packet-listener]: `src/Minecraft.Client/net/minecraft/network/PacketListener.h`, lines 1-110.
