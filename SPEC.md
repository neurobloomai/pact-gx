# PACT-GX Specification

## Governance Expression Protocol — Technical Architecture

---

## Overview

PACT-GX is the expression protocol for collective authority — the problem of how a group acts as a single entity: how it decides, how it binds its members, how it holds itself accountable, and how it changes its mind.

It is built on rlp-0's semantic layer, reinterpreting the four primitives for the geometry of _collective_ relationships, where the participants are a collective and the systems, roles, or commitments the collective acts upon.

PACT-GX is the repair mechanism for PACT-SX. Where PACT-SX detects that a system is in rupture, PACT-GX is where the collective makes legitimate, accountable decisions about what to do about it.

---

## Architecture

```
┌─────────────────────────────────────────────┐
│           Expression Protocols              │
│  PACT-HX  PACT-AX  PACT-HH  PACT-SX  PACT-GX│
├─────────────────────────────────────────────┤
│              rlp-0 substrate                │
│    trust · intent · narrative · commitments │
│         rupture detection · gates           │
├─────────────────────────────────────────────┤
│              storage layer                  │
└─────────────────────────────────────────────┘
```

PACT-GX sits alongside the other expression protocols. It handles a relationship geometry they cannot: the collective acting as one.

---

## The Four Primitives — Governance Reinterpretation

### 1. Trust → Conferred Authority

In dyadic protocols, trust is exchanged between individuals. In PACT-GX, trust becomes _authority conferred by a collective on one of its members or instruments_. Authority is not a possession — it is an active, observable, revocable relationship between the collective and the authority-holder.

Conferred authority is legible through three signals:

**Legitimacy** — By what process was the authority granted? Authority granted through a process the collective considers legitimate has a much higher trust signal than authority assumed or captured.

**Fidelity** — Is the authority-holder acting on behalf of the collective that granted it? Fidelity erodes when the holder's actions begin to serve interests other than those of the granting collective — the earliest sign of capture.

**Revocability** — Can the collective take the authority back? Authority that cannot be revoked has ceased to be conferred authority — it has become captured power, regardless of its original grant.

```python
class ConferredAuthority:
    legitimacy: float       # 0.0 - 1.0: quality of the process by which authority was granted
    fidelity: float         # 0.0 - 1.0: alignment of holder's actions with collective interest
    revocability: float     # 0.0 - 1.0: the collective's actual ability to take authority back

    @property
    def signal(self) -> float:
        # fidelity is weighted highest — a legitimate grant means nothing if the holder
        # no longer acts for the collective, and revocability is meaningless without it
        return (self.legitimacy * 0.25 +
                self.fidelity * 0.5 +
                self.revocability * 0.25)
```

Conferred authority decays by default. Without ongoing signals of fidelity — accountable action, responsive decisions, honored commitments — the authority signal drifts toward suspension. Suspension is not revocation. It is the honest state of authority that is no longer being actively exercised on behalf of the collective.

---

### 2. Intent → Collective Intent

A collective does not have intent in the way an individual does. PACT-GX treats collective intent as an _inference_ from the decision record itself, derived from three sources:

**Declared purpose** — What did the collective say this decision is for? The stated reason, recorded in the decision record.

**Distribution of effect** — Who benefits and who bears the cost of the decision? The distribution across members is the clearest signal of what the collective actually chose — not what it said it chose.

**Dissent pattern** — Who dissented, on what grounds, and how was that dissent treated? Dissent is not noise; it is signal. The shape of the dissent reveals what the collective was willing to trade away to reach the decision.

```python
class CollectiveIntent:
    declared_purpose: str          # what the collective said the decision is for
    inferred_from_distribution: str # what the distribution of effect reveals
    inferred_from_dissent: str     # what the shape of dissent reveals

    coherence_gap: float           # distance between declared and inferred intent
```

A high `coherence_gap` is a primary governance rupture precursor. When what a collective _says_ it decided diverges significantly from what it _actually_ enacted, legitimacy is eroding — even if the decision was formally passed.

---

### 3. Narrative → Decision Provenance

In dyadic protocols, narrative is co-authored. In collective protocols, narrative becomes _provenance_ — the traceable history of how a decision came to be. A decision without provenance cannot be evaluated, appealed, or learned from.

PACT-GX preserves three narrative layers for every decision:

**The record** — What was formally decided: the resolved text, the binding language, the scope and expiry.

**The deliberation** — How it was decided: who was consulted, what information they had, what alternatives were considered, what process was followed.

**The dissent** — What was held against: every formally registered disagreement, with the dissenter, the grounds, and the question of whether the dissent was addressed or set aside.

```python
class DecisionProvenance:
    record: str                    # the formal decision text
    deliberation: Deliberation     # consulted parties, information, alternatives, process
    dissent: list[Dissent]         # every formally recorded disagreement, preserved

    completeness: float            # 0.0 - 1.0: how well the three layers are preserved

    # A decision with record but no deliberation or dissent has low provenance.
    # A decision with complete provenance can be audited, appealed, and learned from.
```

A decision with missing deliberation or suppressed dissent has low provenance — and low provenance decisions accumulate as hidden liabilities. The collective cannot defend what it cannot explain. PACT-GX treats dissent as first-class: the dissent record carries equal standing with the majority position in the provenance layer.

---

### 4. Commitments → Bound Decisions

In dyadic protocols, commitments are personal promises. In systemic protocols (PACT-SX), commitments are structural constraints. In PACT-GX, commitments take a third form: _bound decisions_ — collective acts that impose obligations on members, authority-holders, and the systems under the collective's care.

Bound decisions have properties that distinguish them from both personal and structural commitments:

**Scope** — A bound decision applies within an explicit domain. Decisions that drift beyond their scope are a primary rupture type.

**Expiry** — Every bound decision has an expiry condition: temporal (by date), conditional (until X is achieved), or scope-based (while this authority is in force). Decisions without expiry accumulate and concentrate.

**Dissent-attached** — The dissent recorded at decision time is attached to the binding, not separated from it. Future participants reading the binding also read the dissent that was held against it.

**Renewability** — At expiry, a bound decision does not auto-renew. It must be actively re-examined by the collective and re-bound if still needed.

```python
class BoundDecision:
    decision_id: str
    provenance: DecisionProvenance
    scope: Scope                         # domain, participants, systems affected
    authority_grant: AuthorityGrant      # who holds authority to enforce; under what grant
    expiry: ExpiryCondition              # TEMPORAL | CONDITIONAL | SCOPE_COMPLETION
    attached_dissent: list[Dissent]      # dissent travels with the binding, not separately
    still_in_force: bool | None          # None = unknown (requires audit)

class AuthorityLedger:
    bindings: list[BoundDecision]

    def audit(self) -> list[AuthorityGap]:
        # returns bindings that are expired but still being enforced,
        # or in force but no longer serving the collective
        ...

    def expiring_soon(self, window: timedelta) -> list[BoundDecision]:
        # surface bindings approaching expiry — must be renewed or released
        ...

    def unrenewed_still_enforced(self) -> list[BoundDecision]:
        # bindings that expired but are still being treated as active —
        # the most common form of captured authority
        ...
```

The `AuthorityLedger` is one of the most practically valuable outputs of PACT-GX — making the live state of collective authority legible, including the authority that is quietly operating past its legitimate expiry.

---

## Rupture Semantics

rlp-0 defines rupture as a threshold crossing in `rupture_risk`. PACT-GX extends this with _governance rupture types_ — because how a rupture must be repaired depends on what kind of authority breakdown it is.

```python
class GovernanceRuptureType(Enum):
    AUTHORITY_CAPTURE        # authority no longer serving the collective that conferred it
    DISSENT_SUPPRESSION      # dissent not recorded, or excluded from the decision record
    PROVENANCE_GAP           # decision without traceable deliberation or dissent history
    EXPIRED_AUTHORITY_ACTIVE # authority acting past its legitimate expiry
    SCOPE_DRIFT              # bound decisions enforced beyond their original scope
    FIDELITY_COLLAPSE        # authority-holder's actions systematically diverging from collective intent

class GovernanceRupture:
    rupture_type: GovernanceRuptureType
    severity: float
    detected_at: datetime
    evidence: dict                    # what signals triggered detection
    affected_bindings: list[str]      # BoundDecisions implicated
    repair_requires: RepairScope      # RENEWAL | REVOCATION | RE_DELIBERATION | STRUCTURAL_REFORM
```

PACT-GX ruptures rarely resolve through a single act. Most require one of four repair modes: **renewal** (re-examine and re-bind), **revocation** (withdraw the authority), **re-deliberation** (reopen the decision with full provenance), or **structural reform** (change the collective's own rules for conferring authority).

---

## Gate Semantics

rlp-0's gate blocks interaction until repair is acknowledged. PACT-GX gates operate on the _decision process itself_ — they block a decision from being bound, enforced, or renewed until integrity conditions are met.

* `DELIBERATION_GATE`: decision cannot be bound until minimum deliberation has occurred — consulted parties, alternatives considered, information surfaced
* `DISSENT_GATE`: decision cannot be bound until registered dissent has been formally recorded and attached to the binding
* `PROVENANCE_GATE`: decision cannot be recorded without complete provenance across record, deliberation, and dissent layers
* `AUTHORITY_GATE`: action cannot proceed if the authority invoked is expired, captured, or operating outside its granted scope
* `RENEWAL_GATE`: a binding approaching expiry cannot be treated as renewed without active re-examination by the collective

```python
class GovernanceGate:
    gate_type: GateType
    reason: str                        # which integrity condition is not met
    blocking: BoundDecision | AuthorityGrant | Decision
    required_repair: str               # what the collective must do to pass the gate
```

Gates in PACT-GX are not obstacles. They are the structural insistence that legitimate authority only operates under conditions that make it auditable and accountable.

---

## Minimum Viable Kernel

The essential implementation of PACT-GX:

```python
class PACTGX:
    collective_id: str
    authority: ConferredAuthority
    intent: CollectiveIntent
    provenance_store: ProvenanceStore
    ledger: AuthorityLedger

    def receive_signal(self, signal: GovernanceSignal) -> Decision:
        # ingest a rupture signal from PACT-SX and open a decision
        ...

    def open_deliberation(self, decision: Decision) -> Deliberation:
        ...

    def record_dissent(self, decision: Decision, dissent: Dissent) -> None:
        # dissent is first-class — never discarded, always attached
        ...

    def bind(self, decision: Decision) -> BoundDecision:
        # requires deliberation, provenance, and authority gates to pass
        ...

    def audit_authority(self) -> list[AuthorityGap]:
        # surface captured, expired, or drifted authority
        ...

    def renew_or_release(self, binding: BoundDecision) -> BoundDecision | None:
        # active re-examination at expiry; no auto-renewal
        ...

    def detect_rupture(self) -> GovernanceRupture | None:
        ...

    def emit_to_hh(self, binding: BoundDecision) -> list[HumanCommitment]:
        # bound decisions become individual commitments in PACT-HH
        ...

    def status(self) -> dict:
        # human-readable collective authority summary
        ...
```

---

## Connection to PACT-SX

PACT-SX detects. PACT-GX acts.

Every `SystemicRupture` with `repair_requires = GOVERNANCE` generates a `GovernanceSignal` that PACT-GX receives as the opening of a decision. The signal carries the rupture type, the evidence, the affected participants, and the repair scope — everything the collective needs to begin deliberation.

Without PACT-SX, PACT-GX has no reliable signal of when collective action is required. Without PACT-GX, PACT-SX's rupture detection has no legitimate repair path.

---

## Connection to PACT-HH

PACT-GX operates at the collective level. But every bound decision must eventually land in the individual relationships where implementation actually happens. That is PACT-HH's territory.

A `BoundDecision` emits one or more `HumanCommitment`s into PACT-HH — the accountability path that turns a collective act into the concrete, honored obligations of named individuals. A governance decision that no one takes responsibility for implementing is not a decision; it is an aspiration. The PACT-GX → PACT-HH emission is what prevents this.

---

## Storage Notes

Like rlp-0, PACT-GX is storage-agnostic at the semantic layer. The nature of governance data suggests:

* **Append-only storage** for decision records — a bound decision, once recorded, is never edited; corrections are themselves new decisions with their own provenance
* **Co-located storage** for dissent — the dissent record must live with the decision it was held against, not in a separate log where it can be overlooked
* **Time-series storage** for authority signals — legitimacy, fidelity, and revocability require historical depth to detect capture or drift
* **Graph storage** for authority relationships — who granted what to whom, through which process, under what expiry

The semantic layer preserves meaning across any backend. The rule that is non-negotiable: _dissent is never pruned, summaries never replace records, expired authority is never quietly renewed_.
