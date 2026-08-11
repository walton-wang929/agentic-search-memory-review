# Agentic Search × Personalized Memory Review

[中文](./README.zh-CN.md) | English

A product and technical review of how Agentic Search systems use short-, medium-, and long-term user memory to personalize retrieval, ranking, synthesis, and actions.

The review covers Xiaohongshu, ByteDance, Tencent, Alibaba, Google, Meta, OpenAI, Anthropic, and other relevant players. Product facts, public technical signals, architectural inference, and unknowns are explicitly separated.

## Core thesis

Personalization is not “put chat history in the prompt.” It is a governed read/write system:

```text
request + session state
  → personalization-need routing
  → tiered memory retrieval
  → permission / freshness / conflict / sensitivity filtering
  → query rewriting + candidate retrieval
  → personalized ranking + synthesis
  → user-facing rationale
  → feedback, write-back, decay, or deletion
```

## Memory tiers

- **Short-term:** active conversation, constraints, viewed and rejected candidates.
- **Medium-term:** projects, trips, purchases, recurring tasks, and recent interests.
- **Long-term:** stable preferences, relationships, routines, and enduring goals.

Start with the [Chinese overview](./README.zh-CN.md), [memory lifecycle](./framework/memory-lifecycle.md), and [architecture map](./architecture/README.md).

## Research principles

1. Personalize only when it improves the user’s actual objective.
2. Treat memory as evidence, not unquestionable truth.
3. Separate inferred preferences from user-confirmed facts.
4. Make memory inspectable, correctable, deletable, and disableable.
5. Evaluate relevance gains together with filter bubbles, privacy, and stale-memory harm.

## Disclaimer

This repository is for product and technical research. Unless supported by cited first-party sources, architecture descriptions are hypotheses—not claims about private company implementations.
