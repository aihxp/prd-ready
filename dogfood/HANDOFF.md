# PRD handoff (dogfood: Mirror Box)

**From:** prd-ready v1.0.0, 2026-04-23
**PRD:** `dogfood/PRD.md` (Tier 2 Spec)
**Project:** Mirror Box (toy traced echo service)

## To stack-ready (live, v1.1.5)

### Pre-flight answers

- **Domain:** Developer / infrastructure tool (closest match in stack-ready's 12 profiles). Overrides: single-endpoint public service, no user accounts.
- **Team:** 1 engineer (solo dogfood); JavaScript/Node depth; no on-call.
- **Budget posture:** Free-tier scrappy. No paid services unless free tier is unavailable.
- **Time-to-ship:** Weeks (past tense; shipped in 6 weeks).
- **Scale ceiling (12 months):** <100 req/s sustained; <50M spans/month; <10k unique workshop users total.
- **Regulatory constraints:** None. No PII collected.

Ready to skip stack-ready's Step 1. stack-ready can proceed to Step 2 (constraint mapping).

## To architecture-ready (not yet released)

### Entities

- **Request** (transient: method, route, body, headers).
- **Span** (OTel: trace_id, span_id, attributes, start_time, end_time).

### Flows

- **Echo request:** client POSTs JSON -> server validates JSON -> server augments body with metadata -> OTel span emitted -> response returned.
- **Health check:** client GETs /healthz -> server checks OTel exporter health -> response returned.
- **Exporter failure (error path):** OTel exporter unreachable -> log warning -> continue serving.

### Non-functional requirements

See PRD Non-functional Requirements section.

### Integration points

- OTel OTLP exporter -> Honeycomb (or configurable endpoint).
- Fly.io runtime (host + logs).

### Trust boundaries

- Public service; no auth.
- No persistence; no data at rest.
- IPs hashed before any logging or span emission.

### Explicitly deferred to architecture

- None meaningful; the service is trivially simple.

## To roadmap-ready (not yet released)

### Priority ordering

- R-01 (Must) Echo endpoint
- R-02 (Must) OTel instrumentation
- R-03 (Should) Health endpoint
- Won'ts: auth, rate limiting, persistent storage

### Release-gating criteria

- R-01 + R-02 gate initial open-source release.
- R-03 does not gate; ships post-launch if not at v1.

### Dependencies

- R-02 depends on R-01 (spans describe request handling, so the request handler exists first).
- R-03 depends on R-01 (sharing the server runtime).

### Must-haves vs. nice-to-haves

Cut line: below R-02. R-03 is nice-to-have but cheap.

### Dates or ranges

No hard deadlines.

## To production-ready (live, v2.5.6)

### Entities and CRUD surface

- No persistent entities; the service is stateless. Requests flow through; no CRUD.

### Roles and permissions

- None. Single public role.

### Audit trail requirements

- Every request span shipped to Honeycomb. Retention: 60 days.

### Acceptance criteria per feature

See PRD Functional Requirements section.

### Error and edge states

- Non-JSON body: 400.
- OTel exporter unreachable: log warning, continue serving.
- Rate above sustainable: respond 429 with Retry-After (if R-rate-limiting lands at v1.1).

### Visual identity direction

N/A (API only).

### Domain landmines

- Free-tier pricing changes (Fly, Honeycomb) can break runability.
- OTel semantic conventions evolve; current attribute mapping may drift.
