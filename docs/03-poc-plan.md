# Proof of Concept Plan

| Item | Value |
|---|---|
| Document status | Draft v0.1 |
| Date | 14 November 2026 |
| Input | `docs/02-architecture-plan.md` v0.8 |
| Purpose | Prove that the selected architecture works end to end before Phase 0 and Phase 1 development starts |

---

## 1. Objectives

The proof of concept (PoC) answers one question: **can the selected stack run the club's critical flows on AWS Sydney, securely and within budget?**

| ID | Objective |
|---|---|
| O-1 | Next.js (App Router) runs on AWS Lambda through OpenNext, behind CloudFront and AWS WAF, in `ap-southeast-2`. |
| O-2 | In-house authentication (Better Auth) works on Lambda: email code sign-in, passkey and TOTP MFA, server-side sessions. |
| O-3 | Lambda connects to Single-AZ RDS PostgreSQL in private subnets with controlled connection use (Drizzle ORM). |
| O-4 | Outbound calls reach Stripe, SES and one external API through the single NAT Gateway. |
| O-5 | Background jobs run through SQS and Lambda workers, with retries, idempotency and a dead-letter queue. |
| O-6 | A family can register a player for a test event and pay a test fee with Stripe (test mode). The invoice and payment record are correct. |
| O-7 | A PDF (offer letter or invoice) is generated and stored in a restricted S3 bucket, then downloaded by signed URL. |
| O-8 | The infrastructure is fully defined in Terraform, with redundancy variables (`rds_multi_az`, `nat_gateway_count`) working in both settings. |
| O-9 | The CMS approach (decision D-04) is confirmed by building one editable page with sections. |

## 2. Scope

### 2.1 In scope

| Area | PoC deliverable |
|---|---|
| Infrastructure | One AWS account (non-production). VPC across two zones, one NAT Gateway, S3 gateway endpoint, Single-AZ RDS (db.t4g.micro), S3 buckets, SQS queues, SES (sandbox), CloudFront, AWS WAF with managed rules, Secrets Manager, CloudWatch alarms, CloudTrail, GuardDuty. |
| Delivery | Terraform modules, GitHub Actions pipeline with OIDC (build, test, deploy to the PoC environment). |
| Application | Next.js app with: home page (CMS sections), sign-in, member area, one test event with a registration form, fee calculation (base fee plus sibling discount), Stripe Checkout, invoice record, admin list of registrations, CSV export, PDF offer letter. |
| Security | MFA for an admin user, role check on the admin area, audit log entries, WAF rate rule on sign-in, signed S3 URLs. |
| Data | Synthetic data only. |

### 2.2 Out of scope

- Real member data or Majestri data migration.
- Production account, AWS Organizations structure and service control policies (Phase 0).
- Multi-AZ RDS and a second NAT Gateway (tested only as a Terraform plan in a short trial, then removed).
- Payment plans, Xero, SMS, teams, shop, full CMS, design system and branding.
- Penetration test (planned before Phase 2 go-live).

## 3. Success criteria

| ID | Criterion | Target |
|---|---|---|
| S-1 | End-to-end flow: sign in, register a player, pay by Stripe test card, receive invoice email, admin sees the registration | Works without manual steps |
| S-2 | Page response time from Melbourne (warm) | Under 1 second for server-rendered pages |
| S-3 | Cold start of the Next.js server function | Under 3 seconds, and the warmer keeps busy periods warm |
| S-4 | Database connections under a load test of 50 concurrent users | No connection errors; peak connections below 50% of the RDS limit |
| S-5 | SQS job retry | A failed email job retries and does not send a duplicate |
| S-6 | Security checks | No high findings from dependency scan, static analysis and an OWASP ZAP baseline scan. MFA enforced for admin. |
| S-7 | Infrastructure | `terraform apply` builds the environment from empty. `terraform destroy` removes it. Changing `nat_gateway_count` to 2 and `rds_multi_az` to true produces a valid plan with no resource replacement other than the expected additions. |
| S-8 | Cost | PoC environment costs under A$150 a month. AWS Budgets alert set. |
| S-9 | Documentation | README with setup steps, architecture diagram as built, decision log updates, list of issues found |

## 4. Work breakdown and sequence

| Step | Work | Output | Estimate |
|---|---|---|---|
| 1 | Repository scaffold: Next.js, TypeScript, lint, test tooling, folder structure by module | Running local app | 2 days |
| 2 | Terraform baseline: VPC (2 zones), NAT Gateway, S3 endpoint, RDS, S3, Secrets Manager, CloudTrail, GuardDuty, Budgets | PoC environment | 3 days |
| 3 | OpenNext build and Lambda deployment behind CloudFront and WAF. GitHub Actions with OIDC. | Deployed home page | 3 days |
| 4 | Database schema (person, family, player, event, registration, invoice, payment, audit) and Drizzle migrations | Migrations run in pipeline | 2 days |
| 5 | Better Auth: email code sign-in, sessions, TOTP and passkey MFA, role check | Sign-in working on Lambda | 3 days |
| 6 | Registration flow: event, form, fee calculation, Stripe Checkout, webhook, invoice | End-to-end payment | 4 days |
| 7 | SQS workers: email via SES, PDF generation to restricted S3, idempotency, dead-letter queue | Jobs working | 3 days |
| 8 | CMS spike: one page with editable sections (built-in approach), confirm decision D-04 | Recommendation | 2 days |
| 9 | Load test, security scans, redundancy variable test, cost check | Results against Section 3 | 2 days |
| 10 | PoC report and go or no-go recommendation | Report to committee | 1 day |
| | **Total** | | **about 25 working days (5 to 6 weeks part-time)** |

## 5. Environment and access

| Item | Value |
|---|---|
| AWS account | One non-production account, `ap-southeast-2`. MFA on root and all users. Access through IAM Identity Center. |
| Domain | Subdomain for the PoC, for example `poc.berwickcitysc.com`, or the CloudFront default domain |
| Stripe | Test mode only |
| SES | Sandbox mode, verified test addresses only |
| Data | Synthetic only. No real member or child data. |
| Cost control | AWS Budgets alert at A$100 and A$150. Environment can be destroyed between work sessions if needed (RDS snapshot first). |

## 6. Risks to the PoC

| Risk | Response |
|---|---|
| OpenNext or Lambda limits block a Next.js feature | Record the limit. Fallback is the same container on ECS Fargate (portable code). |
| Better Auth passkeys or TOTP do not work as expected on Lambda | Record the issue. Fallback is Auth0 (target option) for the PoC only. |
| RDS connection exhaustion under load | Lower pool size, set reserved concurrency, test RDS Proxy. |
| AWS nonprofit credits not yet approved | PoC cost is low. Proceed on the club's account within the budget alert. |

## 7. Decisions needed to start the PoC

| Item | Owner |
|---|---|
| Approve the PoC and its budget (under A$150 a month for about 2 months) | Committee, Treasurer |
| Create the AWS non-production account (or approve creation) and name two administrators | Secretary |
| Create a Stripe account in test mode | Treasurer |
| Confirm the PoC subdomain | Secretary |
