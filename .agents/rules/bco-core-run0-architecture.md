# BCO Core Architecture Lock — Run 0 (System Contract)

> This is the immutable architecture contract for the Brandable Control OS Core (BCO Core).
> All future runs (Run 1–4+) MUST comply. Any violation = invalid system design.
> DO NOT MODIFY WITHOUT A NEW VERSIONED DOCUMENT.

---

## 1. System Definition

The system is a:
- Modular, event-driven Control OS
- Storage-agnostic state engine
- Plugin-based module ecosystem
- UI as a pure rendering layer

NOT:
- A single-purpose application
- A hardcoded SaaS product
- A direct database-dependent system

---

## 2. Core Architecture Rules

**RULE 1 — SINGLE SOURCE OF TRUTH (SSOT)**
- One global state store: `storage.js`
- No duplicate state sources
- No local component-owned state as source of truth

**RULE 2 — EVENT-FIRST ARCHITECTURE**
- Everything originates as an EVENT
- State changes only happen after event processing
- No direct state mutation allowed

**RULE 3 — STORAGE ABSTRACTION (MANDATORY)**
- Storage must be abstracted via adapter layer
- Must support: LocalStorageAdapter (initial), SupabaseAdapter (future swap)
- System logic must NEVER depend on storage type

**RULE 4 — MODULE ISOLATION**
- Each module has its own namespace
- Modules cannot directly mutate other modules
- Communication ONLY via events/actions

**RULE 5 — AI NON-DESTRUCTIVE ROLE**
- AI can suggest, analyse, and recommend
- AI cannot directly mutate system state
- AI actions must pass through Action Engine + Rules

**RULE 6 — RULE ENGINE AUTHORITY**
Execution order is strict:
1. Event occurs
2. Rules evaluated
3. Actions validated
4. State updated
5. UI sync triggered

If rule blocks action → action is NOT executed

**RULE 7 — UI IS READ-ONLY RENDER LAYER**
- UI may dispatch events
- UI may display state
- UI MUST NOT mutate state directly

**RULE 8 — NO HARD COUPLING**
Forbidden:
- direct storage calls outside adapter
- module-to-module direct writes
- UI direct state mutation
- AI bypassing action engine
- cross-module hidden dependencies

---

## 3. Global Data Model Contract

Core Entities: User, Role, Session, Module, Event, Action, Alert, Log

---

## 4. Event Structure (Universal Format)

```json
{
  "id": "string",
  "type": "string",
  "source": "user | ai | system",
  "module": "string",
  "payload": "object",
  "timestamp": "ISO_STRING"
}
```

---

## 5. Action Structure (Universal Format)

```json
{
  "id": "string",
  "type": "string",
  "status": "pending | approved | rejected | executed",
  "triggeredBy": "event_id",
  "payload": "object",
  "timestamp": "ISO_STRING"
}
```

---

## 6. Module Contract

Every module must define:

```json
{
  "name": "string",
  "entities": [],
  "actions": [],
  "rules": [],
  "ui_blocks": [],
  "permissions": []
}
```

Module Rules:
- Must be isolated namespace
- Must use event system only
- Must not mutate global state directly

---

## 7. Storage API Contract

System MUST expose ONLY:
- `storage.get()`
- `storage.set()`
- `storage.update()`
- `storage.delete()`
- `storage.subscribe()`

No direct storage access outside this interface.

---

## 8. AI System Limits

ALLOWED: pattern detection, recommendations, insights, suggested actions
FORBIDDEN: direct state mutation, bypassing rules engine, silent data changes, uncontrolled execution

---

## 9. System Flow (Absolute Order)

```
User / AI / System
        ↓
      Event
        ↓
   Rule Engine
        ↓
  Action Engine
        ↓
 Storage Adapter
        ↓
   Event Log
        ↓
     UI Sync
```

---

## 10. Feature Drift Protection

NEVER ALLOW:
- schema-free state injection
- UI-driven state mutation
- module cross-writing
- direct DB coupling
- bypassing action/rule engine
- untracked AI execution

---

## 11. Run Boundary Definition

- **Run 0:** Defines what the system is allowed to be
- **Run 1+:** Defines how the system is implemented

Anything outside this contract is INVALID architecture.
