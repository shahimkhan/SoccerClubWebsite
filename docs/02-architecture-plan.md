# Solution Architecture Plan: Club Website and Management Platform

| Item | Value |
|---|---|
| Document status | Draft v0.8 for committee review. Not approved for development. |
| Date | 14 November 2026 |
| Input | `docs/01-current-state-assessment.md`, including the back-office walkthrough (Section 7) |
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
| Subscription | $49/month plus $10/month domain | AWS hosting and services, estimated $135 to $285/month before nonprofit credits (Section 9) |
| Payment fees | 2.9% per payment | Stripe AU, indicative 1.7% + $0.30 domestic card, BECS direct debit lower (verify current Stripe pricing) |
| Custom forms and fees | Vendor builds on request | Club configures in admin, no vendor dependency |
| Fixtures and PlayFootball | Not integrated | Can be integrated where the source system allows |
| Support | Vendor, same day | Club volunteers or a paid support agreement. **Key risk.** |
| Security and compliance | Vendor responsibility (club still accountable for data) | Club is fully responsible |
| Continuity | Vendor continuity risk | Key-person risk. Needs documentation, IaC and a support model. |

**Recommendation:** proceed with design. Before development, confirm a support model (named maintainers plus a fallback paid support arrangement) and a funded operating budget. Run Majestri and the new platform in parallel for one intake cycle before cut-over.

## 3. Scope and phasing

The scope is grouped into modules. Each phase delivers usable value and can go live alone.

### 3.1 Module scope (from the back-office walkthrough)

The walkthrough on 14 November 2026 showed which Majestri modules the club uses (assessment Section 7.11). The scope below follows that evidence. **[Decision D-08]**

| Scope | Modules | Reason |
|---|---|---|
| **Core (must have)** | Event engine for all event categories, intake with configurable stages and offers, form builder, fee rules, team administration and officials, WWCC accreditation register, email and SMS, role addresses, invoicing, debtors, payment plans, manual receipting, refunds, credit notes, vouchers, committees and access control with MFA, website pages, documents, sponsors | In active use with high volume, or a legal or child-safety need |
| **Required, lower volume** | Ticketing (presentation nights), team photo orders, online shop (small catalogue), Xero posting, Do Not Email and delivery problem registers, notification routing, saved groups | In use, but simple or seasonal |
| **New (Majestri gap)** | Fixtures and results import (Dribl), PlayFootball reconciliation, game-day duty roster, contact form, news, club privacy collection notice, fine-grained permissions, auditor role | Fills gaps G-01, G-02, G-05, G-06, G-11, G-14 |
| **Deferred** | Volunteer hours and rebates, equipment register, service provider register, watchlist, photo albums, player assessments | Not used in Majestri now. Build only if the committee asks. |
| **Out of scope** | Competition management (Whirlpool equivalent) | Football Victoria runs competitions in Dribl |

### 3.2 Phases

| Phase | Modules | Replaces | Target window (indicative) |
|---|---|---|---|
| 0. Foundations | Identity with MFA (in-house, provider-neutral, Section 6.2), RBAC with fine-grained permissions, audit log, CMS core, transactional email service, CI/CD, environments, hosting, backups | None | Weeks 1 to 4 |
| 1. Public website | Pages, news, menu, documents, sponsors (5 tiers), venues, contact form, fixtures display, privacy collection notice | Majestri website, Canva directory, manual fixture tables | Weeks 5 to 10 |
| 2. Registration and core finance | Event engine (season, EOI, clinic, academy, term program, ticketed, photo orders), configurable intake stages, offers with expiry, form builder, fee rules (including switchover fees), family registration, T&Cs, modified-registration approval queue, PlayFootball reconciliation. Core finance: invoices, card payments, payment plans (direct debit) with approval queue, manual receipting (EFT, cash, EFTPOS, vouchers), eVouchers, adjustments, credit notes, refunds, debtors list. Transactional email: registration invites (single and bulk), offer letters, invoices, receipts and statements, with the Do Not Email and delivery problem registers. | Majestri events, intake, registrations and day-to-day financials, Google Forms | Weeks 11 to 28. Must be live and tested before the intake window it is used for. |
| 3. Teams, safeguarding and communications | Team administration, officials, WWCC accreditation register with expiry blocking, schedules, attendance, broadcast email and SMS to filtered audiences, saved groups, message log, role addresses, notification routing, game-day duty roster | Majestri teams, accreditation and broadcast comms, Google Sheets rosters | Weeks 29 to 36 |
| 4. Finance automation | Xero posting, settlement and payout reconciliation, ABA refund batch files, debtor aging and bulk chase-up, revenue and financial period reports, auditor role | Majestri Xero posting and financial reports | Weeks 37 to 42 |
| 5. Shop and optional modules | Shop (products, variants, stock, orders). Optional: volunteer hours, equipment register, player assessments, watchlist (subject to decision D-10) | Remaining Majestri modules | Weeks 43 onward |

Changes from v0.1: intake stages and offers moved into Phase 2 scope because the club uses them for every EOI (G-09). The WWCC register moved into Phase 3 with team administration. Ticketing and photo orders moved into the Phase 2 event engine because they are event categories. Volunteer roster, equipment register and assessments moved to optional because the club does not use them in Majestri.

Changes from v0.2: Phase 2 now includes core finance and transactional email. Without them Phase 2 cannot go live on its own: the club offers payment plans in every season, refunds unsuccessful EOI applicants, receipts offline payments during registration, and depends on invite and invoice emails. Phase 4 keeps only automation and reporting. Phase 2 grew by about six weeks (estimate). After Phase 2, the club can stop using Majestri for registrations and day-to-day finance. Majestri stays read-only for historical records and Xero postings until Phase 4.

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
- Passwordless sign-in (email code or magic link) for members. Invite links for returning families.
- Member self-service: profile, players, registrations, invoices, receipts, payment plans, communication preferences.
- Player history across seasons.

### 4.3 Events, intake and registration
- One event engine for all event types: season, EOI, trial, camp, clinic, social, presentation night.
- Workflow stages per event: EOI, invited to trial, trialled, offered, accepted, registered, declined. Stages are configurable. Stages replace the Majestri workaround that uses "teams" such as Shortlist, Not Invited and Unsuccessful (assessment G-09).
- Offers with an expiry date, and statuses for pending, accepted, declined, expired and cancelled.
- Approval queue for registrations that a member changes after submission.
- Form builder with field types, conditional logic and reusable field sets.
- Fee rules engine configured in admin: base fee and deposit by playing group or program, switchover fees when a player moves between playing groups (with automatic invoice adjustment), sibling discount, early-bird and date pro-rating, volunteer and coach discounts, vouchers.
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
- Payment plans: BECS direct debit instalments, club-defined limits and frequencies. New plans go to an approval queue before the first debit.
- Manual receipting for cash, EFT, EFTPOS and vouchers, including part payments across components.
- Electronic vouchers (club credit) with status and owner.
- Refund batches exported as ABA files for the club bank.
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
- Role-based access control with predefined roles and per-module permissions (Section 6.3). Majestri has only five access areas (Administration, Financials, Content, Events, Shop). The new model adds separate permissions for refunds, exports and child data.
- Import the Majestri Do Not Email register and delivery problem list at migration.
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
               | (RDS,      |  | storage   |  | queue    |  | adapters   |
               |  Sydney)   |  | (S3:      |  | (worker: |  |            |
               |            |  |  docs,    |  |  emails, |  |            |
               +------------+  |  images)  |  |  SMS,    |  +-----+------+
                               +-----------+  |  imports)|        |
                                              +----------+        |
       +--------------+---------------+-------------+-------------+------+
       |              |               |             |                    |
   Stripe AU     Email provider   SMS provider    Xero API       Dribl / PlayFootball
   (cards, BECS) (transactional,  (AU sender ID)  (revenue       (fixtures import,
                  bulk)                            posting)       registration CSV)
```

### 5.3 Hosting architecture: AWS Sydney (decision D-03)

The platform runs on Amazon Web Services in the Asia Pacific (Sydney) Region, `ap-southeast-2`. All personal data at rest, backups and logs stay in this region.

```
 Users ──> Route 53 ──> CloudFront + AWS WAF
                           │            │
                           │            └──> S3 (static assets, cached pages)
                           v
                  Lambda (Next.js server, via OpenNext) ──┐
                  Lambda (image optimisation)             │   private subnets
                                                          │
   SQS queues ──> Lambda workers (jobs) ──────────────────┤
   EventBridge Scheduler ──> SQS                          │
                                                          v
                  RDS PostgreSQL (encrypted, PITR)    NAT Gateway ──> Internet
                  S3 (restricted documents)              (Stripe, Xero, SES,
                  via S3 gateway endpoint                 SMS, Dribl)
```

| Component | AWS service | Notes |
|---|---|---|
| DNS | Route 53 | DNSSEC on. Health checks. |
| CDN and edge security | CloudFront with AWS WAF (managed rule groups for common threats, known bad inputs, IP reputation and bot control, plus rate-based rules) | Single entry point. Caches public pages and assets only. Pages with personal data are not cached. |
| TLS certificates | AWS Certificate Manager | Automatic renewal. |
| Web application | AWS Lambda running the Next.js server, built with OpenNext. Lambda function URL as the CloudFront origin, restricted with CloudFront Origin Access Control so only CloudFront can call it. | No load balancer and no always-on servers. Pay per request. |
| Static assets and page cache | S3 bucket behind CloudFront. OpenNext revalidation uses DynamoDB and SQS. | |
| Image optimisation | OpenNext image optimisation Lambda | |
| Background jobs | Amazon SQS queues with Lambda consumers. EventBridge Scheduler for timed jobs (payment plan runs, reminders, nightly reconciliation). Dead-letter queues with alarms. | Email and SMS sending, imports, PDF generation. Bulk work is split into batches to stay inside the Lambda 15-minute limit. |
| Database | Amazon RDS for PostgreSQL 16, encrypted with KMS, automated backups with point-in-time recovery | Single-AZ (decision D-11). Private subnets only. Recovery from an Availability Zone failure is by point-in-time restore into another zone, within the 8-hour RTO. Lambda connection pool limited per function and reserved concurrency set, to protect database connections. RDS Proxy is an option if connection limits are reached. |
| File storage | Amazon S3 (Sydney): separate buckets for public assets and restricted documents. Block Public Access on restricted buckets, SSE-KMS, versioning. | Restricted documents (offer letters, invoices, medical forms) served through short-lived signed URLs. |
| Email | Amazon SES (Sydney) with DKIM, SPF and DMARC on `berwickcitysc.com` | Bounce and complaint handling feeds the Do Not Email register. |
| Secrets and keys | AWS Secrets Manager and KMS | Database credentials, TOTP encryption key, Stripe and Xero keys. Automatic rotation for database credentials. |
| Logs and monitoring | CloudWatch Logs (12-month retention), CloudWatch alarms (errors, throttles, queue age, dead-letter queues), Sentry for application errors | Alerts to the technical lead and Secretary. |
| Security services | CloudTrail (all accounts), GuardDuty, Security Hub (AWS Foundational Security Best Practices), AWS Config, IAM Access Analyzer | Supports the Essential Eight and audit evidence. |
| Backups | AWS Backup plan for RDS, S3 and DynamoDB, with a copy in a separate backup vault | Quarterly restore test. |
| Infrastructure as code | Terraform in the repository, with the OpenNext build output deployed as Lambda functions. Applied through GitHub Actions with OIDC (no long-lived AWS keys). | SST is an alternative for the application layer. Confirm in Phase 0. |

**Account and access structure**
- AWS Organizations with separate accounts: management, production, non-production (development and staging), and log archive.
- Service control policy restricts all accounts to `ap-southeast-2`, except the global services CloudFront, WAF for CloudFront and ACM certificates for CloudFront, which use `us-east-1`.
- IAM Identity Center for human access, MFA required, no IAM users with long-lived keys. Root accounts secured with hardware MFA and not used.
- Two named administrators. Break-glass access sealed and audited.
- Each Lambda function has its own least-privilege IAM role.
- AWS Budgets alerts at 50%, 80% and 100% of the monthly budget.

**Network design**
- VPC across two Availability Zones. Private subnets hold the Lambda functions and RDS. Public subnets hold only the NAT Gateway.
- **One managed AWS NAT Gateway** gives private Lambda functions outbound internet access (Stripe, Xero, SES, Secrets Manager, SMS provider, Dribl). It is fully managed by AWS: no server to patch.
- An S3 gateway endpoint (no charge) keeps S3 traffic off the NAT Gateway and lowers data processing charges.
- One NAT Gateway in one Availability Zone keeps cost down. If that zone fails, outbound calls (payments, email) stop until the zone recovers or a second NAT Gateway is deployed from Terraform. A second NAT Gateway in the other zone gives full resilience but doubles the NAT cost. Decision D-11: one NAT Gateway for now.
- Security groups allow only Lambda to database and Lambda to outbound HTTPS (port 443).

**Redundancy-ready design (build once, scale by configuration)**

The first release runs with a single RDS instance and one NAT Gateway. The design must let the club add redundancy later by changing Terraform variables, not by redesign or data migration.

| Rule | Implementation |
|---|---|
| Two Availability Zones from day one | The VPC, private subnets, public subnets and RDS subnet group span two zones now, even though only one NAT Gateway and one database instance run. |
| Redundancy as variables | Terraform variables `rds_multi_az` (default `false`), `nat_gateway_count` (default `1`, allowed `1` or `2`), `rds_instance_class`, `lambda_reserved_concurrency`. Private route tables are defined per zone so a second NAT Gateway attaches without changes to other resources. |
| Stateless compute | Lambda functions keep no local state. Sessions, cache and job state live in RDS, DynamoDB, S3 or SQS. |
| Idempotent jobs | Every SQS job has an idempotency key, so retries and failover do not send duplicate emails or charge twice. |
| Database endpoint by name | The application reads the RDS endpoint and credentials from Secrets Manager. A move to Multi-AZ, a read replica or RDS Proxy needs no code change. |
| Environment parity | Development, staging and production use the same Terraform modules with different variable files. |
| Upgrade path documented | Runbook steps for enabling Multi-AZ RDS, a second NAT Gateway, RDS Proxy and provisioned concurrency, with the cost of each. |

**Lambda design constraints**
- Cold starts add up to about one second to the first request after idle time. A scheduled warmer (included in OpenNext) keeps the server function warm in busy periods. Provisioned concurrency is an option during intake if needed.
- 15-minute maximum run time per invocation. Long work runs through SQS in batches.
- Function package size limits. Keep server dependencies lean. Confirm with the CMS choice in Phase 0.

**AWS nonprofit credits.** AWS offers credits for eligible nonprofits through its nonprofit program, delivered in Australia through TechSoup partners. Eligibility usually depends on charity or not-for-profit registration (for example ACNC registration). Confirm that the club, as an incorporated association, is eligible and the credit amount before the budget is approved. **[Action for D-05]**

### 5.4 Key design principles

1. **Configuration over code** for forms, fees, workflows, roles and content, so committees do not need a developer each season.
2. **Hosted payment pages** so card data never enters club systems (PCI DSS SAQ A scope).
3. **Australian data residency** for the database, files, backups and logs. Identity data is held in the same database. A future identity provider must use an Australian region.
4. **Least privilege by default**, scoped by role, team and season.
5. **Everything auditable**: who changed what, and who viewed sensitive child data.
6. **Infrastructure as code** (Terraform) and documented runbooks, so a new maintainer can operate the platform.

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
| Members (guardians, adult players) | Email code, magic link or passkey. No password by default. |
| Coaches, managers, volunteers | Email code, magic link or passkey. MFA required to see guardian contact data. |
| Committee and administrators | Passkey or TOTP MFA mandatory. Session timeout 30 minutes idle. Re-authentication for finance and bulk export actions. |
| Platform operators | Cloud console with MFA, no shared accounts, break-glass account sealed and audited. |

Players under 18 do not get sign-in accounts. Guardians act for them.

**Identity approach: in-house now, Auth0 as the target option (decision D-09)**

The platform starts with in-house authentication. Auth0 (Australia region) stays the target option until its plan and pricing for MFA and passkeys are confirmed. The design lets the club switch providers without changing application code.

*Interim: in-house authentication with Better Auth*

| Factor | Assessment |
|---|---|
| Library | Better Auth (open source, TypeScript), running inside the Next.js application. Pin the version and monitor security advisories. |
| Data residency | All identity data (users, sessions, factors) is in the Sydney PostgreSQL database. No overseas identity processor. |
| Methods | Email one-time code and magic link (members), passkeys (WebAuthn), TOTP authenticator app and recovery codes (MFA). No passwords by default. |
| Cost | No licence cost. Uses the existing database and email service. |
| Trade-off | The club owns sign-in security: configuration, patching, abuse protection and incident response. This adds to risk R-01 and needs penetration test coverage. |

Security controls for in-house authentication (OWASP ASVS v4.0 Level 2, chapters V2 and V3):
1. One-time codes and magic links: single use, 10 to 15 minute expiry, stored as hashes, invalidated when a new one is issued.
2. Rate limiting per IP address and per email address on sign-in, code entry and code requests. Bot protection on the sign-in form.
3. Responses do not reveal whether an email address has an account.
4. Sessions stored server side, in HTTP-only, Secure, SameSite cookies. Idle timeout 30 minutes for committee roles. Session rotation at sign-in and at privilege change. Revocation of all sessions when a role is removed or a factor changes.
5. MFA (passkey or TOTP) mandatory for committee and team officials. Step-up re-authentication for finance, exports and role changes.
6. TOTP secrets encrypted at rest with an application key held in the cloud secrets manager.
7. Audit events for sign-in success and failure, MFA enrolment and removal, recovery code use and session revocation. Alerts on unusual patterns.
8. Admin-assisted account recovery requires two committee members and is logged.
9. Penetration test scope includes authentication before Phase 2 go-live.

*Provider-neutral design (switch to Auth0 later)*
- All application code calls an internal identity module (`currentPerson`, `requireRole`, `requireStepUp`). No module calls Better Auth or Auth0 directly.
- An `identity_link` table maps each internal person to an external identity (provider, subject ID). The person record never depends on the provider.
- Authorisation (roles, committee terms, team scopes) stays in the application database under both options.
- Migration path: create the Auth0 tenant in the Australia region, import users by email, switch the identity module adapter, and ask users to sign in by email code. Passkeys and TOTP must be enrolled again after the switch, so schedule it outside intake windows.

*Target option: Auth0 by Okta, Australia region*

| Factor | Assessment |
|---|---|
| Fit | Official Next.js SDK (`@auth0/nextjs-auth0`). Hosted Universal Login. Passwordless, passkeys, TOTP, breached-password detection and bot protection managed by Auth0. |
| Data residency | Australia region available (`*.au.auth0.com`). Region is fixed at tenant creation. Confirm sub-processors in the Auth0 Data Processing Addendum. |
| Cost | **Open item.** The free plan covers the club's user numbers, but sources disagree on which plan includes MFA and passkeys. If committee MFA needs the B2C Professional plan, the cost is about US$240 a month. Confirm pricing and ask about nonprofit pricing (Okta for Good). |
| Conditions | Australia region for all tenants, separate tenants per environment, data minimisation (email, user ID and factors only), no club permissions in Auth0, MFA policy for committee and officials, sign-in logs streamed to the club's log store, two named tenant administrators. |

Switch criteria: the committee moves to Auth0 when the plan with MFA and passkeys fits the approved budget (decision D-05), or earlier if the club cannot resource the in-house controls above.

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

Mapping from Majestri: Administration maps to System administrator and Secretary. Financials maps to Treasurer. Content maps to Content editor. Events maps to Registrar and event co-ordinator. Shop maps to a new Shop manager role. The per-member "Hide Contact Details" flag becomes a privacy setting on the person record.

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
| Authentication | Interim: Better Auth (in-house) with email codes, magic links, passkeys and TOTP, behind a provider-neutral identity module. Target: Auth0 Australia region when pricing is confirmed. **[Decision D-09]** | No licence cost, all identity data in Sydney, clean path to a managed provider | Auth0 (target), Clerk (rejected: US hosting only), Microsoft Entra External ID, Auth.js (no built-in MFA) |
| Background jobs | Amazon SQS with Lambda consumers, EventBridge Scheduler for timed jobs, dead-letter queues | Native serverless, pay per use, retries built in | Postgres-backed queue (needs an always-on worker) |
| File storage | Amazon S3 in Sydney, separate public and restricted buckets | Guaranteed Australian residency, encryption, versioning, signed URLs | |
| Payments | Stripe AU: Checkout or Payment Element, BECS Direct Debit, Apple Pay, Google Pay | AU entity, hosted PCI scope, direct debit support for payment plans, strong API | Square, Westpac/ Tyro, Ezidebit/ Debitsuccess for plans |
| Email | Amazon SES (Sydney) | Low cost, Australian region, native to the hosting platform | Postmark |
| SMS | Australian provider (for example MessageMedia, ClickSend or Twilio with AU sender ID) | Alphanumeric sender ID "BerwickCity" | |
| Accounting | Xero API (OAuth 2.0) | Current club accounting system **[Verify]** | MYOB |
| Hosting | **AWS Sydney (`ap-southeast-2`), serverless**: Lambda (Next.js via OpenNext), CloudFront, AWS WAF, RDS PostgreSQL, S3, SQS, SES, Route 53, one managed NAT Gateway (Section 5.3). **[Decision D-03: decided]** | Full Australian data residency, no always-on servers, pay per use, possible nonprofit credits | ECS Fargate (higher fixed cost), Cloudflare (no residency guarantee without Enterprise), Vercel |
| Infrastructure as code | Terraform with the OpenNext build output, applied from GitHub Actions with OIDC federation to AWS | Repeatable, auditable environments, no long-lived cloud keys | SST, AWS CDK |
| CI/CD | GitHub Actions: lint, type check, tests, security and container image scans, deploy to staging, gated production deploy to Lambda | Repository is already on GitHub | AWS CodePipeline |
| Monitoring | Sentry (application errors), CloudWatch Logs and alarms, Route 53 health checks | | |
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

| Item | AWS Sydney, serverless (before nonprofit credits) |
|---|---|
| Lambda (Next.js server, image optimisation, workers). Monthly free allowance likely covers most club traffic. | $0 to $15 |
| RDS PostgreSQL (Single-AZ, db.t4g.micro or small, 20 GB, backups). Multi-AZ would about double this line. | $25 to $60 |
| NAT Gateway, one Availability Zone (hourly charge plus data processing). A second gateway would double this line. | $70 to $85 |
| CloudFront and AWS WAF (web ACL and rules) | $15 to $30 |
| S3, SQS, DynamoDB, SES, Route 53, Secrets Manager | $5 to $15 |
| CloudWatch, GuardDuty, Security Hub, Config, CloudTrail | $15 to $35 |
| Monitoring (Sentry) | $0 to $40 |
| Identity (in-house now; Auth0 later $0 to about $370, see Section 6.2) | $0 |
| Domain registration | about $3 |
| **Subtotal (excluding SMS and payment fees)** | **about $135 to $285** |

AWS nonprofit credits, if approved, offset this cost for the credit period. The NAT Gateway is the largest fixed line. A later move to Auth0 adds $0 to about $370 a month, depending on the plan that includes MFA. SMS and payment processing are usage-based. These figures are estimates for planning only and need confirmation with current vendor pricing. Penetration testing (one-off, about $5,000 to $15,000) and any paid support retainer are additional. **[Decision D-05]**

## 10. Migration and cut-over

1. Request a full data export from Majestri: members, families, players, registrations history, invoices, payments, payment plans, teams, documents, images, shop orders. Confirm contract exit terms.
2. Map and cleanse data in staging. Validate record counts and financial balances against Majestri reports.
3. Migrate content (pages, documents, images) and set up URL redirects from old Majestri paths to new paths.
4. Parallel run: new public site and new event for one intake, with Majestri retained read-only for historical finance.
5. Move active payment plans: let existing plans finish in Majestri, start new plans in the new platform. Members must re-authorise direct debits with the new provider.
6. Move DNS for `berwickcitysc.com` to Route 53 in advance (records unchanged), then switch the `www` record to the new platform. Keep the Majestri subdomain until historical data is no longer needed.
7. Close Majestri account and obtain written confirmation of data deletion.

## 11. Decisions register

| ID | Decision | Options | Owner |
|---|---|---|---|
| D-01 | Build custom platform or stay on Majestri (or hybrid: new public website, keep Majestri back office) | Build / Stay / Hybrid | Committee |
| D-02 | Go-live date for Phase 2 relative to the 2027 and 2028 intake windows | | Committee, Registrar |
| D-03 | Hosting option | **Decided: AWS Sydney** (Section 5.3) | Secretary, technical lead |
| D-11 | Database and network availability | **Decided: Single-AZ RDS and one NAT Gateway.** Review after the first intake on the new platform. Upgrade path: Multi-AZ RDS and a second NAT Gateway, both by Terraform change. | Treasurer, technical lead |
| D-04 | CMS approach | Built-in / Payload CMS | Technical lead |
| D-05 | Operating budget and support model | Volunteer / paid retainer / hybrid | Treasurer, committee |
| D-06 | Payment provider and fee policy (absorb or pass on fees) | Stripe / other | Treasurer |
| D-07 | Data retention schedule | | Secretary |
| D-08 | Modules in scope (which Majestri modules are actually used) | Evidence collected on 14 Nov 2026 (assessment Section 7.11). Proposed scope in Section 3.1: core, required, new, deferred and out of scope. Committee to confirm the deferred list and Xero status. | Committee |
| D-09 | Identity provider | **Decided (interim): in-house authentication with Better Auth**, behind a provider-neutral identity module. Target: Auth0 Australia region when the plan with MFA and passkeys fits the budget. Clerk rejected (no Australian region). | Secretary, technical lead |
| D-10 | Watchlist (people of concern) in scope | Build in Phase 3 / defer / out of scope. Not used in Majestri today, but it is a child-safety control. | Child Safety Officer, committee |

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
| R-08 | Larger Phase 2 delays the first registration go-live | Medium | High | Fix the target intake early (D-02), keep Majestri for the next intake if Phase 2 is not ready, cut non-essential Phase 2 items before core finance |
| R-09 | Weakness in in-house authentication | Medium | High | ASVS V2 and V3 controls (Section 6.2), maintained library, version pinning, advisory monitoring, penetration test, switch criteria for Auth0 |
| R-10 | AWS costs exceed budget, or Lambda and OpenNext limits block a feature | Medium | Medium | AWS Budgets alerts, right-size the database, S3 gateway endpoint, Phase 0 proof of concept, keep code portable to a container host |
| R-11 | Availability Zone outage stops payments and email (single NAT Gateway) or the database (Single-AZ RDS) | Low | Medium | Documented runbook: deploy a NAT Gateway in the other zone by Terraform and restore RDS into the other zone from point-in-time backup. Rehearse once before Phase 2 go-live. Review D-11 after the first intake. |

## 13. Next steps

1. Committee review of this draft and the current state assessment.
2. Back-office walkthrough to confirm the modules in use. **Done** (assessment Section 7). Follow-up: the Treasurer confirms the Xero status and supplies revenue and SMS volumes.
3. Decisions D-01, D-05, D-08 and D-10. Check eligibility for AWS nonprofit credits (TechSoup) and apply. Confirm the Auth0 plan that includes MFA and passkeys, and ask about nonprofit pricing, to decide when to switch from in-house authentication (D-09 follow-up).
4. Treasurer confirms the Xero connection status now (assessment G-10). This is an operational risk outside the project.
5. Detailed requirements for Phase 0 and Phase 1.
6. Proof of concept per `docs/03-poc-plan.md`. Then Phase 0: set up AWS Organizations, accounts, guardrails and Terraform baseline. Proof of concept for Next.js on Lambda with OpenNext, Better Auth, Drizzle with RDS (connection limits), SQS workers, SES, Stripe webhooks, PDF generation and the CMS choice.
7. Repository scaffold, CI/CD and environments (Phase 0 start).
