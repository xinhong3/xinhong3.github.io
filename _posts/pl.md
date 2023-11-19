---
layout: post
title: TLA+ for dummies
author: 'Xin Hong'
excerpt_separator: <!--more-->
tags: TLA+
---

An TLA+ Guide from dummies (me) to people who have no context on this.

What this is article is:
- quick intro and roadmap for people who want to experience TLA+ but have no previous experence.
- pointers to TLA+ learning resources

What this is article is <u>NOT</u>:
- Complete guide from the basic to the advanced

Without further todo, let's explore TLA+!

If you prefer just jump to the hands-on part, jump to [this](something)



## First course & set up in TLA+
You can either do TLA+ in <u>TLA toolbox</u> or using VS Code with the offical TLA+ plugin.


TLA+ is a great starting point, provides an IDE with syntax checks and an TLA+ engine. That's could be all you need to run TLA+, no plugins, no `brew install` of anything. Also it gives a couple of examples (while some might be a little complex for beginers). 

Support for TLA+ could be found here:

https://lamport.azurewebsites.net/tla/toolbox.html


From my experience, VS Code is more user friendly and easy to grasp especially for those who are already familiar with the editor.

This will be based on VS Code.

### What does a TLA+ "project" looks like?

Just like any other launguages, there's basically two types of files in TLA+ world: <u>.tla</u> where the model is specified, and <u>.cfg</u>.

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

This seems a bit complicated, let's stick with the barebone structure -- just two files: `model.tla` and `model.cfg`. 

With the .tla file, you want to put all the logic in there -- that means varibles, initial state (often represented with `Init`), operations (`DoA == <...>`, `DoB == <...>`), next step (often represented with `Next`), and invariants.

For example, an simple starting point might be like this:

```
<tla file>
```

with the config file `model.cfg`, we are telling TLA+ **what to check**, for example, the following `.cfg` file means this:

```
<cfg file>
```

With both `.tla` and `.cfg`, you could let the TLA+ examine your model, it will go though possible states and if there's a violation to what is defined in the config file (either the invariants, or some assume function failed), it will give a trace.