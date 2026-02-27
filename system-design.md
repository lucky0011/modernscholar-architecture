System Design Deep Dive – ModernScholar
1. Executive Summary

ModernScholar is a full-stack transactional SaaS platform designed to manage academic service workflows with integrated financial processing and automated payouts.

The system integrates:

Role-based workflow management

Stripe Checkout & webhook verification

M-Pesa Daraja B2C disbursements

Event-driven notification architecture

Scheduled backend automation

Financial state reconciliation

Production source code remains private due to financial integration security. This document outlines the architectural decisions and engineering strategy.

2. Design Objectives

The system was designed with the following priorities:

Financial integrity

Idempotent payment handling

Role-based access enforcement

Event-driven automation

Firestore scalability

Fault-tolerant payout processing

Secure external API integrations

3. High-Level Architecture

The platform follows a 4-layer architecture:

Layer 1 — Client

React frontend

Firebase Auth

Firestore real-time listeners

Layer 2 — Backend

Firebase Cloud Functions (v2)

Firestore database

Role-based access control

Layer 3 — External Services

Stripe API

Stripe Webhooks

M-Pesa Daraja API

Email provider (SMTP abstraction)

Layer 4 — Scheduled Automation

Firebase onSchedule triggers

Financial reconciliation tasks

System integrity checks

4. Order Lifecycle Engine

Orders follow controlled state transitions:

pending

paid

assigned

in_progress

revision_requested

completed

closed

Key constraints:

Clients cannot modify payment state.

Financial mutations are server-side only.

Revision count is tracked and monitored.

Admin warnings trigger when revision threshold is reached.

All transitions involving financial impact are executed within Cloud Functions.

5. Financial Architecture
5.1 Stripe Integration

Checkout Flow:

Client requests checkout session.

Callable Cloud Function creates Stripe session.

Stripe redirects client.

Webhook event confirms payment.

Server validates signature.

Order status updated to paid.

Idempotency safeguards:

Webhook signature verification

Order existence validation

Payment state guards

Duplicate update prevention

This prevents replay attacks and double-confirmation.

5.2 M-Pesa B2C Payout System

The payout system uses a job-based architecture.

Payout lifecycle:

pending

processing

success

failed

Each payout job stores:

Invoice reference

Editor ID

Attempt count

Transaction ID

Result code

Callback payload

Duplicate prevention logic:

Invoice must be approved

payoutCompleted flag enforced

Job uniqueness constraint

Server-only payout initiation

Callback reconciliation:

Daraja callback endpoint updates payout status

Firestore logs complete transaction metadata

Retry-safe state updates

6. Notification System Architecture

The notification engine is event-driven.

Triggers include:

New order created

Order assignment

Chat messages

Work upload

Revision request

Invoice submission

Payout status update

Architecture:

Firestore write triggers

Role-based conditional dispatch

Email templating abstraction

Notification collection storage

Unread tracking:

unreadByEditor

unreadByClient

Unread state auto-clears upon view.

7. Firestore Data Modeling Strategy

Collections:

users

orders

orderMessages

notifications

invoices

payoutJobs

payouts

revisionLogs

Design principles:

Flat structure (avoid deep nesting)

Indexed query fields for dashboard filtering

Financial records separated from operational data

Immutable payout history

Timestamp-based sorting for admin analytics

Performance considerations:

Indexed fields for:

order status

assignedEditorId

createdAt

payout status

Controlled read/write patterns

Minimal document fan-out

8. Role-Based Access Control (RBAC)

Roles:

Client

Editor

Admin

Enforcement strategy:

Firebase Auth custom claims

Firestore security rules

Server-side privilege checks

Admin-only payout execution

Clients cannot:

Modify payment status

Trigger payouts

Edit invoice approval state

Editors cannot:

Approve invoices

Access unrelated orders

Modify payout jobs

9. Scheduled Automation

Firebase onSchedule functions perform:

Balance verification

Payout reconciliation checks

Stale order detection

System integrity monitoring

This reduces manual intervention and prevents silent financial inconsistencies.

10. Failure Handling & Resilience

The system is designed for:

Webhook retries

Daraja callback delays

Network interruptions

Partial transaction failures

Mitigation strategies:

Structured logging

Retry-safe state transitions

Idempotent function guards

Attempt counters for payout jobs

Explicit failure state storage

All critical failures are logged for traceability.

11. Security Strategy

Security is enforced at multiple levels:

Role-based Firestore rules

Callable function authentication checks

Environment-based secret management

Webhook signature verification

No client-side financial mutation

Secure backend boundary separation

Sensitive credentials are stored in environment configuration, never in client code.

12. Scalability Considerations

The architecture supports scaling through:

Stateless Cloud Functions

Indexed Firestore queries

Separation of financial and operational data

Modular backend structure

Event-driven processing model

The system is designed to support increasing transaction volume without architectural redesign.

13. Engineering Outcomes

This project demonstrates:

Full-stack SaaS architecture

Financial transaction integrity design

Payment reconciliation logic

Mobile money API integration

Event-driven backend automation

Production debugging discipline

Real-world fintech workflow orchestration
