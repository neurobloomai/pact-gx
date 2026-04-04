# PACT-GX Design Principles
## Governance Expression Protocol

---

## What is collective authority?

Every other PACT protocol handles relationships between *individual participants* — even pact-hh, which coordinates humans, does so one interaction at a time. None of them can answer the question:

*How does a group make a decision that binds everyone in it?*

This is the problem of collective authority. It is not the same as consensus (everyone agreeing). It is not the same as majority rule (most people agreeing). It is the deeper question of *how a collective acts as a single entity* — how it speaks, how it decides, how it holds itself accountable, and how it changes its mind.

PACT-GX is the protocol for collective authority.

---

## Why authority, not governance

"Governance" is commonly understood as process — rules, procedures, voting mechanisms. PACT-GX is not a voting system or a parliamentary procedure. It is a protocol for *relational authority* — the question of who has the legitimate power to act on behalf of a collective, under what conditions, and with what accountability.

Authority in this sense is:
- **Granted**, not assumed — it flows from the collective, not from position
- **Bounded** — it applies in specific domains and expires under specific conditions
- **Accountable** — the exercise of authority is recorded and auditable
- **Revocable** — collectives can take back authority they have granted

These four properties are what distinguish legitimate collective authority from captured power.

---

## The upstream dependency: PACT-SX

PACT-GX cannot work without PACT-SX. Collective authority is always authority *over something* — a system, a resource, a shared infrastructure. PACT-SX makes systemic relationships legible. PACT-GX acts on that legibility.

The formal handoff is the `GovernanceSignal` — a structured payload from PACT-SX that says: *this system is in rupture, repair requires collective action, here is what we know.*

PACT-GX receives that signal and routes it into the collective authority process: naming the decision, assembling the relevant participants, surfacing the authority question, logging the outcome.

Without PACT-SX, PACT-GX is authority without sight. Without PACT-GX, PACT-SX is sight without hands.

---

## Design principles

### 1. Authority is relational, not positional
In most governance systems, authority flows from position — the CEO decides, the committee votes, the regulator rules. PACT-GX treats authority as *relational* — it flows from the trust, intent, narrative, and commitments that connect the authority-holder to the collective they serve.

An authority-holder who has lost the trust of the collective has lost their authority, even if they still hold the position. PACT-GX tracks this.

### 2. Decisions have provenance
Every collective decision in PACT-GX carries its full history: who was consulted, what information they had, what alternatives were considered, who dissented and why, what conditions were attached. Decisions without provenance cannot be evaluated, appealed, or learned from.

Provenance is not bureaucracy. It is the minimum information needed to hold authority accountable.

### 3. Dissent is structural, not optional
PACT-GX does not optimize for unanimity. It requires that dissent be formally recorded and preserved. A collective that cannot hold its own dissent has no honest account of itself.

Dissent in PACT-GX is first-class — it carries the same weight in the decision record as the majority position. Future participants can see what was traded away in any decision.

### 4. Authority expires
All authority grants in PACT-GX have expiry — either by time, by condition, or by scope completion. Authority that does not expire accumulates and concentrates. The protocol resists this structurally by requiring renewal, not just revocation.

Renewal is not automatic. It requires the collective to re-examine whether the authority is still needed, still bounded, and still accountable.

### 5. Repair is the primary output
PACT-GX exists primarily to repair systemic ruptures surfaced by PACT-SX. This shapes everything about its design. It is not optimized for speed, or efficiency, or elegance — it is optimized for *legitimate repair* that the collective can stand behind.

Repair that is forced through quickly, without participation, without dissent, without provenance — is not repair. It is suppression.

### 6. Collective memory outlasts individual participants
The collective that makes a decision today is not the same collective that will live with it in ten years. PACT-GX is designed with this in mind: decisions are made *for* future participants, not just current ones. The CommitmentLedger (from PACT-SX) is the accountability mechanism — future participants can inspect what was decided, by whom, under what conditions.

---

## What PACT-GX is not

**Not a voting system.** PACT-GX doesn't implement voting mechanisms — those are expression-layer choices that collectives make. The protocol provides the authority infrastructure *underneath* any voting mechanism.

**Not a consensus engine.** Consensus is one possible outcome of collective authority. It is not the only one, and in many cases it is not the right one. PACT-GX supports decision-making under disagreement.

**Not a replacement for human judgment.** The protocol surfaces information, tracks authority, and records decisions. The actual judgment — what to decide — remains with the humans in the collective. PACT-GX makes that judgment legible and accountable, not automatic.

**Not limited to large groups.** Collective authority applies anywhere a group needs to act as one — two people deciding on a shared resource, a small team setting a policy, a global community revising a protocol. The scale changes; the principles don't.

---

## The downstream connection: PACT-HH

PACT-GX operates at the collective level. But collectives are made of humans in relationship. PACT-HH (human-human) is the layer where those individual relationships happen — trust bonds, shared context, the texture of how humans actually coordinate.

PACT-GX decisions must eventually land in PACT-HH's territory — they must become real in the relationships between people. A governance decision that no individual takes responsibility for implementing is not a decision. It is an aspiration.

The connection between PACT-GX and PACT-HH is the accountability path: collective decisions become individual commitments, and those commitments are tracked in the human-human layer.

---

## Summary

PACT-GX is the protocol for collective authority — how groups make binding decisions about systems they share, with full provenance, structural dissent, bounded authority, and an honest accountability trail.

It is the repair mechanism for PACT-SX. It is the collective layer above PACT-HH. And it is the answer to the question every other PACT protocol eventually hits: *who decides?*
