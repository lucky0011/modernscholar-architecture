ModernScholar – System Architecture & Engineering Case Study
Overview

ModernScholar is a full-stack academic task management platform built to automate order processing, payments, editor assignment, revisions, notifications, and payouts.

The system is designed for:

Secure payment processing

Automated financial reconciliation

Role-based workflow control

Event-driven notifications

Scalable Firestore data modeling

Production-grade payout handling

Note: Production source code is private. This repository showcases architecture, system design, and implementation approach.

Tech Stack
Frontend

React.js (Hooks, Context API)

Tailwind CSS

Firebase SDK v9

Real-time Firestore listeners

Backend

Firebase Cloud Functions (v2)

Node.js

Firestore

Firebase Auth

Payments

Stripe Checkout

Stripe Webhooks

M-Pesa Daraja B2C API

Scheduled payout verification

Infrastructure

Firebase Hosting

Firestore security rules

Role-based access control

Cron-based scheduled functions

Core System Architecture
High-Level Flow

Client submits order

Stripe Checkout session created

Webhook confirms payment

Order status updated to paid

Admin assigns editor

Editor submits work

Client may request revisions

Invoice generated

Admin approves payout

M-Pesa B2C disbursement triggered

Callback reconciles transaction status

Major Engineering Components
1. Order Lifecycle Engine

Implemented status transitions:

pending

paid

assigned

in_progress

revision_requested

completed

closed

Includes:

Revision counter tracking

Admin warning at 2+ revisions

Auto-notification triggers per state change

2. Stripe Integration

Callable function creates checkout session

Secure webhook validation

Idempotent order confirmation

Payment reconciliation logic

Failure handling & logging

Security:

No sensitive data exposed to client

Webhook signature verification

Server-side status enforcement

3. M-Pesa B2C Payout System

Custom payout job processor:

Admin approval required

Payout job stored in Firestore

Daraja API invoked via Cloud Function

Callback endpoint handles result

Retry-safe logging

Status reconciliation (Success / Failed / Rejected)

Features:

Prevents duplicate payouts

Tracks attempts

Stores transaction reference IDs

Handles error code 2001 and edge cases

4. Notification Engine

Event-driven notification system:

Triggers include:

New order (Admin + Client)

Assignment (Editor + Client)

Chat messages (role-based delivery)

Work upload (Client)

Revision request (Editor + Admin)

Invoice submission (Admin)

Includes:

Firestore notification collection

Email templating system

Unread message flags

Smart read detection

5. Scheduled Automation

Balance checks via onSchedule

Payout verification cron

Stale order monitoring

Financial reconciliation automation

Data Modeling Strategy

Firestore Collections:

users

orders

orderMessages

notifications

invoices

payoutJobs

payouts

revisionLogs

Design principles:

Document-level security rules

Indexed queries for admin dashboard

Separation of financial records

Immutable payout history

Security Architecture

Role-based Firestore rules

Callable function authentication enforcement

Admin-only payout triggers

Duplicate payout prevention

Environment variable isolation

Webhook secret validation

Production Challenges Solved

Preventing double payouts

Handling M-Pesa callback inconsistencies

Managing asynchronous Stripe + Daraja flows

Designing idempotent financial updates

Avoiding race conditions in order updates

Tracking unread message state per role

Architecture Diagram

See: architecture/modernscholar-architecture-diagram.png

Screenshots

See /screenshots folder for:

Admin Dashboard

Order Detail Workflow

Notification System

Payout Job Logs

(Sensitive data blurred.)

Engineering Focus

This project demonstrates:

Full-stack architecture design

Financial systems integration

Event-driven backend logic

Real-world payment reconciliation

Automation-first system design

Production debugging and logging discipline

Contact

If you would like a technical walkthrough or system deep dive, feel free to reach out.
