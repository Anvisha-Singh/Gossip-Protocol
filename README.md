# Gossip Protocol

A distributed systems simulation in **Pony** that models **Gossip** and **Push-Sum** convergence across multiple network topologies using the actor model. The project focuses on how information and aggregate state propagate through decentralized peer-to-peer systems, making it a strong systems-design demonstration of epidemic dissemination, fault-tolerant communication patterns, and convergence measurement.

## Why This Project Is Interesting

This repository is a compact but high-signal example of distributed systems thinking:

- it uses **Pony actors** to model each node as an isolated concurrent process
- it simulates **Gossip** for rumor dissemination
- it simulates **Push-Sum** for distributed aggregation and convergence
- it supports multiple **network topologies** to compare propagation behavior
- it measures **time to convergence**, which makes the project useful for algorithmic performance experiments

Conceptually, this project touches ideas that show up in real systems such as:

- **anti-entropy / epidemic replication**
- **peer-to-peer failure-tolerant dissemination**
- **eventual convergence across decentralized nodes**
- **state propagation strategies used in large distributed stores and cluster coordination systems**

It does **not** use Redis itself, but the underlying communication pattern is related to the kinds of decentralized propagation and cluster-health ideas that appear in modern distributed infrastructure.

## Implemented Algorithms

### Gossip

Each node repeatedly forwards a rumor to a randomly chosen neighbor until it has heard the rumor enough times to terminate. This models fast, decentralized message spread in a large system.

### Push-Sum

Each node maintains `(s, w)` values and exchanges partial state with neighbors. Convergence is detected when the ratio `s / w` stabilizes within a small threshold across consecutive rounds. This models distributed averaging and convergence detection without a central coordinator.

## Supported Topologies

The coordinator builds one of the following network structures:

- `full`
  Every node connects to every other node.
- `line`
  Each node connects to its immediate neighbor(s).
- `3D`
  Nodes are arranged in a 3D grid with local neighbor connectivity.
- `imp3D`
  A 3D grid plus one additional random long-range neighbor per node, which improves propagation similarly to a small-world shortcut.

These topologies make the project especially useful for interview discussions around:

- communication complexity
- convergence speed
- locality versus random shortcuts
- scalability tradeoffs in distributed overlays

## Architecture

The implementation lives almost entirely in [`main.pony`](/Users/anvishasingh/Downloads/Gossip-Protocol/main.pony).

Core actors:

- `Coordinator`
  Creates the node graph, tracks termination state, dispatches messages between nodes, and measures convergence time.
- `GossipWorker`
  Models a node participating in rumor spreading.
- `PushSumWorker`
  Models a node participating in Push-Sum convergence.
- `Main`
  Parses CLI arguments and starts the requested simulation.

## How It Works

1. The program creates `N` actors, one per simulated node.
2. The coordinator constructs the chosen topology as an adjacency list.
3. The simulation starts from a seed node.
4. Each actor sends messages to randomly selected neighbors according to the selected algorithm.
5. The coordinator marks nodes as terminated when they meet the stopping condition.
6. When all active participants have converged or terminated, the program prints the total runtime.

## Command-Line Interface

The program expects:

```bash
<numNodes> <topology> <algorithm>
```

Example:

```bash
./gossip-protocol 1000 full gossip
```

or

```bash
./gossip-protocol 1000 imp3D push-sum
```

## Build

This project is written in Pony, so the typical build tool is `ponyc`.

If `ponyc` is installed:

```bash
ponyc .
```

That produces an executable for the current platform.

## Run

After building:

```bash
./gossip-protocol 1000 full gossip
./gossip-protocol 1000 line gossip
./gossip-protocol 1000 3D push-sum
./gossip-protocol 1000 imp3D push-sum
```

The program outputs the total time taken for the network to converge or terminate.

## Interview Talking Points

This project gives you strong material for systems interviews because you can talk about:

- why the **actor model** is a natural fit for distributed node simulation
- how **topology choice** changes convergence characteristics
- why **Gossip** is good for simple dissemination but **Push-Sum** is useful for distributed aggregation
- how **imperfect 3D / small-world shortcuts** improve propagation speed
- how decentralized protocols trade exact coordination for scalability and resilience

A concise technical description would be:

> Built a distributed systems simulator in Pony using the actor model to implement Gossip and Push-Sum across full, line, 3D, and imperfect-3D topologies, measuring convergence behavior in decentralized networks.

## Files in the Repo

- main.pony
  Main simulation source.
- `pony`, `pony.exe`, `pony.lib`, `pony.pdb`, `pony.ilk`, `pony.exp`
  Compiled artifacts that appear to have been generated from prior builds.

## Notes

- The simulation starts from node `0`.
- The `3D`-style topologies assume the node count maps into a cube using the cube root logic in the coordinator.
- Push-Sum uses a threshold-based convergence rule over consecutive ratio changes.
- The repository is best presented as a **distributed algorithms / systems project**, not as a production networking stack.
