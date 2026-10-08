**# Software Requirements Specification**

**## Narkhnama: Digital Daily Market Rate Platform**

\| Field | Value |

\|---|---|

\| Document ID | NKN-SRS-001 |

\| Version | 1.0 (Baseline Draft) |

\| Date | 7 October 2026 |

\| Status | Draft for review |

\| Standard followed | ISO/IEC/IEEE 29148:2018 (supersedes IEEE 830-1998) |

\| Prepared by | Hamza, COMSATS University Islamabad, Abbottabad Campus |

**### Revision History**

\| Version | Date | Author | Description |

\|---|---|---|---|

\| 0.1 | Oct 2026 | Hamza | Initial informal SRS |

\| 1.0 | 7 Oct 2026 | Hamza | Restructured to ISO/IEC/IEEE 29148, added IDs, priorities, acceptance criteria, traceability |

**### Table of Contents**

1\. Introduction

2\. Overall Description

3\. External Interface Requirements

4\. System Features and Functional Requirements

5\. Non-Functional Requirements

6\. Data Requirements

7\. Business Rules

8\. Data Accuracy and Verification Policy

9\. Constraints, Assumptions, Dependencies

10\. Legal, Ethical and Compliance Requirements

11\. MVP Scope, Release Plan and Acceptance Criteria

12\. Risks

13\. Requirements Traceability Matrix

14\. Appendices

**---**

**## 1. Introduction**

**### 1.1 Purpose**

This document specifies the requirements of Narkhnama, a web platform that publishes verified daily market prices by city and market. It is intended for the project team, supervisor, testers, and future maintainers. It is the baseline for design, development, testing and acceptance.

**### 1.2 Product Scope**

Narkhnama answers one question: **\*\*"What is today's rate of this product in my market?"\*\***

In scope (Release 1.0, MVP):

\- Public price search by product, city, market and category.

\- Latest price with unit, date, source and verification status.

\- Price history and simple price-change indicators.

\- Shopkeeper accounts, saved product lists, customized rate list generation (PDF download and print).

\- Admin and Data Operator panels for managing master data and prices, with a verification workflow.

\- Initial coverage: Abbottabad, vegetables and selected grocery items.

Out of scope for Release 1.0 (see 11.3): mobile apps, price prediction, WhatsApp delivery, user-submitted prices, payments, automated scraping in production.

**### 1.3 Definitions, Acronyms, Abbreviations**

\| Term | Meaning |

\|---|---|

\| Narkhnama | Product name (Urdu "rate list") |

\| Guest | Visitor without an account |

\| Shopkeeper | Registered business user who creates rate lists |

\| Data Operator (DO) | Staff who enter and check prices |

\| Administrator (Admin) | Staff with full system authority |

\| Market | Physical bazaar or mandi, belongs to one city |

\| Price Record | One price of one product in one market on one date |

\| Rate List | Shopkeeper's document of selected products with latest verified prices |

\| Verified / Pending / Rejected / Outdated | Price lifecycle statuses (Section 8) |

\| RLS | Row Level Security (PostgreSQL) |

\| RBAC | Role-Based Access Control |

\| MVP | Minimum Viable Product |

\| SRS | Software Requirements Specification |

\| p95 | 95th percentile response time |

\| RPO / RTO | Recovery Point / Recovery Time Objective |

**### 1.4 References**

1\. ISO/IEC/IEEE 29148:2018, Requirements engineering.

2\. IEEE 830-1998, Recommended Practice for SRS.

3\. OWASP ASVS 4.0 and OWASP Top 10 (2021).

4\. WCAG 2.1, Level AA.

5\. Pakistan Prevention of Electronic Crimes Act (PECA) 2016 (for scraping and data use review).

6\. Supabase, Next.js and PostgreSQL official documentation.

**### 1.5 Document Conventions**

\- Requirement IDs: \`FR-\<MODULE>-\<NN>\` functional, \`NFR-\<CATEGORY>-\<NN>\` non-functional, \`BR-NN\` business rule, \`UC-NN\` use case.

\- "shall" = mandatory. "should" = recommended. "may" = optional.

\- Priority uses MoSCoW: **\*\*M\*\*** Must, **\*\*S\*\*** Should, **\*\*C\*\*** Could, **\*\*W\*\*** Won't (this release).

\- Each requirement is unique, testable, and has a verification method: **\*\*T\*\*** Test, **\*\*D\*\*** Demonstration, **\*\*I\*\*** Inspection, **\*\*A\*\*** Analysis.

**### 1.6 Overview**

Sections 2 and 3 describe the product context and interfaces. Sections 4 to 7 hold the verifiable requirements. Diagrams (context, DFD, ER, swim lanes, state, sequence, deployment) are in \`02-Narkhnama-Diagrams.md\`. Architecture and technology choices are in \`03-Narkhnama-Tech-Stack.md\`.

**---**

**## 2. Overall Description**

**### 2.1 Product Perspective**

Narkhnama is a new, self-contained, web-based system. It depends on external hosting, a managed PostgreSQL service, an authentication service and external price sources. See the System Context and DFD diagrams.

Major parts:

1\. Public Web App (Guest search).

2\. Shopkeeper Portal.

3\. Admin and Data Operator Console.

4\. Backend API and business logic.

5\. PostgreSQL database.

6\. Price Collection Service (separate, scheduled, not in the request path).

7\. PDF generation service.

**### 2.2 Product Functions (summary)**

Search, location selection, current price, history, comparison, shopkeeper product lists, rate list generation/download/print, price entry, validation, verification, master data management, user and role management, reporting, optional notifications.

**### 2.3 User Classes and Characteristics**

\| Class | Technical skill | Frequency | Access | Priority |

\|---|---|---|---|---|

\| Guest | Low (smartphone user) | Daily | Public pages only | Primary |

\| Shopkeeper | Low to medium | Daily | Own account and lists | Primary |

\| Data Operator | Medium | Daily | Price entry, review | Secondary |

\| Administrator | Medium to high | Daily/weekly | Full system | Secondary |

\| Price Collector (system actor) | n/a | Scheduled | Write pending prices only | Secondary |

**### 2.4 Operating Environment**

\- Client: modern evergreen browsers (Chrome, Edge, Firefox, Safari: latest two major versions), Android, iOS, Windows, Linux, macOS. Responsive from 360 px width.

\- Server: managed cloud hosting (see tech stack document).

\- Network: must work acceptably on 3G/4G mobile connections common in Khyber Pakhtunkhwa.

**### 2.5 Design and Implementation Constraints**

1\. C-01: Release 1.0 covers Abbottabad only, with a data model that supports multiple cities.

2\. C-02: Credentials and API keys shall never reach the client bundle.

3\. C-03: Scraping shall comply with source terms, robots.txt and applicable law.

4\. C-04: Open-source or free-tier services preferred during the student/MVP phase.

5\. C-05: Interface shall support English and Urdu (Urdu with right-to-left layout) at least for labels in Release 1.0 or 1.1 (see FR-I18N-01).

**### 2.6 Assumptions and Dependencies**

Assumptions:

\- A-01: At least one reliable price source per market exists (official market committee lists, authorized data entry, or permitted web sources).

\- A-02: Operators can update prices at least once per working day.

\- A-03: Users have internet access.

\- A-04: Shopkeepers give accurate product information.

Dependencies:

\- D-01: Hosting provider, database service, authentication service.

\- D-02: PDF generation library.

\- D-03: External sources for the collector.

If A-01 or A-02 fails, the product value drops sharply (see Risk R-01).

**---**

**## 3. External Interface Requirements**

**### 3.1 User Interfaces**

\| ID | Requirement | Pri | Ver |

\|---|---|---|---|

\| EIR-UI-01 | The interface shall be responsive and usable on screens from 360 px to 1920 px wide. | M | T |

\| EIR-UI-02 | The home page shall provide product search, city selector, market selector, category browse and a Check Rate action. | M | D |

\| EIR-UI-03 | Price results shall show product, rate, unit, city, market, date, source and status in the first screen view without scrolling on a 360 x 640 px device. | M | I |

\| EIR-UI-04 | Pages: Home, Search Results, Product Detail, Price History, Markets, Login/Register, Shopkeeper Dashboard, Rate List Generator, Admin Dashboard, Price Management, Verification Queue. | M | I |

\| EIR-UI-05 | Units shall always be displayed next to every price. | M | I |

**### 3.2 Hardware Interfaces**

None. The system runs as a hosted web application.

**### 3.3 Software Interfaces**

\| ID | Interface | Purpose |

\|---|---|---|

\| EIR-SW-01 | PostgreSQL (via Supabase) | Persistent storage |

\| EIR-SW-02 | Supabase Auth | Registration, login, sessions, password handling |

\| EIR-SW-03 | PDF generation library | Rate list files |

\| EIR-SW-04 | Price source adapters (HTTP, API, file import) | Collector input |

\| EIR-SW-05 | Email provider (optional) | Verification emails, notifications |

\| EIR-SW-06 | Error and uptime monitoring service | Operations |

**### 3.3.1 Internal API (REST/JSON over HTTPS)**

All endpoints shall return JSON, use standard HTTP status codes, validate input server-side, and be versioned under \`/api/v1\`. Full endpoint list is in the tech stack document.

**### 3.4 Communications Interfaces**

\- HTTPS (TLS 1.2 or higher) for all traffic.

\- HTTP security headers (HSTS, CSP, X-Content-Type-Options, frame protections).

**---**

**## 4. System Features and Functional Requirements**

**### 4.1 Authentication and Account (AUTH)**

\| ID | Requirement | Pri | Ver |

\|---|---|---|---|

\| FR-AUTH-01 | The system shall allow Guests to use search, price and history features without an account. | M | D |

\| FR-AUTH-02 | The system shall allow Shopkeepers to register with name, email or mobile number, password, shop name, city and market. | M | T |

\| FR-AUTH-03 | The system shall verify the email address (or mobile number) before activating a Shopkeeper account. | S | T |

\| FR-AUTH-04 | The system shall allow login and logout for Shopkeepers, Data Operators and Administrators. | M | T |

\| FR-AUTH-05 | The system shall support password reset through a time-limited link (valid 30 minutes or less). | S | T |

\| FR-AUTH-06 | The system shall lock login for 15 minutes after 5 consecutive failed attempts from the same account or IP. | S | T |

\| FR-AUTH-07 | The system shall enforce role-based access (Guest, Shopkeeper, Data Operator, Admin) on every protected route and API call. | M | T |

\| FR-AUTH-08 | Admin and Data Operator accounts shall be created only by an Admin (no public registration). | M | T |

**### 4.2 Product Search and Browse (SRCH)**

\| ID | Requirement | Pri | Ver |

\|---|---|---|---|

\| FR-SRCH-01 | The system shall search products by name, case-insensitive, with partial matching. | M | T |

\| FR-SRCH-02 | The system shall support English and Urdu product names and common alternate spellings (for example "Tamatar" and "Tomato"). | S | T |

\| FR-SRCH-03 | The system shall show search suggestions after 2 typed characters. | S | D |

\| FR-SRCH-04 | The system shall allow browsing products by category. | M | D |

\| FR-SRCH-05 | The system shall show only active products to Guests and Shopkeepers. | M | T |

\| FR-SRCH-06 | If no product matches, the system shall display a clear "no result" message and suggest browsing categories. | M | D |

**### 4.3 Location Management (LOC)**

\| ID | Requirement | Pri | Ver |

\|---|---|---|---|

\| FR-LOC-01 | The system shall let users select a city from the list of active cities. | M | D |

\| FR-LOC-02 | After a city is chosen, the system shall list only that city's active markets. | M | T |

\| FR-LOC-03 | The system shall remember the last chosen city and market in the user's browser. | C | D |

\| FR-LOC-04 | If a market is not chosen, the system shall show prices for all markets of the city, one row per market. | S | T |

**### 4.4 Current Price Display (PRC)**

\| ID | Requirement | Pri | Ver |

\|---|---|---|---|

\| FR-PRC-01 | The system shall display the latest Verified price for the selected product and market. | M | T |

\| FR-PRC-02 | Each displayed price shall show unit, price date, source name and status. | M | I |

\| FR-PRC-03 | The system shall never present Pending or Rejected prices as current rates to Guests or Shopkeepers. | M | T |

\| FR-PRC-04 | If the latest Verified price is older than the staleness threshold (default 3 days, configurable), the system shall label it "Outdated" and show its date. | M | T |

\| FR-PRC-05 | If no Verified price exists, the system shall show "Price not available" with the date of last known price, if any. | M | T |

\| FR-PRC-06 | Prices shall be displayed in Pakistani Rupees (PKR) with unit, for example "Rs. 180 per kg". | M | I |

**### 4.5 Price History (HIST)**

\| ID | Requirement | Pri | Ver |

\|---|---|---|---|

\| FR-HIST-01 | The system shall retain all previous Verified prices; a new price shall never overwrite or delete an old one. | M | T |

\| FR-HIST-02 | The system shall show a dated history table for a product and market. | M | T |

\| FR-HIST-03 | The system shall allow history filtering by range: 7 days, 30 days, 90 days, custom. | S | T |

\| FR-HIST-04 | The system shall show a line chart of history. | S | D |

\| FR-HIST-05 | The system shall show change versus previous price as amount and percentage, with an up/down indicator that does not rely on colour alone (arrow and text). | M | I |

**### 4.6 Price Comparison (CMP)**

\| ID | Requirement | Pri | Ver |

\|---|---|---|---|

\| FR-CMP-01 | The system shall compare the latest Verified price of one product across all markets in a city where data exists. | S | T |

\| FR-CMP-02 | The system shall highlight the lowest and highest price in the comparison. | C | D |

\| FR-CMP-03 | Cross-city comparison is deferred to a later release. | W | n/a |

**### 4.7 Shopkeeper Section (SHOP)**

\| ID | Requirement | Pri | Ver |

\|---|---|---|---|

\| FR-SHOP-01 | A logged-in Shopkeeper shall maintain a profile: shop name, city, market, contact number. | M | T |

\| FR-SHOP-02 | The Shopkeeper shall select one or more categories and see their active products. | M | D |

\| FR-SHOP-03 | The Shopkeeper shall select any subset of products from any categories (multi-category selection). | M | T |

\| FR-SHOP-04 | The Shopkeeper shall review the selection before generation. | M | D |

\| FR-SHOP-05 | The system shall save the selection as a named product list; a Shopkeeper may have up to 5 lists (configurable). | M | T |

\| FR-SHOP-06 | The Shopkeeper shall add, remove and reorder products in a saved list at any time. | M | T |

\| FR-SHOP-07 | The system shall generate a Rate List containing only the products in the chosen list, with the latest Verified prices for the Shopkeeper's market. | M | T |

\| FR-SHOP-08 | The Rate List shall show shop name, city, market, generation date and time, and the price date of each item; products with no Verified price shall be shown as "N/A". | M | I |

\| FR-SHOP-09 | The system shall export the Rate List as PDF (A4) within 5 seconds for lists up to 100 products. | M | T |

\| FR-SHOP-10 | The system shall provide a print-optimized view of the Rate List (browser print). | M | D |

\| FR-SHOP-11 | The Shopkeeper shall be able to add an optional shop logo and contact line to the Rate List header. | C | D |

\| FR-SHOP-12 | The Shopkeeper shall see only their own lists and profile (see NFR-SEC-05). | M | T |

\| FR-SHOP-13 | The system shall support optional shop margin: shopkeeper adds a fixed amount or percentage to selected product rates on the list, without changing stored market prices. | C | T |

**### 4.8 Price Data Entry and Collection (DATA)**

\| ID | Requirement | Pri | Ver |

\|---|---|---|---|

\| FR-DATA-01 | An Admin or Data Operator shall enter a price by selecting product, market, price, unit, date and source. | M | T |

\| FR-DATA-02 | The system shall support bulk entry (spreadsheet-style grid or CSV import) for a market on one date. | S | T |

\| FR-DATA-03 | The system shall store source, collector (user or system), collection timestamp and entry timestamp for every price record. | M | T |

\| FR-DATA-04 | Prices from the Price Collection Service shall be stored as Pending only. | M | T |

\| FR-DATA-05 | The collector shall run on a schedule, separately from user requests, and shall not slow public search. | S | A |

\| FR-DATA-06 | The collector shall use source adapters so a new source can be added without changing core code. | S | I |

\| FR-DATA-07 | The collector shall log each run: source, start/end, records read, records stored, errors. | S | I |

\| FR-DATA-08 | Automated collection shall be enabled for a source only after its legal and terms-of-use check is recorded by an Admin. | M | I |

**### 4.9 Validation and Verification (VER)**

\| ID | Requirement | Pri | Ver |

\|---|---|---|---|

\| FR-VER-01 | The system shall reject records with missing product, market, price, unit, date or source. | M | T |

\| FR-VER-02 | The system shall reject prices that are zero, negative or non-numeric. | M | T |

\| FR-VER-03 | The system shall detect a duplicate (same product, market, date, source) and prevent a second record, offering update instead. | M | T |

\| FR-VER-04 | The system shall flag a price differing from the previous Verified price by more than a configurable threshold (default 30 percent) for mandatory review. | S | T |

\| FR-VER-05 | The system shall provide a Verification Queue where an Admin approves or rejects Pending prices individually or in bulk. | M | D |

\| FR-VER-06 | A rejection shall require a reason, which is stored. | S | T |

\| FR-VER-07 | Verification shall record verifier identity and timestamp. | M | T |

\| FR-VER-08 | A Data Operator shall not verify prices they entered themselves (four-eyes rule). | S | T |

\| FR-VER-09 | The system shall mark Verified prices as Outdated automatically after the staleness threshold passes without a newer price. | M | T |

**### 4.10 Administration (ADM)**

\| ID | Requirement | Pri | Ver |

\|---|---|---|---|

\| FR-ADM-01 | The Admin dashboard shall show counts of products, categories, cities, markets, Pending prices, users and shopkeepers, and the date of the latest price per market. | M | D |

\| FR-ADM-02 | The Admin shall add, edit and deactivate cities. | M | T |

\| FR-ADM-03 | The Admin shall add, edit and deactivate markets, each linked to one city. | M | T |

\| FR-ADM-04 | The Admin shall add, edit and deactivate categories. | M | T |

\| FR-ADM-05 | The Admin shall add, edit and deactivate products with category, default unit, English name and Urdu name. | M | T |

\| FR-ADM-06 | The Admin shall manage price sources (name, type, URL, reliability notes, legal status, active flag). | M | T |

\| FR-ADM-07 | The Admin shall manage users: create staff accounts, assign roles, suspend and reactivate accounts. | M | T |

\| FR-ADM-08 | Entities in use shall be deactivated (soft delete), not physically deleted, to preserve history. | M | T |

\| FR-ADM-09 | The system shall keep an audit log of administrative actions: who, what, when, old and new value. | S | I |

**### 4.11 Reporting (RPT)**

\| ID | Requirement | Pri | Ver |

\|---|---|---|---|

\| FR-RPT-01 | Daily Price Report per market. | S | D |

\| FR-RPT-02 | Product Price History report. | S | D |

\| FR-RPT-03 | Price Change report (largest increases and decreases over a period). | C | D |

\| FR-RPT-04 | Missing Data report (products and markets with no current Verified price). | S | D |

\| FR-RPT-05 | Data Source report (record counts and rejection rate per source). | C | D |

\| FR-RPT-06 | Reports shall export to CSV. | C | T |

**### 4.12 Notifications (NOTIF) (optional in 1.0)**

\| ID | Requirement | Pri | Ver |

\|---|---|---|---|

\| FR-NOTIF-01 | The system may notify a Shopkeeper that new prices are available for products in their saved lists. | C | D |

\| FR-NOTIF-02 | The system may notify users of significant price changes of products they follow. | W | n/a |

**### 4.13 Localization (I18N)**

\| ID | Requirement | Pri | Ver |

\|---|---|---|---|

\| FR-I18N-01 | The interface shall support English and Urdu, with right-to-left layout for Urdu, selectable by the user. | S | D |

\| FR-I18N-02 | Dates shall be shown in a clear day-month-year format, for example "7 October 2026". | M | I |

**### 4.14 Use Cases**

\| ID | Name | Actor | Related requirements |

\|---|---|---|---|

\| UC-01 | Search product price | Guest | FR-SRCH, FR-LOC, FR-PRC |

\| UC-02 | View price history | Guest | FR-HIST |

\| UC-03 | Compare markets | Guest | FR-CMP |

\| UC-04 | Register shopkeeper | Shopkeeper | FR-AUTH-02, 03 |

\| UC-05 | Create and save product list | Shopkeeper | FR-SHOP-02 to 06 |

\| UC-06 | Generate, download, print rate list | Shopkeeper | FR-SHOP-07 to 10 |

\| UC-07 | Enter prices | Data Operator, Admin | FR-DATA, FR-VER-01 to 03 |

\| UC-08 | Verify prices | Admin | FR-VER-04 to 09 |

\| UC-09 | Manage master data | Admin | FR-ADM |

\| UC-10 | Collect prices automatically | Price Collector | FR-DATA-04 to 08 |

\| UC-11 | View reports | Admin | FR-RPT |

**#### UC-01 Search Product Price (detailed template)**

\| Item | Description |

\|---|---|

\| Actor | Guest |

\| Goal | See the current rate of a product in a market |

\| Preconditions | Website available; at least one active city and market exist |

\| Trigger | Guest opens the home page |

\| Main flow | 1. Guest enters product name. 2. Guest selects city. 3. Guest selects market. 4. System queries latest Verified price. 5. System displays rate, unit, date, source, status and change from previous price. |

\| Alternative flow A | No Verified price: system shows "Price not available" and the last known date. |

\| Alternative flow B | Price older than threshold: system shows it labelled "Outdated". |

\| Exception | Database unavailable: system shows a friendly error and retry option; error is logged. |

\| Postcondition | No data changed |

\| Related requirements | FR-SRCH-01, FR-LOC-01, FR-LOC-02, FR-PRC-01 to 05 |

**#### UC-06 Generate Rate List (detailed template)**

\| Item | Description |

\|---|---|

\| Actor | Shopkeeper |

\| Goal | Obtain a printable rate list of own products |

\| Preconditions | Logged in; at least one saved product list |

\| Main flow | 1. Shopkeeper opens saved list. 2. System fetches latest Verified price per product for the shopkeeper's market. 3. System builds the Rate List preview. 4. Shopkeeper reviews. 5. Shopkeeper chooses Download PDF or Print. 6. System delivers the file or print view. |

\| Alternative flow | A product has no Verified price: shown as "N/A"; shopkeeper may remove it. |

\| Exception | PDF generation fails: system shows error and offers print view. |

\| Postcondition | Optional record of generation (timestamp, list) saved for history |

\| Related requirements | FR-SHOP-07 to 10 |

**---**

**## 5. Non-Functional Requirements**

**### 5.1 Performance (PERF)**

\| ID | Requirement | Ver |

\|---|---|---|

\| NFR-PERF-01 | Server response for search and latest-price queries shall be p95 of 800 ms or less with 100 concurrent users and 1 million price records. | T |

\| NFR-PERF-02 | Largest Contentful Paint shall be 2.5 s or less on a simulated 4G connection for the home and result pages. | T |

\| NFR-PERF-03 | Initial JavaScript for the public pages shall be 200 KB or less (compressed). | I |

\| NFR-PERF-04 | PDF export shall complete in 5 s or less for 100 products. | T |

\| NFR-PERF-05 | Collection jobs shall run outside the request path and shall not raise public p95 latency by more than 10 percent. | T |

**### 5.2 Usability (USAB)**

\| ID | Requirement | Ver |

\|---|---|---|

\| NFR-USAB-01 | A first-time Guest shall complete a price search in 3 steps or fewer (search, location, view). | T |

\| NFR-USAB-02 | In usability testing with at least 5 target users, at least 90 percent shall complete the search task without help. | T |

\| NFR-USAB-03 | The interface shall use plain language and avoid technical terms. | I |

**### 5.3 Reliability and Availability (REL)**

\| ID | Requirement | Ver |

\|---|---|---|

\| NFR-REL-01 | Monthly availability target: 99.5 percent (excluding planned maintenance). | A |

\| NFR-REL-02 | Automated daily database backups, retained 14 days or more; RPO 24 hours or less; RTO 4 hours or less. | T |

\| NFR-REL-03 | A restore from backup shall be tested at least once before launch. | D |

\| NFR-REL-04 | Price data integrity: a stored price shall not change after verification except through an audited correction. | T |

**### 5.4 Security (SEC)**

\| ID | Requirement | Ver |

\|---|---|---|

\| NFR-SEC-01 | Passwords shall be stored only as salted hashes by the authentication service; never in plain text or logs. | I |

\| NFR-SEC-02 | All traffic shall use HTTPS with TLS 1.2 or higher; HSTS enabled. | T |

\| NFR-SEC-03 | Authorization shall be enforced server-side and at database level (RLS) for every role. | T |

\| NFR-SEC-04 | Secrets shall be held in environment variables or a secrets manager, never committed to source control or sent to the browser. | I |

\| NFR-SEC-05 | A Shopkeeper shall not read or modify another Shopkeeper's data (verified by automated tests). | T |

\| NFR-SEC-06 | All input shall be validated server-side; the system shall resist SQL injection, XSS and CSRF (OWASP Top 10 review before launch). | T |

\| NFR-SEC-07 | Public and login endpoints shall be rate limited. | T |

\| NFR-SEC-08 | Admin actions shall be logged to a tamper-resistant audit log. | I |

\| NFR-SEC-09 | Admin accounts shall support multi-factor authentication. | S |

\| NFR-SEC-10 | Dependency vulnerability scanning shall run in CI; critical findings block release. | I |

**### 5.5 Scalability (SCAL)**

\| ID | Requirement | Ver |

\|---|---|---|

\| NFR-SCAL-01 | Adding a city, market, category or product shall require data entry only, no code change. | D |

\| NFR-SCAL-02 | The price table design shall support 50 million records through indexing and partitioning by date. | A |

\| NFR-SCAL-03 | The application shall be stateless to allow horizontal scaling. | I |

**### 5.6 Maintainability (MAINT)**

\| ID | Requirement | Ver |

\|---|---|---|

\| NFR-MAINT-01 | Code shall be modular: auth, catalog, pricing, shopkeeper, admin, collector as separate modules. | I |

\| NFR-MAINT-02 | Automated unit test coverage of business logic: 70 percent or more. | T |

\| NFR-MAINT-03 | Code shall follow a linter and formatter configuration enforced in CI. | I |

\| NFR-MAINT-04 | Database changes shall be made only through versioned migrations. | I |

\| NFR-MAINT-05 | API and database shall be documented (OpenAPI, ER diagram kept current). | I |

**### 5.7 Compatibility (COMP)**

\| ID | Requirement | Ver |

\|---|---|---|

\| NFR-COMP-01 | Supported browsers: latest two versions of Chrome, Edge, Firefox, Safari; Android Chrome; iOS Safari. | T |

\| NFR-COMP-02 | The site shall remain functional (read-only search) on low-end Android devices with 2 GB RAM. | T |

**### 5.8 Accessibility (ACC)**

\| ID | Requirement | Ver |

\|---|---|---|

\| NFR-ACC-01 | Public pages shall meet WCAG 2.1 Level AA for contrast, labels and keyboard navigation where practical. | T |

\| NFR-ACC-02 | Information shall not be conveyed by colour alone. | I |

**### 5.9 Observability (OBS)**

\| ID | Requirement | Ver |

\|---|---|---|

\| NFR-OBS-01 | Application errors shall be captured with stack traces and alerting. | D |

\| NFR-OBS-02 | Uptime monitoring shall check the public site at 5 minute intervals or better. | D |

\| NFR-OBS-03 | Collector job failures shall alert the Admin. | D |

**---**

**## 6. Data Requirements**

**### 6.1 Logical Entities**

\| Entity | Key attributes |

\|---|---|

\| City | id, name_en, name_ur, status |

\| Market | id, city_id, name_en, name_ur, status |

\| Category | id, name_en, name_ur, description, status |

\| Product | id, category_id, name_en, name_ur, aliases, default_unit, status |

\| Unit | id, code (kg, dozen, litre, piece, packet, bag), label |

\| PriceSource | id, name, type (official, manual, web, api), url, legal_status, reliability_note, status |

\| Price | id, product_id, market_id, price_pkr, unit_id, price_date, source_id, status (pending, verified, rejected, outdated), entered_by, entered_at, verified_by, verified_at, rejection_reason |

\| Profile (User) | id, name, email, phone, role, account_status |

\| Shopkeeper | id, user_id, shop_name, city_id, market_id, contact |

\| ShopkeeperList | id, shopkeeper_id, list_name |

\| ShopkeeperListItem | id, list_id, product_id, sort_order, optional_margin |

\| RateListLog | id, shopkeeper_id, list_id, generated_at, format |

\| CollectorRun | id, source_id, started_at, ended_at, records_read, records_stored, errors |

\| AuditLog | id, actor_id, action, entity, entity_id, old_value, new_value, at |

**### 6.2 Data Rules**

\- DR-01: Price uniqueness: (product_id, market_id, price_date, source_id) is unique.

\- DR-02: price_pkr is a decimal with 2 places, greater than 0.

\- DR-03: Prices are append-only; corrections create a new record linked to the old one.

\- DR-04: Master data uses soft delete via status.

\- DR-05: All timestamps stored in UTC and shown in Pakistan Standard Time (UTC+5).

**### 6.3 Data Retention and Privacy**

\- Price history retained indefinitely.

\- Personal data (name, email, phone) retained while account active; deleted or anonymized within 30 days of an account deletion request.

\- Logs containing personal data retained 90 days or less.

**---**

**## 7. Business Rules**

\| ID | Rule |

\|---|---|

\| BR-01 | Every price belongs to exactly one product, one market, one date and one source. |

\| BR-02 | Only Verified prices are shown as current rates to the public and in Rate Lists. |

\| BR-03 | Historical prices are never deleted when a new price is added. |

\| BR-04 | A price is Outdated when no newer Verified price exists after the staleness threshold (default 3 days). |

\| BR-05 | A Shopkeeper's Rate List contains only products selected by that Shopkeeper. |

\| BR-06 | A Shopkeeper may change their product lists at any time. |

\| BR-07 | Only Admin and Data Operator roles may create prices; only Admin may verify, and not their own entries when four-eyes rule is on. |

\| BR-08 | Automated sources require a recorded legal check before activation. |

\| BR-09 | A Shopkeeper margin (FR-SHOP-13) affects only the generated list, never the stored market price. |

\| BR-10 | Current and historical prices shall be visibly distinguished in the interface. |

**---**

**## 8. Data Accuracy and Verification Policy**

Pipeline: Source, Collection or Entry, Extraction, Validation (automatic rules), Verification (human), Publication.

Price status lifecycle:

\| Status | Meaning | Visible to public |

\|---|---|---|

\| Pending | Entered or collected, awaiting review | No |

\| Verified | Approved by an Admin | Yes |

\| Rejected | Not accepted, reason stored | No |

\| Outdated | Previously verified, beyond staleness threshold | Yes, labelled |

Validation rules: required fields, positive number, valid unit, no duplicates, spike check above threshold, product-unit plausibility range per product (configurable min and max per kg).

**---**

**## 9. Constraints, Assumptions, Dependencies**

See Sections 2.5 and 2.6.

**---**

**## 10. Legal, Ethical and Compliance Requirements**

\| ID | Requirement |

\|---|---|

\| LEG-01 | The system shall display a Privacy Policy and Terms of Use. |

\| LEG-02 | Scraping shall respect robots.txt and terms of each source; sources without permission shall not be scraped. |

\| LEG-03 | Source attribution shall be shown for every price. |

\| LEG-04 | Prices are informational; the site shall display a disclaimer that actual shop prices may differ. |

\| LEG-05 | Personal data handling shall follow Pakistan's applicable data protection law in force at launch (to be confirmed, see Appendix B). |

**---**

**## 11. MVP Scope, Release Plan and Acceptance Criteria**

**### 11.1 Release Plan**

\| Release | Content |

\|---|---|

\| 1.0 MVP | AUTH, SRCH, LOC, PRC, HIST (table), SHOP (all Must), DATA manual entry, VER (Must), ADM (Must), PDF and print |

\| 1.1 | Charts, comparison, bulk import, Urdu UI, reports, audit log polish, notifications |

\| 1.2 | Collector in production (approved sources), more markets |

\| 2.0 | More cities and categories, mobile app, alerts, analytics |

\| Future | Prediction, user-submitted prices, WhatsApp |

**### 11.2 System Acceptance Criteria**

The release is accepted when:

1\. All Must requirements pass their verification method.

2\. No open critical or high severity defects.

3\. Security checklist (NFR-SEC) passed, including cross-shopkeeper access tests.

4\. NFR-PERF-01 and NFR-PERF-02 measured and met.

5\. Usability test NFR-USAB-02 met.

6\. Backup restore test completed.

7\. At least 30 products and 3 markets of Abbottabad loaded with Verified prices for at least 7 consecutive days.

8\. Works on desktop and mobile in supported browsers.

**### 11.3 Excluded from 1.0**

Mobile apps, prediction, WhatsApp, user-submitted prices, payments, cross-city comparison, production scraping.

**---**

**## 12. Risks**

\| ID | Risk | Likelihood | Impact | Mitigation |

\|---|---|---|---|---|

\| R-01 | No reliable daily price source | High | Critical | Start with manual entry by trained operators; agreement with market committee; show dates clearly |

\| R-02 | Scraping blocked or illegal | Medium | High | Official/manual sources first; legal check (FR-DATA-08) |

\| R-03 | Wrong prices published | Medium | High | Verification workflow, spike detection, source display |

\| R-04 | Low adoption by shopkeepers | Medium | High | Free rate list, simple UI, print-ready PDF, pilot with a few shops |

\| R-05 | Free-tier limits exceeded | Medium | Medium | Monitor usage, caching, upgrade plan path |

\| R-06 | Data entry workload too high | Medium | Medium | Bulk entry grid, limit MVP to about 30 to 50 products |

\| R-07 | Security breach of accounts | Low | High | Managed auth, RLS, rate limiting, security review |

\| R-08 | Scope creep | High | Medium | MoSCoW priorities, release plan |

**---**

**## 13. Requirements Traceability Matrix (summary)**

\| Objective | Use Cases | Requirements | Verification |

\|---|---|---|---|

\| O1 Current prices for users | UC-01 | FR-SRCH, FR-LOC, FR-PRC | Test, Demo |

\| O2 Better purchase decisions | UC-02, UC-03 | FR-HIST, FR-CMP | Test |

\| O3 Location based rates | UC-01 | FR-LOC | Test |

\| O4 Historical records | UC-02 | FR-HIST-01 to 05, BR-03 | Test |

\| O5 Shopkeeper rate list | UC-05, UC-06 | FR-SHOP | Test, Demo |

\| O6 Reduce manual list work | UC-06 | FR-SHOP-07 to 10 | Test |

\| O7 Organized price data | UC-07, UC-09 | FR-DATA, FR-ADM | Test |

\| O8 Reliable data with date and source | UC-08 | FR-PRC-02, FR-VER, BR-02 | Test, Inspect |

\| O9 Expansion to other cities | UC-09 | NFR-SCAL, FR-ADM-02 | Demo, Analysis |

A full per-requirement matrix (requirement, design element, test case ID, status) should be maintained in a spreadsheet during development.

**---**

**## 14. Appendices**

**### Appendix A: Initial Product List (sample for MVP)**

Vegetables: tomato, potato, onion, carrot, cabbage, cauliflower, peas, spinach, cucumber, green chilli, garlic, ginger, lemon, okra, brinjal.

Grocery: sugar, flour (atta), rice, cooking oil, ghee, lentils (daal masoor, mash, moong, chana), tea, salt, eggs, milk.

(Final list to be confirmed with market sources.)

**### Appendix B: To Be Determined (TBD)**

\| ID | Item | Owner |

\|---|---|---|

\| TBD-01 | Final list of Abbottabad markets and their data sources | Team |

\| TBD-02 | Staleness threshold (default 3 days) and spike threshold (default 30 percent) | Team and operators |

\| TBD-03 | Applicable data protection law and policy text | Team |

\| TBD-04 | Email versus mobile (SMS) verification for shopkeepers | Team |

\| TBD-05 | Domain name and hosting plan | Team |

**### Appendix C: Glossary additions**

Urdu terms: Narkh (rate), Mandi (wholesale market), Bazaar (retail market).

**### Appendix D: Related Documents**

\- 02-Narkhnama-Diagrams.md

\- 03-Narkhnama-Tech-Stack.md