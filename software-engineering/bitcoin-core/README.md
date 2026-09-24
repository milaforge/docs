---
description: Understanding Bitcoin Core P2P by Building, Debugging, and Experimenting
icon: bitcoin-sign
---

# Bitcoin Core

### Mission

I want to understand _Bitcoin Core_'s peer-to-peer networking by working with the real system rather than only reading about it.

That means:

* building _Bitcoin Core_ from source;
* running and debugging `bitcoind`;
* tracing _P2P_ behavior through the implementation;
* inspecting peer connections, messages, and network state directly;
* designing small _regtest_ experiments;
* turning useful findings into reproducible tests and op

The goal is not to document all of Bitcoin Core. It is to understand the P2P subsystem well enough to answer:

```
What happened on the network?
Which component handled it?
How did the message move through the node?
What state changed?
What happens when a peer disconnects, stalls, or misbehaves?
What assumptions fail under interruption or partial failure?
```

### Foundations

{% columns %}
{% column %}
{% content-ref url="building-from-source.md" %}
[building-from-source.md](building-from-source.md)
{% endcontent-ref %}
{% endcolumn %}

{% column valign="middle" %}
{% content-ref url="debugging-bitcoin-core.md" %}
[debugging-bitcoin-core.md](debugging-bitcoin-core.md)
{% endcontent-ref %}
{% endcolumn %}
{% endcolumns %}

### Exploring Bitcoin Core P2P

Future work will follow questions that emerge while investigating the networking stack, including:

* _P2P_ architecture and major components
* inbound and outbound peer connections
* connection lifecycle and peer state
* message serialization, transport, and dispatch
* how received messages move through the codebase
* transaction and block propagation
* peer discovery and address management
* timeouts, disconnects, retries, and misbehaving peers
* network observability with logs, _GDB_, _USDT_, `tcpdump`, and related tools
* controlled _regtest_ experiments and failure injection

Each post should leave behind something another engineer can reproduce: exact commands, source paths, packet or log observations, breakpoints, tests, or other technical evidence.
