---
layout: post
title: TLA+ for dummies
author: 'Xin Hong'
excerpt_separator: <!--more-->
tags: TLA+
---
## Introduction

An TLA+ Guide from dummies (me) to people who have no context on this.

What this is article is:
- quick intro and roadmap for people who want to experience TLA+ but have no previous experence.
- pointers to TLA+ learning resources.

What this is article is <u>NOT</u>:
- A complete guide from basic to advanced TLA+.

After opening the [TLA+ wiki](https://learntla.com/core/tla.html), let's explore TLA+!

## First course & set up in TLA+
### TLA+ Toolbox vs. VS Code
You can use TLA+ in two main environments:
1. **TLA Toolbox**: An IDE with syntax checks and a TLA+ engine, suitable for running TLA+ without additional plugins. More complex examples are available for beginners. [TLA Toolbox Support](https://lamport.azurewebsites.net/tla/toolbox.html).
2. **VS Code with the Official TLA+ Plugin**: User-friendly, especially for those already familiar with VS Code.

From my experience, VS Code is more user friendly and easy to grasp especially for those who are already familiar with the editor.

This will be based on VS Code.

### What does a TLA+ "project" looks like?

Just like any other launguages, there's basically two types of files:
- `.tla` files for model specification.
- `.cfg` files for model configuration.

Chatgpt was kind enough to print out this tree structure for me:

```
project_name/
│
├── src/                    # Main directory for all TLA+ modules
│   ├── main_module.tla     # Main TLA+ module
│   ├── helper_module1.tla  # Additional TLA+ modules
│   └── helper_module2.tla
│
├── model/                  # Directory for TLC model configurations and outputs
│   ├── main_module.cfg     # Configuration for the TLC model checker
│   ├── states.dot          # Generated state graph (if requested)
│   └── other_outputs/      # Other output files, traces, logs, etc. 
│
└── docs/                   # Optional: documentation, notes, etc.
    ├── notes.md
    └── ...
```

For simplicity, we will focus on a basic structure with just `model.tla` and `model.cfg`.

### The model file (.tla)

The `.tla` file contains all the logic: variables, initial state (`Init`), operations (e.g., `DoA == <...>`), next step (`Next`), and invariants.

For example, an simple starting point might be like this (reference [this wiki](https://en.wikipedia.org/wiki/Wolf,_goat_and_cabbage_problem) for the background of this puzzle):

https://github.com/xinhong3/tla_plus/blob/main/goat/goat.tla

This file defines our model, including variables (boat, wolf, cabbage, etc.), the state of the system (`var`), and operations (`MOVE(item)`, `MoveBoat(b)`).

The `NEXT` definition specifies possible state transitions, and `Spec` is the temporal property to run.

Writing `Spec` is crusial as one needs to understand the system behavior to be able to write the correct `Spec`. And something that confused me, as a beginner, is the box action syntax, namely, the meaning of `<>`, `[]`, `<>[]`, and `[]<>`.

#### Cheat sheet for <> and []

You can check the TLA+ tutorial for a detailed description of those, but here's a quick cheat sheet. Let `P` be a formula -- meaning `P` is either true or false in a given state.

- `<>P`: Eventually, `P` will become true. Mathematically, it means $$ \exists i \in \mathbb{N} \text{ such that } P \text{ is true for } s_i $$

- `[]P`: `P` is always true. This translates to:

$$ \forall i \in \mathbb{N}, P \text{ is true for } s_i$$

- `<>[]P`: `P` is **evetually always true**. Meaning there exists some number $n$ after which $P$ will always be true for $s_i$.

$$ \exists n \in \mathbb{N}, \forall i \geq n, P \text{ is true for } s_i$$

- `[]<>P`: `P` is **true for infinite many times**. Let $ N = \{n_i\}$ denotes a sequence in $\mathbb{N}$. Then this translates to:

$$ \exists N \subseteq \mathbb{N}, \forall i \in \mathbb{N}, P(s_{n_i}) \text{ is true} $$


An example will be the `Termination` formula [here](https://github.com/xinhong3/tla_plus/blob/main/goat/goat.tla#L62). We are saying that `Termination` will eventually always be true -- meaning after a certain point, it will always be true, which matches our intention of naming it with `Termination`.

### The config file (.cfg)

With the config file `model.cfg` ([github link](https://github.com/xinhong3/tla_plus/blob/main/goat/goat.cfg)), we are telling TLA+ **what to check**, for example, the following `.cfg` file means this:

```
\* CONSTANT declarations
CONSTANT ITEMS = {"WOLF", "GOAT", "CABBAGE"}
\* SPECIFICATION definition
SPECIFICATION
Spec
```

1. We defines ITEMS as wolf, goat, and cabbage, this will later be passed when the model is runing.
2. We are point the SPECIFICATION to `Spec` we defined in the `.tla` file. The naming here doesn't matter, just a convention to name it as `Spec`.

### Finally

With both `.tla` and `.cfg`, you could let the TLA+ examine your model, it will go though possible states and if there's a violation to what is defined in the config file (either the invariants, or some assume function failed), it will give a trace.


## References
- [Learning TLA+ from PlusCal](https://learntla.com/core/tla.html#learning-from-pluscal)
- [Core concepts in TLA+](https://learntla.com/core/tla.html)