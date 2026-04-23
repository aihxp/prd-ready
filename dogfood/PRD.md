# Mirror Box PRD (dogfood for prd-ready v1.0.0)

**Tier:** Spec (Tier 2)
**State:** Living
**Version:** 1.0
**Last updated:** 2026-04-23
**Owner:** h (dogfood)

This is a retroactive PRD written using prd-ready v1.0.0 for the toy Node + Fly.io + Honeycomb traced web service used as the shipping-tier dogfood target across deploy-ready, observe-ready, and launch-ready. Purpose: validate that prd-ready produces an artifact consumable by stack-ready (live) without extra interrogation.

## Changelog

- **2026-04-23** initial draft from prd-ready v1.0.0. Mode: A (Greenfield product, retroactive).

## Open questions

| ID | Question | Owner | Due | Blocks |
|---|---|---|---|---|
| OQ-01 | Do we add per-user rate limiting before the public launch? | h | 2026-05-01 | Tier 3 |

## Mode and context

**Mode:** A (Greenfield product, retroactive). The service is already running; the PRD is written after the fact to exercise the skill.
**Context:** "Mirror Box" is a single-endpoint HTTP service that accepts a POST with a JSON body and returns the body echoed back with request metadata (trace ID, upstream IP, server version). Used as a deploy + observe + launch target.

## Problem

**Friction.** Developers teaching or learning observability-wiring (OTel, SLO authorship, incident-response practice) need a tiny, trace-instrumented service they can poke at without building one. Every observability workshop teacher starts by writing a throwaway "hello world" with OTel wired in; the throwaway is rebuilt from scratch across tutorials, inconsistently instrumented, and never hardened for long-running public use.

**Who.** Observability educators and self-directed learners working through an SLO / tracing / runbook practice who do not want to burn 2-4 hours building yet another traced hello-world before getting to the actual lesson.

**Workaround.** Today, learners copy a snippet from an OTel getting-started guide, paste it into a scratch Express app, run locally, and struggle because local-only tracing doesn't exercise the reality of a deployed service.

## Target user

### Primary: Observability educator / self-directed learner

- **Role:** Engineer learning or teaching OTel, SLOs, and incident response; mid-career, comfortable with Node and a cloud CLI.
- **Context:** Running a workshop or self-guided study; needs a live traced target to exercise against.
- **Constraint:** Free-tier or low-cost; deployable in under 10 minutes; visible on the public internet so real request traces can be studied.
- **Workaround:** Hand-roll a trivial Express app per lesson; skip observability exercises that require a deployed target.
- **Research:** No formal interviews; drawn from dogfood experience across deploy-ready, observe-ready, launch-ready authoring (2026-01 through 2026-04).

## Success criteria

### Leading

- **Time-to-first-trace.** Under 10 minutes from clone to first trace visible in Honeycomb. Measured via workshop participant self-report.

### Lagging

- **Sibling-skill reuse.** At least 3 of the 4 live shipping-tier skills (deploy-ready, observe-ready, launch-ready) cite Mirror Box as a dogfood example in their published artifacts by 90 days post-open-source. Measured via README grep of each sibling repo.

### Counter-metric

- **Survivability.** Service uptime >=99% month-over-month on free Fly.io tier (no paid upgrade to keep running).

## Appetite

Big batch: 6 engineer-weeks. Past-tense; the service shipped in roughly that window.

## Functional requirements

### R-01 (Must): Echo endpoint

Users can POST JSON to `/echo` and receive the body echoed with request metadata (trace ID, upstream IP, server version, timestamp).

**Acceptance criteria:**
- Given a POST to `/echo` with `{"k":"v"}`, when the server responds, then the response body includes `{"echo":{"k":"v"},"trace_id":"<otel-trace-id>","server_version":"<semver>","received_at":"<iso8601>"}`.
- Given a POST with a non-JSON body, when the server responds, then the response is 400 with `{"error":"body must be JSON"}`.

**Dependencies:** none.

### R-02 (Must): OTel instrumentation

Every request emits an OTel span with attributes: `http.method`, `http.route`, `http.status_code`, `user.ip` (hashed), `request.size_bytes`, `response.size_bytes`.

**Acceptance criteria:**
- Given a request to any endpoint, when the span is exported, then all six attributes are present.
- Given the OTel exporter is misconfigured (bad OTLP endpoint), when the server starts, then it logs a warning and continues serving (does not block on exporter health).

**Dependencies:** R-01.

### R-03 (Should): Health endpoint

`GET /healthz` returns 200 with `{"status":"ok"}` when the service is healthy.

**Acceptance criteria:**
- Given the service is running and OTel exporter is reachable, when a client hits `/healthz`, then 200 with `{"status":"ok"}` within 100ms.

**Dependencies:** R-01.

### Won't (this release)

- Authentication (the service is intentionally public for learning).
- Per-user rate limiting (tracked as OQ-01; may ship before public launch).
- Persistent storage (service is stateless by design).

## Non-functional requirements

| Dimension | Threshold |
|---|---|
| Performance | p95 end-to-end under 200ms for 1KB POSTs on Fly.io shared-cpu-1x |
| Scale | 50 req/s sustained on free tier; no horizontal scaling at v1 |
| Availability | 99% monthly (free tier; not SLA-backed) |
| Security | No PII collected; IPs hashed in logs and spans |
| Privacy | Not applicable; no user accounts, no stored data |
| Compliance | None required (public learning tool, no user data) |
| Accessibility | N/A (API only; no UI) |
| Observability | Every request traced to Honeycomb; SLO dashboard panels for availability, latency p95, error rate |
| Data retention | Span retention: 60 days (Honeycomb free tier default) |
| Browser support | N/A |
| Operational | Zero-downtime deploys via Fly rolling strategy; rollback-window last 3 deploys |

## Scope and out-of-scope

### Part 1: Explicit no-gos

- **Authentication.** Cut for v1. The service is a learning target; auth adds complexity for no educational benefit.
- **Multi-region deploy.** Cut for v1. Single Fly.io region (IAD) keeps costs at zero.
- **UI.** Cut for v1. API-only; any UI would be out of scope for a traced-endpoint dogfood.

### Part 2: Deferrals

- **Per-user rate limiting.** Deferred to v1.1, gated on OQ-01.
- **Additional endpoints (reverse, hash, etc.).** Deferred to v1.1+ if educator feedback asks.

### Part 3: Non-ownership

- **Honeycomb team / API key provisioning.** Owned by the individual operator; the PRD assumes a `HONEYCOMB_API_KEY` env var exists.
- **DNS.** Owned by the operator; the service assumes a Fly-provided hostname at v1.

## Rabbit holes

- **Perfect OTel semantic conventions compliance.** Tempting to map every attribute to the canonical OTel spec precisely. Rabbit hole: spec evolves; 100% compliance is a moving target.
  - **Smallest version that avoids:** use OTel HTTP semantic conventions for the six named attributes; accept drift as the spec updates; publish current mapping in README.

## Risks

### Risk: Free-tier exhaustion

**Failure mode:** Fly.io's free tier caps or paid-only changes could make the service un-runnable on free tier.
**Owner:** h.
**Mitigation:** Publish Dockerfile and fly.toml so operators can redeploy elsewhere; document the switch-cost explicitly.
**Trigger:** Fly.io pricing page announces free-tier removal.

### Risk: Honeycomb free-tier changes

**Failure mode:** Honeycomb changes free-tier retention or span caps, breaking the 60-day retention NFR.
**Owner:** h.
**Mitigation:** Observability backend is configurable via OTLP endpoint; Grafana Cloud or Axiom can substitute.
**Trigger:** Honeycomb pricing page change.

## Assumptions

### Assumption: Learners want a deployed target, not a local-only

**Claim:** Learners working through observability tutorials get more value from a live deployed service than from a local-only Express app.
**Evidence:** Dogfood observation across deploy-ready and observe-ready authoring: every "try it" workshop path benefited from a real deploy target.
**Validation:** Measure README/dogfood references across siblings at 90 days.

## Downstream handoff

Full handoff block at `dogfood/HANDOFF.md`.

## Sign-off ledger

(Dogfood; no formal signers.)
