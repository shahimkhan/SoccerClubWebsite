# Current State Assessment: berwickcitysc.com (Majestri)

| Item | Value |
|---|---|
| Document status | Draft v0.1 for committee review |
| Date | 14 November 2026 |
| Scope | Public website and Majestri back-office feature set |
| Method | Review of the public site (15 pages) and the public Majestri product pages (features, pricing, FAQs, club-year pages, showcase) |
| Limitation | The authenticated back office was not reviewed. Back-office features in Section 4 come from Majestri's published material. Items marked **[Verify]** need confirmation by a committee member with admin access. |

---

## 1. Platform summary

| Attribute | Finding |
|---|---|
| Vendor | Majestri Pty Ltd, Brisbane. In operation since 2011. |
| Hosting | Vendor-hosted SaaS. Vendor states club data is stored in Australia. |
| Technology indicators | ASP.NET WebForms (`Login.aspx`, `frmMain` post-back form). Custom front-end framework (`/ody/js/odyssey.js`). |
| Tenant identifier | Organisation ID `409` (path `/hosted/org/409/`). Alternate host `berwickcitysc.majestri.com.au`. |
| Custom domain | `www.berwickcitysc.com` (BYO domain add-on, TLS managed by vendor). |
| Analytics | Google Analytics 4 (`G-XG73Z40W6N`). |
| Cost model | $49/month subscription. 2.9% per payment for the payment processing service. SMS at 6.6c per credit. BYO domain $10/month. All AUD incl. GST. |
| Legal documents | Privacy policy and terms are Majestri documents (`legal.majestri.com.au`), not club documents. |

## 2. Public website inventory

### 2.1 Site map

| Menu | Page | Path | Content type |
|---|---|---|---|
| Home | Home | `/home` | Static CMS sections: welcome, YPL EOI, Back to Berwick, business directory, volunteering |
| Home | Back to Berwick | external | Google Form (`forms.gle`) for past-player database |
| Home | Business Directory | external | Canva site (`berwickcitysc.my.canva.site`) |
| Season 2027 | Miniroos and Juniors | `/season2027` | Static program information, links to grading policy |
| Season 2027 | JB-NPL | `/jbnplseason2025` | Static program and coach information |
| Season 2027 | Training Allocations | `/trainingallocationsseason2026` | Manually maintained HTML table (pitch by day and time) |
| Season 2027 | Weekly Fixtures | `/weeklyfixtures` | Manually maintained HTML table, plus a published Google Sheet for the setup and pack-up roster |
| What's On | What's On | `/whats-on` | Majestri event listing with "Register Now" buttons |
| Seniors Fixtures | 2026 Men's / Women's Fixtures | `/2026mensfixtures`, `/2026womensfixtures` | Mostly empty CMS page (content probably embedded or image) **[Verify]** |
| About | Contact | `/contact` | Secretary email, two venues with Google Maps embeds. No contact form. |
| About | Breaking Barriers Project | `/breakingbarriersproject` | Static content, YouTube links |
| About | Coaches & TM Corner | `/coachescorner` | Static guidance, PDF downloads (Document Vault and FV links) |
| About | Committee 2026 | `/committee2024` | Static table of roles, names and role email addresses |
| About | History | `/history` | Static content |
| About | Policies and Governance | `/policiesandgovernance` | Static list of policy PDFs (Document Vault) |
| Online Shop | Online Shop | `/shop` | Majestri shop: categories, products, variants, cart |
| Login | Login | `/Login.aspx` | Email and password sign-in, "Sign in another way" option |

### 2.2 Dynamic platform functions visible on the public site

| Function | Evidence | Notes |
|---|---|---|
| Event landing pages | `event-landing?enc=...` | Event description, close date, T&Cs, Code of Conduct links |
| Registration entry | `rego-choose-path?enc=...` | Starts EOI or registration workflow |
| Online shop | `shop`, `product-landing`, `add-to-cart` | Product variants (for example colour), cart |
| Document Vault | `/hosted/org/409/docs/<id>/<file>.pdf` | Public policy and coach documents |
| Image hosting | `/hosted/org/409/imgs/`, `/hosted/gen/maxWxH/` | Server-side image resizing |
| Sponsor footer | Every page | Tiers: Major, Gold, Production Partner, Business Buddies, Player Sponsors |
| Member login | `Login.aspx` | Members, coaches, managers and committee |

### 2.3 Current events (What's On, November 2026)

| Event | Type | Closes |
|---|---|---|
| EOI 2027: YPL Teams Only | EOI, invitation-only trials | 22 Jan 2027 |
| EOI 2027: Community Teams | EOI, new players only | 30 Mar 2027 |
| EOI 2027: Senior Men's Reserves | EOI | 26 Nov 2026 |

### 2.4 External dependencies and integrations

| Dependency | Purpose | Integration type |
|---|---|---|
| PlayFootball (Football Australia) | Mandatory player, coach and manager registration and insurance | None. Members register separately. Majestri compares against a PlayFootball extract. |
| Dribl (Football Victoria) | Competition fixtures, results, team sheets | None found. Fixtures are re-keyed into HTML tables. |
| Google Forms / Google Sheets | Past-player sign-up, game-day roster | External links and published sheet |
| Canva | Business directory | External link |
| YouTube, Facebook, Instagram | Media and social | External links |
| Google Maps | Venue maps | Embedded iframes |
| Xero | Accounting | Majestri revenue posting **[Verify if used by the club]** |

## 3. Observations on the public site

1. **Manual data entry.** Fixtures and training allocations are re-keyed tables. This is a high-effort, error-prone weekly task.
2. **Fragmented tools.** Google Forms, Google Sheets and Canva fill gaps in the platform. Data collected there is outside the club's main system and outside its access controls.
3. **No news or blog function in use.** Updates appear as long sections on the home page.
4. **Stale URLs.** Page slugs do not match titles (`/committee2024` shows Committee 2026, `/jbnplseason2025`).
5. **No contact form.** Only a mailto link.
6. **No structured team pages** on the public site, although Majestri supports them. **[Verify]**
7. **Accessibility and SEO.** Opaque encrypted query strings (`enc=`) for events and products. Limited semantic structure.
8. **Privacy notice.** The site uses the vendor privacy policy. The club does not publish its own privacy collection notice, although it collects data about children.

## 4. Back-office feature set (Majestri, per vendor material)

The back office is organised into a "Control Room" with tabs. The tables below are the baseline for feature parity.

### 4.1 Administration and security

| Feature | Description |
|---|---|
| Committees | A committee is a time-bound set of administrators. The term ends at the next AGM. |
| Fine-grained permissions | Per-person access to each area of the system. |
| Coach and manager roles | Elevated access scoped to their own team. |
| Auditor access | Temporary access to the financial module. |
| Committee history | Past committees are retained. |
| Notifications | Configurable email notifications to officials when system events occur. |

### 4.2 Intake, events and registration

| Feature | Description |
|---|---|
| Event engine | One setup model for all events: season, EOI, trials, camps, clinics, presentation nights, social events. |
| Multi-stage intake | EOI to trial to offer to registration, with cohort tracking. |
| Letters of offer | Personalised offer letters for successful trialists. |
| Custom registration forms | Per-event fields and dropdowns (vendor builds these). |
| Custom fee calculators | Per-event rules: sibling discounts, sliding scales, date-based pro-rating (vendor builds these). |
| Invite-based registration | Email invite with a direct link. No username or password needed for returning members. |
| Family model | One registration per family, two adult contacts, multiple players, extra one-way email recipients for split families. |
| T&C acceptance | Each T&C is a checkbox. Date, time, IP and browser are recorded. |
| Association compliance | Prompts members to register with PlayFootball. Compares members against a PlayFootball extract. |
| Fee modeller | Checks that correct association fees are passed upstream. |
| Sporting vouchers | Government vouchers treated as revenue. |
| Event dashboard | Live progress of each event. Filtered registration lists, 4 CSV export options. |
| Bulk actions | Financial adjustments, chase-up, email on selected registrations. |

### 4.3 Players and teams

| Feature | Description |
|---|---|
| Player records | Full player history across events and seasons. |
| Player filters | Filter by any registration form field. |
| Player assessments | Configurable assessment framework, NPL/SAP compliant, history over time. |
| Grading support | Assessment data informs team allocation. |
| Team builder | Create teams per event, allocate players, install coaches and managers. |
| Team Dugout | Private team area: roster with contacts, schedule, RSVPs, attendance register, team email and SMS. |
| Team Page | Public team page with content and match reports. |
| Team scheduling | Team calendar for fixtures, training and social events. |

### 4.4 Communications

| Feature | Description |
|---|---|
| Email and SMS | Broadcast and targeted messages to any audience built from the member base. |
| Saved groups | Permanent communication groups. |
| Role email register | Club role addresses "leased" to the current office holder. |
| Message log | All outbound messages logged and visible to administrators. |
| Unsubscribe register | Central opt-out register. |

### 4.5 Finance

| Feature | Description |
|---|---|
| Invoicing | Club-branded invoice at registration. Secondary invoices. Member self-service invoice history. |
| Payment methods | Card, Apple Pay, Google Pay, BPAY, direct-debit payment plans (Majestri collects and pays out weekly). Offline methods direct to the club. |
| Payment plans | Weekly, fortnightly or monthly direct debit. Club sets limits. Treasurer can create plans on behalf of members. |
| Manual receipting | Cash, bank transfer, vouchers. Part payments split across invoice components. |
| Adjustments | Three adjustment types on registrations and orders. |
| Debtor management | Outstanding debts across events and shop. Aging reports. Bulk chase-up emails. |
| Revenue reporting | Revenue by date range and payment method. CSV exports. Financial period alignment. |
| Xero integration | Posts aggregate revenue, refunds and processing fees to mapped Xero accounts. |
| Payouts | List of Majestri deposits to the club bank account. |
| Batch payments | Volunteer rebates paid to bank accounts. |

### 4.6 Website and content

| Feature | Description |
|---|---|
| Page editor | Section-based inline editor for non-technical users. |
| Site menu | One level of drop-down. Links to internal functions or external URLs. |
| Document Vault | Permanent document store. Public or committee-only visibility. |
| Image library and photo albums | Reusable images, match photo albums. |
| Club calendar | Public club events calendar. |
| Field status | Publish ground open or closed status. |
| Event publishing | One-click event content section with a registration button. |

### 4.7 Shop, sponsors, volunteers and assets

| Feature | Description |
|---|---|
| Online shop | Products with size and colour variants, stock per variant, shop step inside registration checkout. |
| Order management | Order list, filters, CSV export as supplier order form, bulk status change, archiving. |
| Sponsor management | Sponsor logos and tiers on the website. "Sponsor Roulette" promotion to members. End-of-season sponsor exposure report. |
| Volunteer management | Volunteer interest captured at registration. Task roster with self-enlistment. Hours tracking for rebate eligibility. |
| Equipment register | Asset register, assignment to team officials, lending history. |
| Ticketing | Multi-tier ticket pricing for functions. |

## 5. Gaps and pain points to address in a new solution

| ID | Gap | Impact |
|---|---|---|
| G-01 | No integration with Dribl fixtures and results | Weekly manual re-keying |
| G-02 | No integration with PlayFootball | Double registration, manual reconciliation |
| G-03 | Custom forms and fee calculators depend on the vendor | Lead time and change control outside the club |
| G-04 | Use of Google Forms, Sheets and Canva for core data | Data outside club access control and retention rules |
| G-05 | No news, structured team pages or contact form in use | Weaker member communication and SEO |
| G-06 | Vendor privacy policy only | Club compliance gap for children's data **[Verify]** |
| G-07 | 2.9% processing fee, no surcharging | Significant cost at club volumes |
| G-08 | Opaque URLs, ageing WebForms stack | Accessibility, SEO and long-term vendor risk |

## 6. Information still required

1. A walkthrough of the authenticated back office by a committee member (screen recording or screenshots of each Control Room tab and the permission matrix).
2. Annual volumes: registrations, transactions, total revenue processed, SMS sent, shop orders.
3. Which Majestri modules the club actively uses (for example payment plans, Xero, assessments, volunteer roster, equipment register).
4. Data export options from Majestri (formats and coverage) for migration.
5. Contract terms: notice period, data return and deletion on exit.
