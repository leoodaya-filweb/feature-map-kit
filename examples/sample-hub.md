---
slug: hub-mailer
title: Mailer
kind: hub
domain: shared
area: shared
status: active
summary: Queued outbound mail with retries; dev traffic goes to the local mail trap
aliases: []
implicit: []
exposed_as: Mailer service, injected; Mailable classes under app/mail/
files:
  - app/services/Mailer.*
  - app/mail/
  - config/mail.*
tests:
  - tests/unit/MailerTest.*
data: [mail_outbox]
config: [mail.driver, mail.from, MAIL_TRAP_URL]
updated: 2026-10-15
---

# Mailer

## Summary
Every outbound email in the app goes through one service that queues a Mailable, retries delivery
three times with backoff, and records the outcome in mail_outbox. Features never talk to a transport
directly.

## Contract
- `Mailer::queue(Mailable $m): string` returns the outbox id; never throws on transport errors.
- `Mailer::status(string $id)` returns queued | sent | failed.
- A Mailable declares `subject()`, `to()`, `render()`; anything else is ignored.
- In the `dev` environment `mail.driver` is forced to the trap; production refuses the trap driver.

## Example use
`Mailer::queue(new InviteMail($invite))` — see app/services/InviteService.*

## Gotchas
- Retries are per outbox row; re-queuing the same Mailable creates a second row and a duplicate mail.
- The trap keeps only the last 200 messages; functional tests that send more lose the oldest.
- `mail.from` is read at queue time, not send time; changing it does not affect queued rows.

## Changelog
- 2026-10-15 (refactor, PR #238): transport calls moved out of features into Mailer. Test: MailerTest
