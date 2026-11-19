---
name: customer-experience
description: Customer Experience (CX) design and audit framework covering end-to-end user journeys, friction removal, time-to-value onboarding, transparent error/recovery flows, and CX metrics (CSAT, NPS, CES).
when_to_use: "When designing product flows, auditing onboarding or checkout friction, improving retention, crafting empathetic error/empty states, or defining CX metrics."
allowed-tools: Read, Write, Edit, Glob, Grep
version: 1.0.0
---

# Customer Experience (CX) — Journey & Friction Reduction

> Great CX removes uncertainty and effort at every touchpoint — especially when things go wrong.

## Core Principles

1. **Time-to-Value (TTV) First**: Let users experience the core value in the fewest possible steps; defer non-essential setup (profile details, optional integrations) until after the first win.
2. **Zero Dead Ends**: Every empty state, loading state, 404, permission block, or payment/API failure must explain *what happened*, *why*, and *the exact next action* to recover.
3. **Radical Transparency**: Never hide pricing, fees, limits, calculation formulas, or irreversible consequences. Show previews and clear breakdowns before asking for commitment.
4. **Low Cognitive & Effort Load**: Pre-fill smart defaults, remember context, avoid asking twice for the same data, and minimize required fields.
5. **Consistency Across Touchpoints**: Keep terminology, tone, status labels, and notifications (in-app, email, push, support) aligned so users never feel like they switched products.

## CX Audit Protocol

### Step 1: Map the End-to-End Journey
Trace the 5 key stages for the feature or product:
- **Discover & Enter**: Clarity of value proposition and entry CTA.
- **Onboard & First Win**: Steps and time required to reach the first meaningful outcome.
- **Core Usage Loop**: Speed, clarity, and feedback on primary tasks.
- **Edge Cases & Recovery**: Network drops, validation errors, empty states, undo/cancel/refund paths.
- **Retention & Advocacy**: Follow-up notifications, progress visibility, and referral/feedback touchpoints.

### Step 2: Hunt for Friction
Flag and fix:
- Unexplained jargon, ambiguous button labels ("Submit" vs "Confirm Payment — $20").
- Silent failures or generic `"Something went wrong"` errors without a retry/support path.
- Forced account creation before users can even preview value.
- Multi-step forms without progress indicators or draft persistence.

### Step 3: Measure What Matters
- **CES (Customer Effort Score)**: How easy was it to complete the core task or resolve an issue?
- **CSAT (Customer Satisfaction)**: Immediate post-interaction satisfaction (after checkout, support, or key workflow).
- **NPS (Net Promoter Score)**: Long-term loyalty & willingness to recommend.
- **Behavioral Signals**: Onboarding completion rate, step-by-step funnel drop-off, time-to-first-value, and repeat-error rate.

## Verification Checklist

- Every primary flow has explicit **happy path**, **empty state**, **loading state**, and **recoverable error state** defined.
- Irreversible actions require clear confirmation with consequence summary; reversible actions provide immediate **Undo** where feasible.
- Copy is concise, human, action-oriented, and free of internal system jargon.
