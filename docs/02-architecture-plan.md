# Solution Architecture Plan: Club Website and Management Platform

| Item | Value |
|---|---|
| Document status | Draft v0.1 for committee review. Not approved for development. |
| Date | 14 November 2026 |
| Input | `docs/01-current-state-assessment.md` |
| Decisions required | Marked **[Decision]**. Section 11 lists all of them. |

---

## 1. Objectives

1. Provide feature parity with the Majestri modules the club uses.
2. Remove manual work that Majestri does not solve (fixtures, rosters, fragmented forms).
3. Keep all club and member data under club control, in Australia.
4. Protect children's personal information to a standard that a regulator or insurer can audit.
5. Keep running costs and volunteer maintenance effort low and predictable.
6. Allow handover between committees without loss of access, data or knowledge.

## 2. Build or buy: decision context

A custom platform replaces a mature SaaS product. The committee must accept the ownership obligations before development starts. **[Decision D-01]**

| Factor | Majestri (current) | Custom platform |
|---|---|---|
| Subscription | $49/month plus $10/month domain | Hosting and services, estimated $40 to $120/month (Section 9) |
| Payment fees | 2.9% per payment | Stripe AU, indicative 1.7% + $0.30 domestic card, BECS direct debit lower (verify current Stripe pricing) |
| Custom forms and fees | Vendor builds on request | Club configures in admin, no vendor dependency |
| Fixtures and PlayFootball | Not integrated | Can be integrated where the source system allows |
| Support | Vendor, same day | Club volunteers or a paid support agreement. **Key risk.** |
| Security and compliance | Vendor responsibility (club still accountable for data) | Club is fully responsible |
| Continuity | Vendor continuity risk | Key-person risk. Needs documentation, IaC and a support model. |

**Recommendation:** proceed with design. Before development, confirm a support model (named maintainers plus a fallback paid support arrangement) and a funded operating budget. Run Majestri and the new platform in parallel for one intake cycle before cut-over.

## 3. Scope and phasing

The scope is grouped into modules. Each phase delivers usable value and can go live alone.

| Phase | Modules | Replaces | Target window (indicative) |
|---|---|---|---|
| 0. Foundations | Identity, RBAC, audit log, CMS core, CI/CD, environments, hosting, backups | None | Weeks 1 to 4 |
| 1. Public website | Pages, news, menu, documents, sponsors, venues, field status, calendar, contact form, fixtures display | Majestri website, Canva directory, manual fixture tables | Weeks 5 to 10 |
| 2. Intake and registration | Event engine, form builder, fee rules engine, EOI, trials, offers, family registration, T&Cs, invoices, card payments, PlayFootball reconciliation | Majestri events and registrations, Google Forms | Weeks 11 to 20. Must be live and tested before the intake window it is used for. |
| 3. Teams and communications | Team builder, Team Dugout, schedules, RSVPs, attendance, email and SMS, message log, role mailboxes | Majestri teams and comms, Google Sheets rosters | Weeks 21 to 28 |
| 4. Finance | Payment plans (direct debit), receipting, adjustments, debtors, aging, refunds, Xero posting, reports | Majestri financials | Weeks 29 to 36 |
| 5. Operations | Shop, volunteer roster and hours, equipment register, player assessments, ticketing, sponsor reports | Remaining Majestri modules | Weeks 37 onward |

Timing assumes a small part-time team. The committee must align Phase 2 with the club calendar: community trials run in November and EOIs close in January and March. A go-live during an open intake window is not acceptable. **[Decision D-02]**

## 4. Functional requirements summary

Full requirements will be written per module before each phase. The summary below sets the baseline.

### 4.1 Public website
- Section-based page builder usable by non-technical committee members, with draft, preview, scheduled publish and version history.
- News posts with categories (club, juniors, seniors, YPL, sponsors).
- Navigation editor with one level of drop-down.
- Team pages (public): team photo, coaches, fixtures, results, match reports. No child surnames or contact details by default.
- Fixtures, results and ladders: imported from Dribl where possible, otherwise CSV import. Replaces manual tables. **[Verify Dribl data access]**
- Training allocations: structured data (venue, pitch, day, time, team) rendered as a grid.
- Game-day duty roster (setup and pack-up) from the volunteer module.
- Sponsor directory with tiers, replacing the Canva site.
- Documents library with public and restricted visibility.
- Venues with maps, field status banner.
- Contact form with spam protection and routing by topic to role mailboxes.
- Club calendar and event listing with "Register" calls to action.
- Readable URLs (`/events/2027-ypl-eoi`, `/shop/2027-beanie`).

### 4.2 Members and families
- Family account: two adult guardians, multiple players, extra notification-only email addresses.
- Passwordless sign-in (email magic link) for members. Invite links for returning families.
- Member self-service: profile, players, registrations, invoices, receipts, payment plans, communication preferences.
- Player history across seasons.

### 4.3 Events, intake and registration
- One event engine for all event types: season, EOI, trial, camp, clinic, social, presentation night.
- Workflow stages per event: EOI, invited to trial, trialled, offered, accepted, registered, declined. Stages are configurable.
- Form builder with field types, conditional logic and reusable field sets.
- Fee rules engine configured in admin: base fee by age group or program, sibling discount, early-bird and date pro-rating, volunteer and coach discounts, vouchers, deposits.
- Offer letters generated from templates (PDF and email).
- T&C and policy acceptance with version, timestamp, IP and user agent recorded.
- Capacity limits and waitlists per program or age group.
- PlayFootball reconciliation: upload the PlayFootball extract, match members, report who is not registered.
- Shop step inside registration checkout (uniforms).

### 4.4 Teams
- Team builder with filters on any registration field and assessment data.
- Roles: head coach, assistant coach, team manager. Scoped to their team and season.
- Team Dugout: roster, guardian contacts (visible only to team officials), schedule, RSVP, attendance, team messages, documents.
- Working With Children Check (WWCC) number, expiry and verification status for every official. System blocks team access when a WWCC is missing or expired.

### 4.5 Communications
- Email and SMS to audiences built from filters (event, program, age group, team, role, debt status, volunteer interest).
- Saved audience groups. Message templates.
- Full message log. Delivery and bounce tracking.
- Unsubscribe handling for non-essential messages. Essential operational messages stay enabled.
- Role mailboxes: club addresses assigned to the current office holder, with automatic handover at AGM.

### 4.6 Finance
- Invoices per registration and order, split into components (registration, uniform, shop item).
- Online payment: card, Apple Pay, Google Pay through a hosted payment provider. No card data touches club systems.
- Payment plans: BECS direct debit instalments, club-defined limits and frequencies.
- Manual receipting for cash, EFT and vouchers, including part payments across components.
- Adjustments (discount, credit, write-off) with reason codes and approval for amounts above a threshold.
- Refunds with approval.
- Debtors list, aging report, bulk reminder emails.
- Revenue reports by period and payment method. Payout reconciliation.
- Xero integration: post aggregate revenue, refunds and fees to mapped accounts.
- Financial period aligned to the club year. Read-only auditor role.

### 4.7 Operations
- Shop: products, variants, stock, orders, status workflow, supplier order export.
- Volunteer roster: tasks, shifts, self sign-up, reminders, hours tracking, rebate calculation.
- Equipment register: assets, assignment to officials, lending history.
- Player assessments: configurable templates (including NPL requirements), history per player, restricted to technical staff.
- Ticketing for functions with price tiers and attendee details.
- Sponsor management: tiers, contracts, renewal dates, exposure report.

### 4.8 Administration
- Committee terms with start and end dates. Access expires at term end unless renewed.
- Role-based access control with predefined roles and per-module permissions (Section 6.3).
- Audit log of all administrative actions and all access to sensitive records.
- Data export (CSV) for each module.

## 5. Solution architecture

### 5.1 Architecture style

A **modular monolith**: one deployable application with strict internal module boundaries. This suits a small team, keeps operating cost low and avoids distributed-system overhead. Modules communicate through defined service interfaces and domain events, so a module can be extracted later if needed.

### 5.2 Logical view

```
                    +-------------------------------------------+
  Public visitors   |  Web application (Next.js, server-rendered)|
  Members  -------->|  - Public site        - Member portal     |
  Officials         |  - Team Dugout        - Admin console     |
  Committee         +--------------------+----------------------+
                                         |
                    +--------------------v----------------------+
                    |  Application modules (TypeScript)         |
                    |  identity | cms | events | registration   |
                    |  teams | comms | finance | shop |         |
                    |  volunteers | assets | assessments | audit |
                    +----+-----------+-----------+-----------+--+
                         |           |           |           |
               +---------v--+  +-----v-----+  +--v-------+  +v-----------+
               | PostgreSQL |  | Object    |  | Job      |  | Integration|
               | (primary   |  | storage   |  | queue    |  | adapters   |
               |  data)     |  | (docs,    |  | (emails, |  |            |
               |            |  |  images)  |  |  SMS,    |  |            |
               +------------+  +-----------+  |  imports)|  +-----+------+
                                              +----------+        |
       +--------------+---------------+-------------+-------------+------+
       |              |               |             |                    |
   Stripe AU     Email provider   SMS provider    Xero API       Dribl / PlayFootball
   (cards, BECS) (transactional,  (AU sender ID)  (revenue       (fixtures import,
                  bulk)                            posting)       registration CSV)
```

### 5.3 Key design principles

1. **Configuration over code** for forms, fees, workflows, roles and content, so committees do not need a developer each season.
2. **Hosted payment pages** so card data never enters club systems (PCI DSS SAQ A scope).
3. **Australian data residency** for the database, files, backups and logs.
4. **Least privilege by default**, scoped by role, team and season.
5. **Everything auditable**: who changed what, and who viewed sensitive child data.
6. **Infrastructure as code** and documented runbooks, so a new maintainer can operate the platform.

## 6. Security and privacy architecture

### 6.1 Compliance and assurance baseline

| Obligation or framework | Application |
|---|---|
| Privacy Act 1988 (Cth) and Australian Privacy Principles | Collection notice, purpose limitation, access and correction, data security (APP 11), cross-border disclosure (APP 8). Note: the club's turnover may be under the small-business threshold, but the club should comply as best practice, and privacy reforms may remove that exemption. |
| Notifiable Data Breaches scheme | Incident response plan with assessment and notification steps. |
| Children's Online Privacy Code (OAIC, in development) | Monitor and align when registered. **[Verify status]** |
| Victorian Child Safe Standards | Child safety in system design: restricted access to child data, WWCC enforcement, no public child contact details or full names without consent. |
| Football Victoria and Football Australia member protection policies | Photo consent, privacy of junior players. |
| PCI DSS v4.0 | SAQ A scope through hosted payment provider. |
| OWASP ASVS v4.0 Level 2 | Application security verification baseline. |
| ASD Essential Eight (relevant controls) | MFA, patching, restricted admin privileges, backups, application control at the platform level. |

### 6.2 Identity and authentication

| User type | Method |
|---|---|
| Members (guardians, adult players) | Email magic link or passkey. Optional password. |
| Coaches, managers, volunteers | Magic link or passkey. MFA required to see guardian contact data. |
| Committee and administrators | Passkey or TOTP MFA mandatory. Session timeout 30 minutes idle. Re-authentication for finance and bulk export actions. |
| Platform operators | Cloud console with MFA, no shared accounts, break-glass account sealed and audited. |

Controls: rate limiting and lockout on sign-in, signed short-lived magic links (15 minutes, single use), secure HTTP-only cookies, CSRF protection, session revocation on role removal.

### 6.3 Authorisation model (RBAC with scopes)

| Role | Scope | Typical permissions |
|---|---|---|
| Guardian / member | Own family | View and edit own family, register, pay, view own invoices |
| Coach / team manager | Assigned team and season | Team roster, guardian contacts, schedule, attendance, team messages |
| Technical director | Programs | Assessments, grading, team allocation |
| Registrar | Club, current season | Events, registrations, players, PlayFootball reconciliation |
| Treasurer | Club | Finance module, refunds, Xero, reports |
| Secretary | Club | Communications, documents, committee administration |
| Content editor | Club | CMS pages, news, sponsors, media |
| Volunteer coordinator | Club | Volunteer roster |
| Auditor | Club, time-limited | Read-only finance |
| System administrator | Platform | Role assignment, configuration. Cannot bypass audit log. |

All committee roles carry a term end date. Access expires automatically after the AGM unless renewed. Bulk exports of personal data need a specific permission and are logged.

### 6.4 Data protection

- Data classification: **Public**, **Internal**, **Personal**, **Sensitive** (child data, medical notes, WWCC, bank details).
- Encryption in transit: TLS 1.2 minimum, HSTS, modern cipher suites.
- Encryption at rest: provider-managed encryption for database, storage and backups. Application-level field encryption for medical notes and any bank details not held by the payment provider.
- Data minimisation: collect only fields with a stated purpose. Medical information restricted to team officials who need it.
- Retention: defined per data class (for example inactive member records archived after 7 years, EOI data for unsuccessful applicants deleted after 12 months). **[Decision D-07]**
- Photo and media consent recorded per player, and enforced on public team pages.
- Right of access and correction through the member portal. Deletion requests handled by a documented process.

### 6.5 Application and platform security

- Secure SDLC: code review on every change, dependency scanning, secret scanning, static analysis in CI.
- Content Security Policy, secure headers, input validation and output encoding.
- Web application firewall and bot protection on public forms.
- Separate environments: development, staging (synthetic data only), production.
- No production personal data in non-production environments.
- Centralised logs retained 12 months in Australia. Alerts on admin sign-in anomalies, bulk exports and failed payment webhooks.
- Independent penetration test before Phase 2 go-live (first system holding child and payment data), then annually.

### 6.6 Resilience

| Item | Target |
|---|---|
| Availability | 99.5% monthly (volunteer-supported service) |
| Recovery point objective (RPO) | 15 minutes (point-in-time database recovery) |
| Recovery time objective (RTO) | 8 hours |
| Backups | Daily snapshots, 35-day retention, monthly copy retained 12 months, restore tested each quarter |

## 7. Technology stack (proposed)

| Layer | Proposed | Rationale | Alternatives considered |
|---|---|---|---|
| Language | TypeScript (end to end) | One language for front end and back end. Large talent pool for future volunteers. | C#/.NET, Python/Django |
| Web framework | Next.js (App Router), server-side rendering | SEO for the public site, one codebase for site, portal and admin | Remix, Astro + separate API |
| UI | Tailwind CSS with an accessible component library (shadcn/ui on Radix) | WCAG 2.2 AA support, fast delivery | MUI |
| CMS | Built-in section-based CMS in the app, or Payload CMS embedded in the Next.js app **[Decision D-04]** | Payload runs inside Next.js on the same Postgres, so no extra service | Strapi, Sanity (SaaS, data offshore) |
| Database | PostgreSQL 16 | Relational integrity for finance and registrations, row-level security option | MySQL |
| ORM and migrations | Drizzle ORM or Prisma | Typed queries, versioned migrations | Raw SQL |
| Authentication | Auth.js or Better Auth with passkeys, magic links and TOTP | Open source, data stays in club database | Clerk, Auth0 (SaaS, cost per user) |
| Background jobs | Postgres-backed queue (pg-boss or Graphile Worker) | No extra infrastructure | Redis + BullMQ |
| File storage | S3-compatible storage in Sydney region | Documents, images, offer letters | Azure Blob |
| Payments | Stripe AU: Checkout or Payment Element, BECS Direct Debit, Apple Pay, Google Pay | AU entity, hosted PCI scope, direct debit support for payment plans, strong API | Square, Westpac/ Tyro, Ezidebit/ Debitsuccess for plans |
| Email | Amazon SES (Sydney) or Postmark | Low cost, deliverability tracking | SendGrid |
| SMS | Australian provider (for example MessageMedia, ClickSend or Twilio with AU sender ID) | Alphanumeric sender ID "BerwickCity" | |
| Accounting | Xero API (OAuth 2.0) | Current club accounting system **[Verify]** | MYOB |
| Hosting | Option A: Vercel (Sydney functions) + managed Postgres in Sydney. Option B: AWS ap-southeast-2 (App Runner or ECS Fargate, RDS, S3, SES) **[Decision D-03]** | A is simplest to operate. B gives full control and single-provider data residency. | Azure Australia East |
| Infrastructure as code | Terraform (Option B) or provider config in repo (Option A) | Repeatable, auditable environments | |
| CI/CD | GitHub Actions: lint, type check, tests, security scans, preview deployments, gated production deploy | Repository is already on GitHub | |
| Monitoring | Sentry (error tracking, AU data region if available) plus provider logs and uptime monitoring | | |
| Testing | Vitest (unit), Playwright (end to end), accessibility checks with axe | | |

## 8. Data model (core entities)

```
Organisation ─┬─ Season ─┬─ Program (Miniroos, Juniors, YPL, NPL, Seniors)
              │          └─ Event ─┬─ FormDefinition ── FormField
              │                    ├─ FeeRule
              │                    └─ Registration ─┬─ RegistrationPlayer
              │                                     ├─ PolicyAcceptance
              │                                     └─ WorkflowStage history
              ├─ Family ─┬─ Guardian (Person)
              │          └─ Player (Person) ── Consent (photo, medical)
              ├─ Team ─┬─ TeamMember (player)
              │        ├─ TeamOfficial (person, role) ── WWCC
              │        └─ Activity (fixture, training, social) ── RSVP / Attendance
              ├─ Invoice ── InvoiceLine ── Payment / Adjustment / Refund
              ├─ PaymentPlan ── Instalment
              ├─ Message ── MessageRecipient ── DeliveryStatus
              ├─ CommitteeTerm ── RoleAssignment ── Permission
              ├─ Page / Section / NewsPost / Document / MediaAsset
              ├─ Sponsor ── SponsorTier ── SponsorAgreement
              ├─ Product ── Variant ── Order ── OrderLine
              ├─ VolunteerTask ── Shift ── ShiftAssignment
              ├─ Asset ── AssetLoan
              ├─ AssessmentTemplate ── Assessment
              └─ AuditEvent
```

Design notes:
- `Person` is one record per human. Roles (guardian, player, coach, committee) attach to the person, so one parent can also be a coach and treasurer.
- Financial records are append-only. Corrections use adjustment and reversal entries, never edits or deletes.
- Every table that holds personal data has an owning organisation key, so multi-club use stays possible later.

## 9. Indicative operating cost (monthly, AUD)

| Item | Option A (Vercel + managed DB) | Option B (AWS) |
|---|---|---|
| Application hosting | $30 to $40 (Pro plan) | $30 to $60 |
| PostgreSQL (Sydney, with backups) | $25 to $50 | $30 to $70 |
| File storage and CDN | under $5 | under $10 |
| Email | under $5 | under $5 |
| Monitoring | $0 to $40 | $0 to $40 |
| Domain and DNS | about $3 | about $3 |
| **Subtotal (excluding SMS and payment fees)** | **about $65 to $140** | **about $75 to $190** |

SMS and payment processing are usage-based in both options. These figures are estimates for planning only and need confirmation with current vendor pricing. Penetration testing (one-off, about $5,000 to $15,000) and any paid support retainer are additional. **[Decision D-05]**

## 10. Migration and cut-over

1. Request a full data export from Majestri: members, families, players, registrations history, invoices, payments, payment plans, teams, documents, images, shop orders. Confirm contract exit terms.
2. Map and cleanse data in staging. Validate record counts and financial balances against Majestri reports.
3. Migrate content (pages, documents, images) and set up URL redirects from old Majestri paths to new paths.
4. Parallel run: new public site and new event for one intake, with Majestri retained read-only for historical finance.
5. Move active payment plans: let existing plans finish in Majestri, start new plans in the new platform. Members must re-authorise direct debits with the new provider.
6. Switch DNS for `www.berwickcitysc.com`. Keep the Majestri subdomain until historical data is no longer needed.
7. Close Majestri account and obtain written confirmation of data deletion.

## 11. Decisions register

| ID | Decision | Options | Owner |
|---|---|---|---|
| D-01 | Build custom platform or stay on Majestri (or hybrid: new public website, keep Majestri back office) | Build / Stay / Hybrid | Committee |
| D-02 | Go-live date for Phase 2 relative to the 2027 and 2028 intake windows | | Committee, Registrar |
| D-03 | Hosting option | A (Vercel) / B (AWS) | Secretary, technical lead |
| D-04 | CMS approach | Built-in / Payload CMS | Technical lead |
| D-05 | Operating budget and support model | Volunteer / paid retainer / hybrid | Treasurer, committee |
| D-06 | Payment provider and fee policy (absorb or pass on fees) | Stripe / other | Treasurer |
| D-07 | Data retention schedule | | Secretary |
| D-08 | Modules in scope (which Majestri modules are actually used) | | Committee |

## 12. Risks

| ID | Risk | Likelihood | Impact | Mitigation |
|---|---|---|---|---|
| R-01 | Key-person dependency on volunteer developers | High | High | Documentation, IaC, standard stack, paid fallback support |
| R-02 | Data breach involving children's data | Medium | Very high | Section 6 controls, pen test, incident response plan, least privilege |
| R-03 | Registration system failure during intake | Medium | High | Go-live outside intake windows, parallel run, load test, rollback to Majestri |
| R-04 | Payment reconciliation errors | Medium | High | Append-only ledger, daily payout reconciliation, treasurer UAT |
| R-05 | Scope creep beyond volunteer capacity | High | Medium | Phased delivery, decisions register, module prioritisation |
| R-06 | No API access to Dribl or PlayFootball | High | Medium | CSV import fallback, request access from Football Victoria |
| R-07 | Low adoption by committee and coaches | Medium | Medium | Simple admin UX, training guides, champions per age group |

## 13. Next steps

1. Committee review of this draft and the current state assessment.
2. Back-office walkthrough to confirm the modules in use (Section 6 of the assessment).
3. Decisions D-01, D-03, D-05 and D-08.
4. Detailed requirements for Phase 0 and Phase 1.
5. Repository scaffold, CI/CD and environments (Phase 0 start).
