---
description: A minimal Fedora build setup for building Bitcoin Core from source
icon: hexagon-check
---

# Building from source

I want to build and debug _Bitcoin Core_ on a _Fedora_ Linux machine. Here is the setup I used.

### 1. Install the build dependencies

Install the tools required to compile _Bitcoin Core_, keep _wallet support_, and enable _USDT_ tracing:

```bash
sudo dnf install -y \
  git \
  gcc \
  gcc-c++ \
  cmake \
  make \
  python3 \
  boost-devel \
  sqlite-devel \
  systemtap-sdt-devel
```

The reason for some optional dependencies:

* `sqlite-devel`: _Bitcoin Core_'s wallet support is enabled. Descriptor wallets use SQLite.
* `systemtap-sdt-devel`: Required because I am compiling with _USDT_ tracepoints enabled.

I also installed a separate set of investigation tools:

```bash
sudo dnf install -y \
  gdb \
  perf \
  strace \
  bpftrace \
  tcpdump \
  jq \
  iproute \
  iproute-tc \
  clang \
  llvm
```

These are not required to compile the configuration below. They are there for later debugging, tracing, packet inspection, and experimentation.

### 2. Clone _Bitcoin Core_ and record the exact revision

```bash
git clone https://github.com/bitcoin/bitcoin.git
cd bitcoin
```

Before doing anything else, for reproducibility, I record the current source revision rather than saying only that it was tested against `master`:

```bash
git rev-parse HEAD
```

```
5c726f2058a4d5ac2c7d0dc8753d4ff125d03f15
```

I also record the basic environment:

```bash
cat /etc/fedora-release
uname -m
gcc --version | head -n1
cmake --version | head -n1
```

```
Fedora release 44 (Forty Four)
x86_64
gcc (GCC) 16.2.1 20260819 (Red Hat 16.2.1-2)
cmake version 4.3.0
```

### 3. Configure a development build

```bash
cmake -B build \
  -DBUILD_GUI=OFF \
  -DENABLE_IPC=OFF \
  -DWITH_USDT=ON \
  -DCMAKE_BUILD_TYPE=Debug
```

There are four deliberate choices here.

#### `BUILD_GUI=OFF`

I am interested in the node and its internals, not the Qt application.

This avoids building `bitcoin-qt` and avoids pulling the Qt GUI dependencies into this environment.

It does **not** disable `bitcoind`, `bitcoin-cli`, or wallet support.

#### `ENABLE_IPC=OFF`

_Bitcoin Core_ can also build its multiprocess architecture, including the supplemental `bitcoin-node` executable.

That requires [Cap'n Proto](https://capnproto.org/) (a data interchange format).

For now I want to study the conventional `bitcoind` process without adding another process boundary, so I disable _IPC_.

This also means I do not need the _Fedora_ `capnproto` development packages for this build.

#### `WITH_USDT=ON`

This enables _Bitcoin Core_'s _User-space Statically Defined Tracing_ probes.

It is why `systemtap-sdt-devel` is installed above.

The probes give tools such as `bpftrace` access to predefined instrumentation points inside _Bitcoin Core_ without requiring me to add temporary logging every time I want to observe something.

#### `CMAKE_BUILD_TYPE=Debug`

I want a build intended for source investigation rather than normal production operation.

In my configuration this resulted in C++ flags including:

```
-O0
-g3
-ftrapv
```

and _Bitcoin Core_ debug definitions including:

```
DEBUG
DEBUG_LOCKORDER
DEBUG_LOCKCONTENTION
RPC_DOC_CHECK
ABORT_ON_FAILED_ASSUME
```

`-O0` is very useful when stepping through code because the compiler does not aggressively transform the program through optimization.

`-g3` emits debugging information used by tools such as GDB.

_Bitcoin Core_'s Debug configuration also enables additional internal diagnostics such as _lock-order checking (_&#x70;ractices used to prevent deadlocks).

### 4. Check what CMake actually selected

At the end of configuration I expect the relevant part of the summary to look roughly like:

```
bitcoind ............................ ON
bitcoin-node (multiprocess) ......... OFF
bitcoin-qt (GUI) .................... OFF
bitcoin-cli ......................... ON
bitcoin-tx .......................... ON
bitcoin-util ........................ ON
bitcoin-wallet ...................... ON

wallet support ...................... ON
IPC ................................. OFF
USDT tracing ........................ ON

test_bitcoin ........................ ON

CMAKE_BUILD_TYPE .................... Debug
```

So far, _Bitcoin Core_ is not compiled or built yet.

_CMake_ has:

1. detected my compiler and platform;
2. located required libraries;
3. tested available compiler/linker features;
4. selected configuration options;
5. generated a build graph under `build/`.

Some individual _CMake_ feature probes can report `Failed` without indicating a failed configuration. They are often checking whether an optional compiler, architecture, or operating-system feature exists.

The configuration itself succeeded if _CMake_ reaches:

```
Configuring done
Generating done
Build files have been written to: .../build
```

CMake has now configured the project and **generated the build system under** `build/`.

### 5. Compile _Bitcoin Core_

Now compile the generated build:

```bash
cmake --build build -j"$(nproc)"
```

Remembe:

```
cmake -B build ...
        │
        └── configure and generate

cmake --build build
        │
        └── actually compile and link
```

After a successful build:

```bash
build/bin/bitcoind --version
```

should execute the newly built node and generate something like the following:

```
Bitcoin Core daemon version v32.99.0-5c726f2058a4 bitcoind
Copyright (C) 2009-2026 The Bitcoin Core developers

Please contribute if you find Bitcoin Core useful. Visit
<https://bitcoincore.org/> for further information about the software.
The source code is available from <https://github.com/bitcoin/bitcoin>.

This is experimental software.
Distributed under the MIT software license, see the accompanying file COPYING
or <https://opensource.org/license/MIT>
```

### 6. Run the unit tests

```bash
ctest --test-dir build -j"$(nproc)"
```

This is a separate validation boundary.

A successful compilation tells me that the source compiled and linked.

It does not tell me that the unit tests pass.

### 7. Run one functional P2P test

_Bitcoin Core_ also has _Python_ functional tests that start real `bitcoind` processes and exercise them through _RPC_ and _P2P_ interfaces.

As a small initial test:

```bash
build/test/functional/test_runner.py p2p_ping.py
```

`p2p_ping.py` is a useful first test because the environment I'm setting up is intended for later _P2P_ investigation.

This gives me four distinct pieces of evidence:

```
CMake configuration succeeded (compilation is possible)
        ↓
Bitcoin Core compiled (binaries generated)
        ↓
unit tests passed (logic is correct)
        ↓
a real functional P2P test passed (a sample scenario works end to end)
```

### Result

At this point I have a _Fedora_-native _Bitcoin Core_ development environment with:

* a Debug `bitcoind`;
* wallet support;
* no Qt GUI;
* no multiprocess IPC layer;
* _USDT tracepoints_;
* unit tests;
* the functional-test framework;
* debugging and Linux observability tools available for later experiments.

This is the foundation I wanted: a local _Bitcoin Core_ build that I can inspect, instrument, break, and test while learning how the system behaves first hand.

From here, the work moves from preparing the environment to **observing the running system**.

Next I will run `bitcoind` under a debugger, drive specific behavior through its RPC interface, and follow those operations through the source. Once a behavior is understood manually, I can encode the same experiment in _Bitcoin Core_'s functional-test framework and make it reproducible.
