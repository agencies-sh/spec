---
schema: agentagencies/v1
kind: company
slug: hello-world
name: Hello World
description: |
  The smallest complete agentagencies/v1 agency. Two agents (planner +
  doer), one project, two linked tasks, one SKILL placeholder.
  Demonstrates every manifest type the spec defines.
version: 0.1.0
license: MIT
authors:
  - name: agencies-sh
    url: https://github.com/agencies-sh
tags:
  - example
  - demo
metadata:
  language: en
teams:
  - home
---

# Hello World

The smallest agentagencies/v1 agency that exercises all seven manifest types.

## How it operates

`planner` proposes a greeting. `doer` writes it. The two-step handoff is
modeled as two tasks with `dependsOn`.

## Anti-scope

This agency does nothing useful. It exists to be readable end-to-end in
under 5 minutes.
