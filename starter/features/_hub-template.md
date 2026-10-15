---
slug: hub-<name>
title: <Human Title>
kind: hub
domain: shared
area: shared
status: draft
summary: <one line, <= 90 chars: the contract in a sentence; copied verbatim into the README row>
aliases: []
implicit: []
exposed_as: <how callers reach it: import path, injection token, global, base class>
files:
  - <repo-root-relative path>
tests:
  - <test file path>
data: []
config: []
updated: YYYY-MM-DD
---

# <Human Title>

## Summary
<!-- 2-3 sentences: what it provides, which features rely on it, what breaks when it changes. -->

## Contract
<!-- The public surface callers depend on: functions/methods by name, inputs, outputs, invariants.
     Implicit hubs: say what every feature of the listed areas inherits without declaring it. -->

## Example use
<!-- One minimal, real call site: "<Symbol> — see <path>". -->

## Gotchas
<!-- <= 8 bullets. Hidden coupling, ordering, environment differences, the "there are two of these" traps. -->

## Changelog
<!-- Newest first, <= 10 lines: - YYYY-MM-DD (kind, ref): symptom -> cause -> change. Test: Symbol -->
