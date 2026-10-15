---
slug: invite-user
title: Invite User
kind: feature
domain: accounts
area: web
status: active
summary: Team owners email an invite; the link creates a membership on acceptance
aliases: []
entry:
  - POST /teams/{id}/invites
  - GET /invites/{token}
  - cli invites:expire
files:
  - app/controllers/InviteController.*
  - app/services/InviteService.*
  - app/models/Invite.*
  - app/mail/InviteMail.*
  - app/console/ExpireInvitesCommand.*
  - app/views/invites/
tests:
  - tests/functional/InviteFlowTest.*
  - tests/unit/InviteServiceTest.*
data: [invites, memberships]
config: [INVITE_TTL_HOURS, mail.driver]
connections:
  - team-members: an accepted invite creates a memberships row through MembershipService::add()
  - auth-signup: GET /invites/{token} for an unknown email redirects to /signup?invite=<token>
hubs:
  - hub-mailer: sends InviteMail; delivery retries and the dev mail trap are the hub's contract
updated: 2026-10-15
---

# Invite User

## Summary
A team owner enters an email address; the app stores an invite with a one-time token and emails a
link. Opening the link while signed in adds the user to the team; opening it signed out sends them
through signup with the token preserved. A nightly command expires unused invites.

## Example use
- UI: http://localhost:8080/teams/1 → Members → Invite → enter an address → the mail trap at
  http://localhost:8025 shows the message → open the link → the member appears in the team list.
- CLI: `./bin/app invites:expire` (prints the number of invites expired).
- Tests: `make test tests/functional/InviteFlowTest tests/unit/InviteServiceTest`

## Entry points
| Entry | Handler | Kind / access | Note |
|---|---|---|---|
| POST /teams/{id}/invites | InviteController::store | http-route, team owner | validates email, rate-limited 20/hour per team |
| GET /invites/{token} | InviteController::accept | http-route, any | signed-out users are redirected to signup |
| invites:expire | ExpireInvitesCommand::handle | cli-command | scheduled nightly |

## Chain
- Controller: InviteController (store, accept) → InviteService (create, accept, expire)
- Models: Invite (token, email, team_id, expires_at, accepted_at), Membership via team-members
- Mail: InviteMail (queued through hub-mailer)
- Views: app/views/invites/ (form partial, accepted page)
- Config: INVITE_TTL_HOURS (default 72), mail.driver

## Flow
1. InviteController::store validates the address and calls InviteService::create.
2. InviteService::create writes the invites row with a random token and expires_at = now + INVITE_TTL_HOURS.
3. InviteMail is queued; hub-mailer delivers it.
4. InviteController::accept loads the invite by token and rejects expired or accepted ones.
5. Signed in: InviteService::accept calls MembershipService::add and marks accepted_at.
6. Signed out: redirect to /signup?invite=<token>; auth-signup re-enters step 5 after registration.
7. ExpireInvitesCommand::handle deletes invites past expires_at that were never accepted.

## Gotchas
- The token is single-use: a second visit after acceptance shows "already accepted", not the team.
- Rate limiting is per team, not per user; an owner on two teams gets two budgets.
- Changing INVITE_TTL_HOURS does not touch existing invites; expires_at is stored at creation.
- KNOWN BUG (#231): an invite to an address with uppercase letters is not matched at signup.

## Tests
- InviteFlowTest covers store → accept for signed-in and signed-out users and the expired path.
- InviteServiceTest covers token generation, TTL and double acceptance.
- Gap: invites:expire has no test; the nightly schedule is only verified manually.

## Changelog
- 2026-10-15 (fix, PR #240): expired invites still accepted -> accept() compared dates as strings -> compare timestamps. Test: InviteServiceTest::rejectsExpired
- 2026-10-01 (feat, PR #228): invite flow introduced. Test: InviteFlowTest
