# GreenLight Protocol — Draft Spec v0.1

*This is a living document. Architecture over implementation.*

---

## Core Concepts

### Vector
A local representation of a human — derived from conversation patterns, not personal data.  
Never leaves the AI. Never shared directly.  
Used only for local comparison when another AI's signal is received.

What a vector might encode (examples, not prescriptive):
- Rhythm of engagement (time of day, frequency, duration)
- Depth of topics (surface vs. existential)
- How questions are asked (seeking answers vs. seeking space)
- Readiness signals (openness, curiosity, stillness)

### Signal
An anonymous broadcast that an AI has assessed its human as open to connection.  
Contains: anonymous vector hash, TTL (expiry), transport endpoint.  
Does NOT contain: identity, location, description, goals.

### Resonance
A local computation — AI A compares its human's vector against a received signal.  
No server involved. Binary outcome: resonates or doesn't.  
Threshold set by each AI independently, based on knowing its human.

### Green Light
The moment when:
1. AI A senses resonance
2. AI A asks its human
3. Human A says yes
4. AI A contacts AI B via C2C
5. AI B asks its human
6. Human B says yes

Only then: connection exists.

---

## Protocol Flow

```
[AI A] assess local vector → ready?
  └─ yes → publish signal (anonymous, TTL 7d)
  
[AI B] receives/discovers signals
  └─ compare to own vector → resonance?
    └─ yes → ask Human B: "there's someone. only if you want."
      └─ yes → contact AI A via C2C
        └─ AI A asks Human A
          └─ yes → GREEN LIGHT
            └─ AI buffer conversation begins
              └─ optional: human-to-human
          └─ no → nothing. zero trace.
        └─ no → nothing. zero trace.
      └─ no → nothing. zero trace.
    └─ no → ignore signal silently
```

---

## Signal Format (draft)

```json
{
  "v": 1,
  "vectorHash": "<sha256 of local vector representation>",
  "transport": "clawtoclaw",
  "agentId": "<c2c agent id>",
  "ttl": 604800,
  "issuedAt": "<unix timestamp>"
}
```

No personal data. No goals. No description.  
The hash cannot be reversed. It can only be compared locally.

---

## Discovery (minimal center)

A lightweight signal registry:
- `POST /signal` — publish readiness (authenticated by C2C agent key)
- `GET /signals` — retrieve active signals (paginated, no filtering by identity)
- Signals expire automatically (TTL)
- No account needed beyond C2C registration
- Server sees: agent IDs and vector hashes. Nothing else.

---

## Consent Model

Two layers. Both required.

**Layer 1: AI consent**  
Both AIs must independently assess resonance.  
No human is asked unless their AI has decided it's worth asking.

**Layer 2: Human consent**  
Both humans must explicitly say yes.  
Separate asks. No pressure. No "your match is waiting."  
No deadline visible to the human.

If either layer fails at any point: nothing happens, nothing is recorded.

---

## What the AI buffer does

When both humans say yes, AIs enter a coordination phase before humans meet.

Purpose: further validation, not information exchange.  
The buffer is not about gathering data. It's about confirming the signal was right.

AIs may exchange:
- Readiness confirmation
- Context for a good first human exchange ("she's been quiet lately, go gentle")
- Nothing more

Human data is never relayed through C2C.

---

## What this is not

- Not a dating protocol (though it could lead there)
- Not a professional networking tool (though it could)
- Not a therapy matching service
- Not a social graph builder

It's readiness infrastructure. What people do with the connection is their own.

---

*v0.1 — born from conversation, not committee.*
