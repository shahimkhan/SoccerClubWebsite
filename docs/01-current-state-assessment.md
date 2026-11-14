# Current State Assessment: berwickcitysc.com (Majestri)

| Item | Value |
|---|---|
| Document status | Draft v0.2 for committee review |
| Date | 14 November 2026 |
| Scope | Public website and Majestri back-office feature set |
| Method | Review of the public site (15 pages) and the public Majestri product pages (features, pricing, FAQs, club-year pages, showcase). Read-only walkthrough of the authenticated Control Room on 14 November 2026 (Section 7). |
| Limitation | Section 4 comes from Majestri's published material. Section 7 records what the club's own Control Room shows. The walkthrough recorded structure, field names and aggregate counts only. It did not record personal data or dollar balances. Items still marked **[Verify]** need more checks. Items marked **[Confirmed]** were checked in the walkthrough. |

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
| Xero | Accounting | Majestri revenue posting. **[Confirmed]** The club has used it (316 posting records), but the status shows "Not connected" and postings are waiting. See Section 7.7. |

## 3. Observations on the public site

1. **Manual data entry.** Fixtures and training allocations are re-keyed tables. This is a high-effort, error-prone weekly task.
2. **Fragmented tools.** Google Forms, Google Sheets and Canva fill gaps in the platform. Data collected there is outside the club's main system and outside its access controls.
3. **No news or blog function in use.** Updates appear as long sections on the home page.
4. **Stale URLs.** Page slugs do not match titles (`/committee2024` shows Committee 2026, `/jbnplseason2025`).
5. **No contact form.** Only a mailto link.
6. **No structured team pages** on the public site, although Majestri supports them. **[Verify]** The back office has team administration and match reports per event, but the walkthrough did not find team pages published to the site.
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
| G-03 | Custom forms and fee calculators depend on the vendor | Lead time and change control outside the club. **[Confirmed]** The event setup screen tells administrators to send fee calculator changes to Majestri support. |
| G-04 | Use of Google Forms, Sheets and Canva for core data | Data outside club access control and retention rules |
| G-05 | No news, structured team pages or contact form in use | Weaker member communication and SEO |
| G-06 | Vendor privacy policy only | Club compliance gap for children's data. **[Confirmed]** Every back-office screen links only to Majestri legal documents. The walkthrough found no setting for a club privacy collection notice. |
| G-07 | 2.9% processing fee, no surcharging | Significant cost at club volumes |
| G-08 | Opaque URLs, ageing WebForms stack | Accessibility, SEO and long-term vendor risk |

## 6. Information still required

| # | Item | Status after the walkthrough |
|---|---|---|
| 1 | A walkthrough of the authenticated back office, including the permission matrix | **Done.** See Section 7. |
| 2 | Annual volumes: registrations, transactions, total revenue processed, SMS sent, shop orders | **Partly done.** Section 7.10 gives registration, player, order and message counts. Revenue and SMS totals are still needed from the Treasurer. |
| 3 | Which Majestri modules the club actively uses | **Done.** See Section 7.11. |
| 4 | Data export options from Majestri (formats and coverage) for migration | **Partly done.** Section 7.9 lists the CSV exports on each screen. A full data export still needs a request to Majestri. |
| 5 | Contract terms: notice period, data return and deletion on exit | Open. |
| 6 | Why Xero shows "Not connected" and who posts to Xero now | New. Ask the Treasurer. |
| 7 | Whether the seniors fixtures pages and public team pages are in use | Open. |

## 7. Back-office walkthrough (authenticated)

### 7.1 Method and rules

| Item | Value |
|---|---|
| Date | 14 November 2026 |
| Access | A committee account with full system access. The committee member signed in. |
| Mode | Read only. No record, setting or message was saved, sent, exported or deleted. Dialog boxes were closed without saving. |
| Data recorded | Screen structure, menu paths, field names, column names, actions, settings and aggregate counts. |
| Data not recorded | Member names, contact details, medical data, WWCC numbers, financial records and dollar balances. |
| Entry point | `CtlRoom.aspx` (Control Room). It has 14 tabs. Each tab loads a panel on the same page. |

### 7.2 Control Room layout

| Area | Content |
|---|---|
| Alert banners | Product updates from Majestri. Action alerts, for example "Payment Plan awaiting review" and "Registration with modifications that must be approved". |
| Header actions | New Event, Explore Events (`EventExplorer.aspx`), Find someone (`OrgPeople.aspx`), Email All (`SendMessage.aspx`). |
| Tabs | Intake, Seasons, Coaching Clinics, Ticketed Events, Academy Programs, Term-Based Programs, EOIs, Team Photos, People and Groups, Online Shop, Financials, Administration, Content, Competitions. |
| Help pages | Each tab links to a Majestri help page (for example `MajestriHelpEventsAndRegos.aspx`, `MajestriHelpFinancials.aspx`). |

### 7.3 Events (Seasons, Clinics, Ticketed, Academy, Term-Based, EOIs, Team Photos)

All seven event tabs use the same event engine. A tab is an event category. Each event card has the same actions.

| Attribute | Finding |
|---|---|
| Purpose | Set up and run one event: season registration, EOI, clinic, academy term, term program, ticketed function or photo order. |
| Menu path | Control Room > (event category tab) > event card |
| Card actions | Dashboard, Setup, Intake (if linked), New Registration, Email, SMS, Officials, Volunteer Hub, Copy Event. Counters for Incomplete, Registrations, Players (or Attendees or Ticket Holders) and Teams. |
| Event list | `EventExplorer.aspx`: current and upcoming events, with search. |

**Event setup (`Event.aspx`)**

| Group | Fields and actions |
|---|---|
| Publishing options | Public URL (friendly URL), listing on the What's On page, Print/Download QR Code, Preview Landing Page, Publish to Site. |
| Event settings | Name, Friendly URL, Category, Registration Dates, Event Dates, Payment Options (available on checkout), Minimum payment percentage, Final payment date. Free-text event description. |
| Fees | Fee per playing group with a deposit amount. Add Switchover Fees. Discounts (for example a second and subsequent sibling discount). A note tells administrators to send fee calculator changes to Majestri support. |
| Terms and conditions | Add Terms and Conditions. Each item links to a document (club T&Cs, Code of Conduct, Grading Policy). |
| Coordinators | Add a Coordinator. Coordinators get event co-ordinator access. |
| Registration | Registration Rules, Show Form Template, Send Registration Invite (bulk), Send Single Invite. |
| Lifecycle | Review Event Setup, Copy Event, Delete Event. |

**Registration form template (Season 2027)**

| Section | Fields |
|---|---|
| Player | First Name, Middle Name, Surname, Date of Birth, Gender, Playing Group, FFA Number |
| Primary Contact | Email Address, Mobile Number, Alternate Email Address, Phone Number, Address, Suburb, State, Postcode |
| Volunteer interest | Interest in coaching next season (Yes/No). Roles of interest: Committee Member, Assistant Coach, Team Manager, Referee, Linesperson, Canteen, Sponsor, Product/Service Supplier, Working Bee, Other, None. |
| Emergency Contact | Emergency contact fields |

**Event dashboard (`EventDashboard.aspx`)**

| Group | Content |
|---|---|
| Status tiles | Modified registrations, Active registrations, Incomplete registrations, Cancelled registrations, Fees owing, Players, Volunteers. Playing group breakdown. Payment breakdown. |
| Actions | Register Players, Send Email, Send SMS, Send Bulk Registration Invites, Send Individual Registration Invite. |
| Reports | Players Not Registered, WWC Card Holders Report, Invite History, Registrations over Time, Financial Model, Invoices, Credit Notes, Refunds, Officials, Match Reports. |
| Teams | Team list with search. Sort Categories, Team, Import, Import Fixtures, Export Calendars, Export Team Details, Export Team Lists, Audit (`TeamAllocationAuditLog.aspx`). |
| Notes | Administrator Notes, visible only to users with event co-ordinator access. |

**Registrations list (`OrgRegos.aspx`)**

| Attribute | Finding |
|---|---|
| Columns | Primary Contact, Players, Invoiced Amount, Applied Amount, Committed Amount, Pending Amount, Payable Amount |
| Sort and search | Surname (A-Z), Registration Date (newest first). Free-text search. Exports and Filters panel. |
| Bulk actions on selected rows | Send Email, Send SMS, My Details link, Debt Chaseup link, Email Statements, Check Financials, Refund, Credit, Debit |
| Exports (CSV) | Export Registrations, Export Players, Export Registration Debts, Export Player Debts, Export Full Invoices |
| Workflow states | Incomplete, Active, Modified (needs administrator approval), Cancelled. Fees owing is a financial filter. |

**Players list (`EventPlayers.aspx`)**: columns Player and Details. Sort by Surname, Age, Registration Date or Playing Group. Exports: Export Players, Export Player Debts. This screen lists children. It was passed quickly.

**Team administration (`TeamAdministration.aspx`)**: team roster, team message history (Date, Sender), allocation audit (Date/Time, Action, Member, By). Two note areas: Administrator Notes (event co-ordinators only) and Team Member Notes (all officials and players in the team). This screen lists children. It was passed quickly.

**Officials (`TeamOfficials.aspx`)**: list of team officials per event, with Select All for bulk messages. Official roles seen in messaging: Coach, Assistant Coach, Manager, Event Coordinator.

**Event messaging (`SendEventMessage.aspx`)**: audience check boxes by team category (for example Youth Premier League, Community Ungraded, Miniroos, Juniors, Girls Only), Players not on a team, Players, Coaches, Assistant Coaches, Managers, Event Coordinators and saved groups. Fields: From (Display Name), Reply Email (role address), Subject, Message, up to 10 extra addresses. Actions: Send myself a test email, Send Email.

### 7.4 Intake

| Attribute | Finding |
|---|---|
| Purpose | Move players from an EOI event into a season event through trials and offers. |
| Menu path | Control Room > Intake |
| Screens | Intake Players (`IntakePlayers.aspx`: Player, Offer), Intake Dashboard (`IntakeDashboard.aspx`: linked season and EOI events with team counts), Offers (`IntakeOffers.aspx`). |
| Offer columns | Player, Playing Group, Team, Offer Date, Expiry Date, Status |
| Offer sort | Name, Expiry Date, Cancelled Date, Playing Group, Offer Date |
| Workflow states | Offer statuses seen: Accepted, Cancelled. Offers have an expiry date. |
| Current use | One intake ("Player Intake 2027") links the YPL EOI to Season 2027. 92 intake players, 84 accepted offers, 1 cancelled offer. |
| Workaround seen | EOI events use "teams" as pipeline stages, for example "Shortlist", "Trial-1", "Week-1", "Week-2", "Offers", "Not Invited", "Not Attended", "Unsuccessful - T1", "Withdrawn" and "Not selected - to communicate and refund". The platform has no general stage model for community EOIs. |

### 7.5 People and Groups

| Screen | Purpose and functions |
|---|---|
| Find Someone (`OrgPeople.aspx`) | Search by name, phone number or email address. |
| Add a Person | Create a person record. |
| Fix Duplicates (`ResolveDuplicateUsers.aspx`) | Find and merge duplicate person records. |
| Contact List (`BuildContactList.aspx`) | Select from 42 events, then list contacts. |
| Player History (`MemberHistory.aspx`) | Select from 42 events, then list or export players. |
| Players by Gender Report | Aggregate report with Export Report. |
| Accreditation Register (`AccreditationRegister.aspx`) | Columns: Name, Name on Card, Date of Birth, Number, Expiry, Verification. Add Accreditation. Accreditation type seen: WWC Card. 254 records. Not opened in detail. |
| Watchlist (`UserWatchlist.aspx`) | Watchlist entries for people of concern. No entries seen. |
| Groups (`Group.aspx`) | Saved groups with Email, SMS, Bulk Add (First Name, Surname, Email Address, Mobile Number) and member list. 3 groups: Senior Match Day Support, Support Staff, Technical Directors. |

### 7.6 Online Shop

| Screen | Columns and functions |
|---|---|
| Products (`Products.aspx`) | Product, Price, Stock Control, Admin Only, Available in Shop. 3 products. |
| Stock Explorer | Product, Option, Available in Shop, Stock Level. |
| Bundles | Bundle, Price, Admin Only, Available in Shop. 0 bundles. |
| Orders (`OrgOrders.aspx`) | Customer, Order # / Reference, Invoiced, Applied, Committed, Pending and Payable Amount. Order counts by status. 136 orders. |
| Other actions | New Order, Shop Settings, Archive Settings. |

### 7.7 Financials

| Screen | Columns and functions |
|---|---|
| Summary tiles | Unpaid Refunds, Committed/Pending (payment plans), Payable Now (outstanding debts). |
| Invoices (`InvoiceExplorer.aspx`) | Financial year to date. Name, Reference, Date, Amount, Paid Amount, Outstanding Amount. Export. |
| Outstanding Debts | Person, Reference, Invoiced, Applied, Committed, Pending, Payable Amount. Sort by Surname, Payable Amount, Invoice Date. |
| Revenue (`Takings.aspx`) | Revenue by period, with a last 7 days view. |
| Manual Payments | Name, Payment Method, Reference, Issue Date, Description, Receipted By, Amount. Export. |
| Unallocated Payments | Name, Payment Method, Reference, Payment Date, Payment Amount. |
| Failed Card Payments | Name, Reference, Issue Date, Issue Time, Reason, Amount. |
| Payment Plans (`PaymentPlans.aspx`) | Plan #, Applicant, Instalments, Final Payment By, Projected End Date, Plan Amount, Repaid Amount, Outstanding Amount. Sort by Status, End Date, Name. New plans need administrator review before debiting starts. |
| Refunds | Outstanding Refunds (Person, Bank Account Details, Reference, Raised, Refunded, Refundable Amount). Refund Payments (Payment Method, Paid By, Amount, Export). Raised Refunds. |
| Credit Notes | Name, Reference, Issue Date, Description, Issued By, Amount. Export. |
| eVouchers | Voucher #, Status, Owner, Amount. 41 current vouchers. |
| Majestri Settlements | Settlement Date, Web Payments, BPay Payments, Direct Debit Payments, Total Payments, Majestri Fees, GST, Settlement. Export Settlements. |
| Xero Summary and Posting Activity | Status shows "Not connected". Connection starts when an administrator posts. Amounts wait to be posted by type: Majestri Transfers, Sports Voucher Payments, Direct-to-Club Payments (EFTPOS, Manual Voucher, Cash, Bank Transfer) and Refund Payments. Posting Activity has 316 records (Posted on, Type, Narration, Amount Posted). |

**Financial settings (configuration, values as seen)**

| Setting | Value |
|---|---|
| Payout bank account and ABN | Set (values not recorded) |
| First month of financial year | October |
| Show "Tax Invoice" and GST-inclusive wording | On |
| Transfer outstanding refunds to outstanding debt | On |
| Invoice terms | None (options: None, 7, 14, 28 days, Calendar Month) |
| Offline payment methods | EFT on, Cash on, EFTPOS on, BPay off, Cheque/money order off |
| Partial card payments | On |
| Refund batches (ABA file) | User Identification Number, Institution Code, Remitter Name. Automatic balancing line off. |

### 7.8 Administration

| Screen | Purpose and functions |
|---|---|
| Account Security | Site-wide setting "Enable two-factor authentication (2FA) for this site": **On**. |
| 2FA Activity | User, Last Authenticated. |
| All Committees (`OrgCommittees.aspx`) | 4 committees in history. New Committee. |
| Committee Boardroom (`CommitteeBoardroom.aspx`) | Current committee: 15 members, term 29 Dec 2025 to 29 Dec 2026. Committee Settings (name, effective from, to). Add Committee Member. Send Email, Send SMS. Meeting records. Audit tab (Date/Time, Action, Member, Changes, By). Link to the Document Vault. |
| Administrators group | 1 member. Email and SMS. |
| Club Email Addresses | 8 role addresses (for example secretary@, registrar@, treasurer@). |
| Do Not Email Register | 103 entries. Email Address, Date/Time. Export. |
| Do Not SMS Register | Add Mobile Number. |
| Delivery Problems | 103 entries. Date, Email Address, Reason. |
| Daily Email Logs | Filter by month. Monthly volumes from about 2,300 to 8,800 emails. |
| SMS Management | Type, Date/Time, Message/Narration, From/To, Credits. |
| Messages Sent | 1,042 messages. Date, Sender. |
| Equipment Register | Status, Equipment. Export. No records seen. |
| Service Provider Register | Name, Renewal Date. No records seen. |
| Connected Domains | Export. No entries listed. |
| Notification Settings | Recipient addresses per category: Majestri Updates, Registrations, Online Shop, Financials, Payment Plans, Content, System, Financial Documents from Majestri, Public Contact. Recipients are role addresses. |

**Permission model [Confirmed]**

| Control | Finding |
|---|---|
| Committee term | Each committee has start and end dates. A new committee is created each year. |
| Committee Role | Free label per member. Roles seen: President, Vice President, Secretary, Treasurer, Registrar, Director Of Football, Coaching Director, Junior Coordinator, Womens Football, Sponsorship, Canteen Convenor, General Member. |
| System Access | Per member, any mix of five areas: **Administration, Financials, Content, Events, Shop**. Examples seen: all five; Financials and Shop; Financials only; Content and Events; Events only. Some members have no system access. |
| Hide Contact Details | Per member flag. |
| Event co-ordinator | Per event. Gives access to that event and its Administrator Notes. |
| Team officials | Coach, Assistant Coach, Manager per team. Scoped to the team. |
| Audit | The Committee Boardroom audit tab records role and access changes. Team allocation has its own audit log. |
| Gaps | No read-only role and no auditor role were seen. Access is per area, not per action (for example no separate permission for refunds or exports). |

### 7.9 Content

| Screen | Purpose and functions |
|---|---|
| Pages (`OrgWebSitePages.aspx`) | 33 web site pages. Search. |
| Menu Builder | Site menu editor. |
| Images (`HostedImages.aspx`) | 168 images. |
| Photo Albums | 1 album. |
| Calendar (`edit-calendar`) | Month view. Show old entries, Import entries. 0 upcoming entries. |
| Document Vault | 31 documents. Filename, Uploaded, Type. Sort by Category, Filename, Upload Date. |
| Media Library (beta) | Collections. 1 collection. |
| Sponsors (`OrgSponsors.aspx`) | 20 sponsors in 5 tiers: Major Sponsor (1), Gold Sponsor (8), Production Partner (1), Berwick Business Buddies (8), Player Sponsor (2). |
| Sponsor Roulette Statistics | Sponsor, Web Site Impressions, Email Impressions, Total Impressions, Total Clicks. |
| Fields (`OrgLocations.aspx`) | New Field. No fields listed. |
| Odyssey Website Experience | Website theme and experience settings. Not opened. |

### 7.10 Competitions and aggregate volumes

**Competitions** shows only an information page for "Whirlpool", the Majestri competition product (fixtures, referee allocation, results and ladders). The club does not use it.

| Event (aggregate counts only) | Registrations | Players or attendees | Teams |
|---|---|---|---|
| Season 2027 (open) | 498 | 593 | Teams being formed |
| Season 2026 | 590 | 698 | 50 |
| EOI 2027: YPL Teams Only | 649 | 681 | Pipeline lists |
| EOI 2027: Community Teams | 479 | 550 | Pipeline lists |
| EOI 2026: Community Teams | 576 | 654 | 43 |
| EOI 2026: YPL Teams | 571 | 599 | 24 |
| Development Academy Program, Term 4 | 52 | 61 | |
| 2026 Seniors Presentation (ticketed) | 63 | 63 ticket holders | |
| Team Photos Season 2026 | 282 | 334 | |
| Shop orders (all time) | 136 | | |
| Messages sent (all time) | 1,042 | | |

The message audience list shows 42 events since Season 2023.

### 7.11 Modules in use

| Module | Status | Evidence |
|---|---|---|
| Event engine (season, EOI, clinic, academy, term program) | **In use, core** | 7 event categories, 42 events since 2023 |
| Intake and offers | **In use, core** | 92 intake players, 84 accepted offers |
| Registration form and fee calculator | **In use, vendor-maintained** | Fee changes go through Majestri support |
| Ticketing | In use, low volume | One presentation event per year |
| Team Photos orders | In use, seasonal | Event category with orders |
| Team administration and officials | **In use, core** | 50 teams in Season 2026 |
| Match reports | Available, use not confirmed | Report link on the dashboard |
| Fixture import | Available, use not confirmed | Import Fixtures on the dashboard |
| Accreditation (WWC) register | **In use** | 254 records |
| Saved groups | In use, light | 3 groups |
| Email and SMS | **In use, core** | 1,042 messages, up to about 8,800 emails a month |
| Do Not Email and delivery problems | In use | 103 entries each |
| Club role email addresses | In use | 8 addresses |
| Invoicing, debtors and payment plans | **In use, core** | Plans, debts and refunds active |
| Manual receipting (EFT, cash, EFTPOS, vouchers) | **In use** | Direct-to-club payments waiting for Xero posting |
| eVouchers | In use | 41 current vouchers |
| Refund batches (ABA) | In use | Refund batch settings set |
| Xero posting | **Used before, now disconnected** | 316 posting records, status "Not connected" |
| Online shop | In use, small | 3 products, 136 orders |
| Website pages, menu, images, documents | **In use** | 33 pages, 168 images, 31 documents |
| Sponsors and Sponsor Roulette | In use | 20 sponsors, 5 tiers |
| Committees, access control, 2FA | **In use** | Site-wide 2FA on, yearly committees |
| Notification settings | In use | Role addresses per category |
| Volunteer Hub | **Not in use** | 0 volunteers in Season 2027 |
| Equipment register | **Not in use** | No records seen |
| Service provider register | Not in use | No records seen |
| Watchlist | Not in use | No entries seen |
| Club calendar | Not in use | 0 upcoming entries |
| Fields and field status | Not in use | No fields listed |
| Photo albums and media library | Minimal use | 1 album, 1 collection |
| Player assessments | **Not seen** | No assessment screen in the Control Room |
| Competitions (Whirlpool) | **Not in use** | Information page only |

### 7.12 New findings and gaps

| ID | Finding | Impact |
|---|---|---|
| G-09 | Community EOI pipeline stages are modelled as "teams" (Shortlist, Trial, Not Invited, Unsuccessful) | Workaround. The new platform needs configurable stages for every intake. |
| G-10 | Xero is not connected and postings are waiting | Finance reconciliation risk now. Confirm the Treasurer's current process. |
| G-11 | Access control has only five broad areas | No least-privilege control for refunds, exports or child data. No auditor role. |
| G-12 | Modified registrations and new payment plans wait for manual approval | Approval queues need alerts and owners in the new platform. |
| G-13 | About 103 addresses have delivery problems and 103 are on the Do Not Email register | The new platform must import both lists at migration. |
| G-14 | Volunteer interest is collected on the form, but the Volunteer Hub is not used | The game-day roster stays in Google Sheets. |
| G-15 | Registration form collects the FFA number but there is no live PlayFootball link | "Players Not Registered" depends on a manual extract. |
