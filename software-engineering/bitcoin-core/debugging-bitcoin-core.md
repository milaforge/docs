---
description: Debugging Bitcoin Core on Fedora with VS Code and GDB
icon: butterfly
---

# Debugging ‌Bitcoin Core

After [setting up the foundation](building-from-source.md), I now have a Debug build of _Bitcoin Core_ on _Fedora_. The next step is to run the _node_ under a debugger, send it a real _RPC_ request, and follow that request through the source.

The setup I want is:

```
bitcoin-cli
    │
    │ JSON-RPC
    ▼
 bitcoind
    │
    └── running under GDB
            │
            └── controlled from VS Code
```

`bitcoin-cli` gives me a precise external stimulus. The debugger lets me observe what _Bitcoin Core_ does internally in response.

### 1. Enable C++ debugging in VS Code

[VS Code](https://share.google/aimode/L89UisZmQ6ml64HQh) uses its C/C++ extension to provide the `cppdbg` debugger type and connect to _GDB_ on Linux.

Install it:

```bash
code --install-extension ms-vscode.cpptools
```

Verify GDB:

```bash
gdb --version
```

If it is missing:

```bash
sudo dnf install -y gdb
```

### 2. Create a disposable regtest datadir

From the Bitcoin Core repository:

```bash
mkdir -p regtest-node
```

I use _regtest_ because the _node_ runs entirely locally and blocks can be created on demand. _Bitcoin Core_'s own developer notes recommend _regtest_ for experiments that can run on one machine.

### 3. Configure VS Code to launch `bitcoind`

Create:

```
.vscode/launch.json
```

with:

```json
{
  "version": "0.2.0",
  "configurations": [
    {
      "name": "Bitcoin Core: regtest",
      "type": "cppdbg",
      "request": "launch",
      "program": "${workspaceFolder}/build/bin/bitcoind",
      "args": [
        "-regtest",
        "-datadir=${workspaceFolder}/regtest-node",
        "-printtoconsole=1"
      ],
      "cwd": "${workspaceFolder}",
      "stopAtEntry": false,
      "MIMode": "gdb",
      "miDebuggerPath": "/usr/bin/gdb",
      "externalConsole": false,
      "setupCommands": [
        {
          "description": "Enable GDB pretty printing",
          "text": "-enable-pretty-printing",
          "ignoreFailures": true
        }
      ]
    }
  ]
}
```

The important fields are:

```
program
```

The executable being debugged: my locally built `bitcoind`.

```
args
```

The same arguments I would normally pass from the shell.

```
MIMode: gdb
```

VS Code controls _GDB_ rather than debugging the process itself.

```
stopAtEntry: false
```

_Bitcoin Core_ starts normally until it reaches one of my breakpoints.

VS Code's C++ debugger supports breakpoints, stepping, watches, locals, threads, and call-stack inspection through this setup.

### 4. Start Bitcoin Core under the debugger

In VS Code press `F5`:

```
Run and Debug
→ Bitcoin Core: regtest
→ F5
```

`bitcoind` now runs as a normal _regtest node_, except _GDB_ owns the process.

I deliberately do **not** use `-daemon`. A daemon would detach from the process I am trying to debug.

### 5. Find one RPC to trace

Start with a read-only _RPC_:

```bash
rg -n "getblockchaininfo" src
```

The implementation is in _Bitcoin Core_'s blockchain RPC code:

{% @github-files/github-code-block url="https://github.com/bitcoin/bitcoin/blob/5c726f2058a4d5ac2c7d0dc8753d4ff125d03f15/src/rpc/blockchain.cpp#L1448" %}

Rather than trying to understand the entire codebase, I use one external operation as the entry point:

```
getblockchaininfo
        ↓
RPC dispatch
        ↓
handler
        ↓
chain state
        ↓
JSON result
```

I set a breakpoint inside the `getblockchaininfo` request handler.

The useful breakpoint is inside the code that handles the request, not merely on code used to register the RPC during startup.

### 6. Trigger the breakpoint from another terminal

Leave `bitcoind` running under VS Code.

From another terminal:

```bash
build/bin/bitcoin-cli \
  -regtest \
  -datadir="$PWD/regtest-node" \
  -rpcwait \
  getblockchaininfo
```

The RPC reaches `bitcoind`, and execution should stop at the breakpoint.

Now I can inspect:

* local variables;
* function arguments;
* the call stack;
* active threads;
* referenced objects;
* the source location that produced the result.

And I can move through the code with:

```
F10        step over
F11        step into
Shift+F11  step out
F5         continue
```

### 7. Following the behavior, not the whole codebase

The goal is not to single-step through Bitcoin Core from `main()`.

Instead, now I have the ability to trace a specific behavior:

```
external request
      ↓
parsing / dispatch
      ↓
validation
      ↓
state access or mutation
      ↓
persistence boundary, if any
      ↓
response
```

For `getblockchaininfo`, the interesting question might simply be:

> Where does the `"blocks"` value returned by the RPC actually come from?

That question gives me a path through the implementation without requiring me to understand unrelated subsystems.

Once that workflow is familiar, I can move to operations that change state:

```
createwallet
getnewaddress
generatetoaddress
sendtoaddress
```

Those let me investigate wallet persistence, descriptor derivation, _UTXOs_, transaction creation, and eventually mempool and P2P behavior.

### 8. Know when _not_ to use a breakpoint

_Bitcoin Core_ is heavily multithreaded.

When _GDB_ stops at a breakpoint, execution timing changes. That makes breakpoints a poor tool for some questions involving:

```
timeouts
thread scheduling
lock contention
peer timing
race conditions
performance
```

For those cases I can use the other observability tools from the build environment:

```
debug.log
USDT / bpftrace
perf
strace
tcpdump
```

A debugger is strongest when the question is:

> What code path produced this state?

Tracing and logging are stronger when the question is:

> What happened over time without stopping the process?

### Result

I can now run the real `bitcoind` process under _GDB_, interact with it normally through _RPC_, and stop inside _Bitcoin Core_ at the exact code responsible for the behavior I am observing.

That gives me a practical experimentation loop:

```
form a question
      ↓
set a breakpoint
      ↓
trigger one operation through RPC
      ↓
inspect the implementation
      ↓
check the resulting node state
      ↓
repeat
```

The tools serve different purposes:

```
bitcoin-cli       controlled stimulus
VS Code / GDB     implementation inspection
functional tests  reproducibility and assertions
```

The development environment is now ready for source-level experimentation with Bitcoin Core.
