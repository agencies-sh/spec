---
name: greeting
description: |
  Compose a context-appropriate greeting. Demo SKILL — only structure
  matters. The actual prompt body is intentionally minimal.

  Triggers: greet, hello, welcome
includes: []
---

# Greeting

This is a placeholder SKILL.md so the hello-world example has a complete
canonical layout. Real SKILL.md packages live elsewhere — see
[Anthropic Skills](https://github.com/agentskills/agentskills) for the
authoritative format.

## Inputs

- `audience` — who is being greeted
- `tone` — formal / friendly / playful
- `length` — short / medium / long

## Behavior

Compose a single greeting matching the inputs. Return the greeting text
only. No commentary.
