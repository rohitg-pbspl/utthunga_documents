# Utthunga ERP — Buying Module User Manual

| | |
|---|---|
| **System** | Utthunga ERP (ERPNext v15 + Utthunga Customizations) |
| **Module** | Buying (Procurement) |
| **Audience** | End users (Requesters, Purchase team, Approvers, Stores/QC, Accounts) and functional consultants |
| **Version** | 1.0 |
| **Date** | July 2026 |
| **Apps covered** | `erpnext` (standard), `utthunga_customizations`, `utthunga_manufacturing` |

> **How to read this manual:** Every chapter first explains the **Standard ERPNext behaviour**, then a clearly marked **🔶 Utthunga Customizations** section describes what has been added or changed for Utthunga. Anything not mentioned in a customization section behaves exactly as standard ERPNext.

---

## Table of Contents

1. [Introduction](#1-introduction)
2. [Buying Module Overview](#2-buying-module-overview)
3. [The Procurement Lifecycle](#3-the-procurement-lifecycle)
4. [User Roles and Permissions](#4-user-roles-and-permissions)
5. [Setup and Master Data](#5-setup-and-master-data)
   - 5.1 Supplier (with vendor onboarding & approval)
   - 5.2 Item (buying & quality settings)
   - 5.3 Buying Settings
   - 5.4 Project Budget prerequisite
6. [Material Request](#6-material-request)
7. [Request for Quotation (RFQ)](#7-request-for-quotation-rfq)
8. [Supplier Quotation](#8-supplier-quotation)
9. [Purchase Order](#9-purchase-order)
10. [Purchase Order Amendment (Utthunga Document)](#10-purchase-order-amendment-utthunga-document)
11. [Purchase Receipt and Quality Inspection](#11-purchase-receipt-and-quality-inspection)
12. [Purchase Invoice](#12-purchase-invoice)
13. [Subcontracting](#13-subcontracting)
14. [WBS-Based Project Budget Tracking (Utthunga Feature)](#14-wbs-based-project-budget-tracking-utthunga-feature)
15. [Approval Workflows](#15-approval-workflows)
16. [Reports](#16-reports)
17. [Print Formats](#17-print-formats)
18. [Notifications and Automation](#18-notifications-and-automation)
19. [Common Business Scenarios](#19-common-business-scenarios)
20. [Error Messages and Troubleshooting](#20-error-messages-and-troubleshooting)
21. [Frequently Asked Questions](#21-frequently-asked-questions)
22. [Best Practices](#22-best-practices)
- [Appendix A — Custom Field Reference](#appendix-a--custom-field-reference)
- [Appendix B — Naming Series Reference](#appendix-b--naming-series-reference)

---

## 1. Introduction

### 1.1 Purpose of this manual

This manual describes how to use the **Buying module** of Utthunga ERP. It covers the complete procure-to-pay cycle — from raising a Material Request to receiving and quality-checking goods and booking the Purchase Invoice — including all Utthunga-specific features such as **WBS-based project budget control**, **PO Type classification**, **supplier quotation comparison**, **multi-level approval workflows**, **inward quality inspection**, and **automatic supplier e-mails**.

### 1.2 Who should use this manual

| Audience | Most relevant chapters |
|---|---|
| Employees who request materials | 3, 6, 14, 19 |
| Purchase / procurement team | All chapters |
| Approvers (managers, finance, MD) | 3, 9, 10, 14, 15 |
| Stores / warehouse / QC team | 11, 13 |
| Accounts payable team | 12, 16 |
| Functional consultants / administrators | All chapters + appendices |

### 1.3 Conventions used

- 📘 **Note** — additional information.
- 💡 **Tip** — a recommendation that saves time.
- ⚠️ **Warning** — an action that can block a document or cannot be undone.
- 🔶 **Utthunga customization** — behaviour specific to Utthunga ERP, not found in standard ERPNext.
- `Field Name` — a field on the screen; **Button** — a clickable action.
- *[Screenshot placeholder]* — where a screenshot should be inserted in the published manual.

---

## 2. Buying Module Overview

### 2.1 What the Buying module does

The Buying module manages everything the company purchases — raw materials, components, services, consumables, fixed assets, and subcontracted work. It provides:

- **Demand capture** — internal requests for material (Material Request).
- **Sourcing** — asking suppliers for prices (Request for Quotation) and recording their offers (Supplier Quotation).
- **Ordering** — legally binding orders to suppliers (Purchase Order) and controlled changes to them (Purchase Order Amendment 🔶).
- **Receiving** — recording goods received, with inward quality inspection (Purchase Receipt + Quality Inspection).
- **Billing** — recording supplier bills for payment (Purchase Invoice).
- **Supplier management** — vendor onboarding, classification, and approval.
- **Subcontracting** — sending raw material to vendors and receiving finished goods back.

### 2.2 How Utthunga ERP extends the standard module

🔶 The key Utthunga additions on top of standard ERPNext Buying:

1. **WBS-based project budget tracking** — purchase lines for controlled projects must carry a **WBS Code** (Work Breakdown Structure code). The system tracks committed and actual spend per WBS element and blocks purchases that exceed the approved project budget (Chapter 14).
2. **PO Type classification** — every purchase is classified as *Material & Services – Domestic / Import* or *Fixed Asset – Domestic / Import*. The classification is mandatory from Material Request through Purchase Invoice and drives currency and reverse-charge validations.
3. **Multi-level approval workflows** — Material Requests, Supplier Quotations, Purchase Orders, PO Amendments, Purchase Invoices and Suppliers follow approval workflows with value-based routing (e.g. above ₹1,00,000 goes to the MD) (Chapter 15).
4. **Supplier Quotation comparison** — a built-in side-by-side comparison of competing quotations with rate-revision history, commercial terms, last-purchase history, and a printable A3 comparison sheet.
5. **Purchase Order Amendment** — a dedicated, approval-controlled document for changing or short-closing submitted Purchase Orders (Chapter 10).
6. **Quality-controlled receiving** — items flagged for inward inspection are received into a Quality Inspection warehouse; Quality Inspections move accepted/rejected quantities automatically and can trigger Repair & Rework.
7. **Vendor onboarding controls** — vendor type (Domestic/International), MSME classification, temporary-vendor handling, and a Supplier approval workflow that locks approved supplier records.
8. **Automation & alerts** — automatic PO e-mail to the supplier on submission, insufficient-stock alerts, advance-payment alerts to Accounts, working-hours approval reminders, budget threshold alerts, and overdue quality-inspection reminders.
9. **Company naming series** — buying documents use Utthunga-specific numbering such as `PO-MM-YY-#####` (Appendix B).

---

## 3. The Procurement Lifecycle

### 3.1 Process flow diagram

```
                     ┌───────────────────────────────────────────────────────┐
                     │        PROJECT BUDGET (Budget Tracking) 🔶            │
                     │   WBS codes · budgeted qty & amount per element       │
                     └───────────────┬───────────────────────────────────────┘
                                     │ buying documents validate against budget
                                     ▼
 ┌──────────────┐    ┌──────────────┐    ┌────────────────┐    ┌──────────────────┐
 │   MATERIAL   │──▶ │  REQUEST FOR │──▶ │    SUPPLIER    │──▶ │  PURCHASE ORDER  │
 │   REQUEST    │    │  QUOTATION   │    │   QUOTATION    │    │  (approval 🔶,   │
 │ (approval 🔶)│    │ (ask price)  │    │ (comparison 🔶,│    │  budget commit 🔶,│
 └──────┬───────┘    └──────────────┘    │  approval 🔶)  │    │  auto e-mail 🔶) │
        │                                └────────────────┘    └───────┬──────────┘
        │  (MR can also go directly to PO)                             │
        └──────────────────────────────────────────────────────────────┤
                                                                       ▼
                                                     ┌──────────────────────────────┐
        ┌──────────────────────┐   changes needed?   │      PURCHASE RECEIPT        │
        │  PURCHASE ORDER      │◀────────────────────│  goods in → QC warehouse 🔶  │
        │  AMENDMENT 🔶        │                     │  Quality Inspection →        │
        │  (short close /      │                     │  accepted / rejected stock 🔶│
        │   qty & rate change) │                     └──────────────┬───────────────┘
        └──────────────────────┘                                    │
                                                                    ▼
                                                     ┌──────────────────────────────┐
                                                     │      PURCHASE INVOICE        │
                                                     │  (approval 🔶, WBS actuals 🔶)│
                                                     └──────────────┬───────────────┘
                                                                    ▼
                                                     ┌──────────────────────────────┐
                                                     │   PAYMENT (Accounts module)  │
                                                     └──────────────────────────────┘
```

### 3.2 Lifecycle in words

1. **Material Request (MR)** — a requester lists the items needed with quantity, required-by date, 🔶 the **Project**, **PO Type**, and the **WBS Code** of the budget line that will pay for each item. The MR goes through approval; the system warns the Purchase Manager automatically if warehouse stock is insufficient.
2. **Request for Quotation (RFQ)** — the purchase team sends the item list to suppliers. 🔶 An RFQ must be created from one (and only one) Material Request.
3. **Supplier Quotation (SQ)** — each supplier's offer is recorded (one SQ per supplier per RFQ 🔶). The **Supplier Comparison** tab shows all offers side by side; negotiated rate changes are captured as **rate revisions**; the comparison sheet is printed and the winning quotation is approved through the SQ workflow.
4. **Purchase Order (PO)** — the approved offer becomes a binding order. 🔶 The PO carries the PO Type (validating currency), requires every line to trace back to a Material Request, checks the WBS budget, and routes through value-based approval. On final approval the PO is e-mailed to the supplier automatically (if enabled).
5. **Purchase Order Amendment (POA)** 🔶 — any post-submission change (quantity, rate, short close) is made through an approval-controlled amendment document, never by editing the PO directly.
6. **Purchase Receipt (PR)** — stores receive the goods. 🔶 Items flagged *Inward Inspection Required* are received into the **Quality Inspection warehouse**; a **Quality Inspection** per item records accepted/rejected quantities and automatically transfers stock to the accepted or rejected warehouse. Rejects can go to **Repair and Rework**.
7. **Purchase Invoice (PI)** — accounts book the supplier bill against the PO/PR. 🔶 Actual spend on service items posts to the WBS budget; the bill routes through the AP approval workflow.
8. **Payment** — accounts pay the supplier (Payment Entry — covered in the Accounts manual). 🔶 POs with advance payment terms alert the Accounts team automatically at submission.

📘 **Note:** Not every purchase needs all steps — but in Utthunga ERP the chain **MR → RFQ → SQ** is enforced by validation (RFQ items must come from an MR; SQ items must come from an RFQ), and non-subcontracted **PO lines must reference a Material Request**. Plan on the full chain for all regular purchases.

*[Screenshot placeholder: Buying workspace home page]*

---

## 4. User Roles and Permissions

### 4.1 Roles used in the Buying module

Standard ERPNext provides the **Purchase User**, **Purchase Manager**, and **Purchase Master Manager** roles for document create/read/submit rights. 🔶 Utthunga adds workflow-specific roles used in approvals:

| Role | Typical responsibility in buying |
|---|---|
| Purchase User | Creates MRs, RFQs, SQs, POs; initiates approvals |
| Purchase Manager | First-line purchase approval; manages the buying process |
| Deputy Purchase Head / Purchase Head | Higher-value purchase approvals (services flow) |
| Purchase Executive | Purchase initiation in the manufacturing (EOU) flow |
| Engineer | Technical approval of Material Requests |
| Projects Manager / Project Cost Controller | Project-side approvals and budget control |
| BU Head | Business-unit approval |
| Finance Controller | Financial approval of SQs, POs, amendments, suppliers |
| MD | Final approval above value thresholds (₹1,00,000) |
| Store Manager | Handles approved MRs and receiving |
| Production Head / PPC Head / Plant Head / Manufacturing Head | Approvals in the manufacturing (EOU) Material Request flows |
| AP Manager / Accounts Executive / AR Executive | Supplier onboarding and Purchase Invoice approvals |
| System Manager | Administration, cancellations |

### 4.2 🔶 Project-based approver resolution

For project documents, Utthunga ERP can resolve **who exactly** may approve — not just which role. Each Project record names its stakeholders (`Project Manager`, `BU Head`, `MD`, `Engineer`, `Finance Controller`, `Purchase Manager`, `Purchase User`, `Project Cost Controller`). When a workflow has *Project-Based Role Validation* enabled, only the user named on that project may perform the transition; anyone else sees:

> *"You are not allowed to perform this action. Only {user} can perform this action."*

The same mapping drives who receives approval e-mails and reminders (Chapter 18).

### 4.3 🔶 Automatic access sharing on Material Requests

When a Material Request is saved, the system automatically creates a **ToDo** ("Material Request {name} Has Assigned To You") and shares the document (read/write) with the project's **Project Manager**, **BU Head**, and all **Engineer Users** configured on the Project — so approvers always see the MRs waiting for them.

### 4.4 Permissions notes for administrators

- Role permissions for the buying doctypes follow the standard ERPNext role-permission matrix (System Manager → Role Permission Manager).
- 🔶 A project-based row-level visibility filter (users see only documents of projects where they are a named stakeholder) exists in the codebase for PO, PI, PR, MR and Subcontracting Receipt but is **currently disabled**. Consultants should be aware it can be enabled per doctype.
- Workflow "Allow Edit" per state further restricts who can modify a document while it awaits approval (Chapter 15).
---

## 5. Setup and Master Data

Before buying documents can be created, the following master data must exist. Most of this is one-time setup done by the administrator or purchase manager.

### 5.1 Supplier

**Purpose:** the master record of every vendor the company buys from.

**Standard ERPNext behaviour**

- Created under **Buying → Supplier**.
- Key fields: `Supplier Name`, `Supplier Group`, `Supplier Type` (Company/Individual), `Country`, `Tax ID / GSTIN`, `Default Currency`, `Default Price List`, `Payment Terms`, contact and address links, and the `Disabled` / `Is Frozen` flags. A disabled or frozen supplier cannot be used on new buying documents.

**🔶 Utthunga customizations**

*Numbering and form behaviour*

- Suppliers are numbered automatically as `SUP-MM-YY-#####`; the naming-series selector is hidden — the number is assigned on save.
- `Default Currency` and `Payment Terms` are **mandatory**.
- Quick Entry is disabled — suppliers are always created on the full form.

*Vendor classification fields*

| Field | Rules |
|---|---|
| `Vendor Type` | **Mandatory.** `Domestic` or `International` |
| `Vendor Classification` | Visible and **mandatory when Vendor Type = Domestic**: `MSME` or `Non MSME` |
| `MSME Number` | Visible and **mandatory when Classification = MSME** |
| `Temporary Vendor` | **Mandatory.** `Yes` / `No`. ⚠️ Cannot be changed after the supplier is saved — *"Temporary Vendor status cannot be changed after the Supplier is saved"* |
| `Account Number`, `IFSC Code` | Bank details for payment |

*Currency rules (validated on save)*

- Domestic vendor → `Default Currency` must be **INR** (*"For Domestic vendor type, Default Currency must be INR"*).
- International vendor → `Default Currency` must **not** be INR (*"For International vendor type, Default Currency cannot be INR"*).

*Supplier approval workflow* (active workflow "Supplier 2.0")

| Step | State | Who acts | Action |
|---|---|---|---|
| 1 | Draft | AR Executive | **Send for AP Manager's Approval** (only for suppliers with `Temporary Vendor = No`) |
| 2 | Sent for AP Manager's Approval | AP Manager | **Send for Finance Controller Approval** |
| 3 | Sent for Finance Controller's Approval | Finance Controller | **Approve** |
| 4 | Approved | Finance Controller | **Send Back to Draft** (to reopen for edits) |

⚠️ Once a supplier reaches **Approved**, the entire form becomes read-only. Changes require the Finance Controller to send the record back to Draft.

*Temporary vendors*

- A supplier created with `Temporary Vendor = Yes` skips the approval chain (intended for one-off/urgent purchases).
- A **Convert to Regular Vendor** button appears on temporary vendors. It asks *"Are you sure you want to convert this Temporary Vendor to a Regular Vendor?"*, sets Temporary Vendor to "No", and re-numbers the supplier with a fresh series number. The converted vendor then follows the normal approval flow.

*[Screenshot placeholder: Supplier form with vendor classification fields]*

💡 **Tip:** Check whether a supplier already exists before creating a new one — duplicates split purchase history and break price comparisons.

### 5.2 Item (buying and quality settings)

**Standard ERPNext behaviour**

- Created under **Stock → Item**. Buying-relevant: `Is Purchase Item` (must be checked), `Default UOM` / `Purchase UOM`, `Lead Time in Days`, `Minimum Order Qty`, `Last Purchase Rate`, default supplier, and default expense account.

**🔶 Utthunga customizations (fields that affect buying)**

| Field | Effect on buying documents |
|---|---|
| `Purchasing Group` | Procurement categorisation (shown in the Purchasing tab) |
| `Rounding Value` | Order quantities on MR/PO are **rounded up** to the nearest multiple of this value automatically |
| `Inward Inspection Required` | When checked, receipts of this item must go through Quality Inspection (Chapter 11) |
| `GR Processing Time` (days) | Deadline for completing the incoming Quality Inspection after receipt; overdue inspections trigger a reminder e-mail |
| `Has Expiry Date` (custom) | When checked (available for batched items), every Purchase Receipt line must carry an `Expiry Date` |
| `Start Date` / `End Date` | Copied onto Material Request lines for planning |
| `ABC Classification`, `Procurement Type`, part-number fields | Reference/classification data |
| Item Reorder → `Maximum Stock Level` | MR/PO warns when projected stock in a warehouse would exceed this level |

### 5.3 Buying Settings

Found under **Buying → Settings → Buying Settings**. Administrator only.

**Standard ERPNext behaviour (main switches)**

| Setting | Effect |
|---|---|
| `Supplier Naming By` | Number suppliers by name or by series |
| `Purchase Order Required` | If "Yes", a Purchase Invoice/Receipt cannot be made without a PO |
| `Purchase Receipt Required` | If "Yes", a Purchase Invoice cannot be made without a Receipt |
| `Maintain Same Rate Throughout Purchase Cycle` | Prevents rate changes between PO, PR and PI |
| `Role Allowed to Over Deliver/Receive` (Stock Settings) | Who may bypass over-ordering limits |

**🔶 Utthunga customizations**

*Vendor Email Configuration tab* — controls automatic supplier e-mails:

| Field | Purpose |
|---|---|
| `Email supplier on PO submit` | Master switch: every finally-approved Purchase Order is e-mailed to the supplier automatically |
| `PO Auto Email Print Format` | The print format attached as PDF to that e-mail |
| `PO Email Template` | The e-mail subject/body template used |

⚠️ **Warning:** If the master switch is ON but no print format or template is selected, the e-mail may fail or go out unformatted. Always configure all three together.

*Exchange rate* — `Use Transaction Date Exchange Rate Globally`: when enabled, downstream documents created from a PO/PR/PI (e.g. **Create → Purchase Receipt**) refresh the currency exchange rate to the new document's transaction date instead of copying the source document's rate.

*[Screenshot placeholder: Buying Settings — Vendor Email Configuration tab]*

### 5.4 Project and Budget Tracking (prerequisite for project purchases)

🔶 For project procurement under budget control, the project's **Budget Tracking** document must exist and be submitted before buying starts. It defines the **WBS codes** — budget lines with quantities and amounts — that buying document lines must reference. Budget control applies to projects whose **Sub Department** has the *Budget Mandatory* flag. See Chapter 14.

---

## 6. Material Request

### 6.1 Purpose

A **Material Request (MR)** is the formal internal request that starts the buying cycle: "we need these items, in these quantities, by this date, charged to this budget line." It creates no stock or accounting entries — it is a demand document that drives RFQs and Purchase Orders.

Material Request types (standard): `Purchase` (the normal buying case), `Material Transfer`, `Material Issue`, `Manufacture`, `Customer Provided`. This chapter deals mainly with type **Purchase**.

### 6.2 Prerequisites

- Item masters exist and are purchasable.
- 🔶 The **Project** is mandatory on every MR; for budget-controlled projects the Budget Tracking must be submitted and you must know the **WBS code** for each item.
- 🔶 For type Purchase, decide the **PO Type** (Material & Services / Fixed Asset, Domestic / Import) up front — items must match it.

### 6.3 Creating a Material Request — step by step

1. Go to **Buying → Material Request → New**.
2. Select `Purpose` (Material Request Type) — normally **Purchase**.
3. 🔶 Select the **PO Type** (visible for Purchase requests): *Material & Services - Domestic* (default), *Material & Services - Import*, *Fixed Asset - Domestic*, or *Fixed Asset - Import*.
4. Set `Transaction Date` and `Required By` (the date the material is needed).
5. 🔶 Select the `Project` (**mandatory**); `Project Name` fills in automatically. Fill the accounting dimensions — `Business Unit`, `Cost Center`, `Department`, `Profit Center`, `Sub Department` — per company policy (some are filled from the project).
6. In the **Items** table, for each line:
   - 🔶 **WBS Code** — the budget line this item is charged to. The dropdown offers only WBS codes belonging to the selected project **and** that item.
   - `Item Code`, `Quantity`, `UOM`, `Required By`, `Warehouse`.
   - 🔶 Reference fields: `Vendor` (suggested supplier), `Target Price`, `Start Date` / `End Date` (fetched from the Item), `Total Warehouse Qty` (current stock across raw-material warehouses, filled automatically).
   - 🔶 A small **stock-by-warehouse table** (with stock ageing) appears under the open item row for quick availability checks.
7. Save. 🔶 Quantities are rounded **up** to the item's `Rounding Value` automatically, and the MR `Total` is computed from the line amounts.
8. Submit / send for approval — see the Material Request workflow in Chapter 15.

*[Screenshot placeholder: Material Request form with WBS Code column and stock table]*

### 6.4 Field reference (header)

| Field | Std/🔶 | Description |
|---|---|---|
| `Series` | 🔶 | Auto: `MR-MM-YY-#####` |
| `Purpose` | Std | Material Request Type |
| `PO Type` | 🔶 | Procurement classification (Purchase type only) — carried to the PO |
| `Transaction Date`, `Required By` | Std | Dates |
| `Project` | 🔶 | **Mandatory** on every MR |
| `Project Name` | 🔶 | Read-only, fetched from the Project |
| `Business Unit`, `Cost Center`, `Department`, `Profit Center`, `Sub Department` | 🔶 | Accounting dimensions |
| `Currency` / `Total` | 🔶 | Document currency and computed total of all line amounts |

### 6.5 Field reference (Items table)

| Column | Std/🔶 | Description |
|---|---|---|
| `WBS Code` | 🔶 | Budget line (filtered by project + item). First grid column |
| `Item Code`, `Required By`, `Quantity`, `UOM`, `Warehouse` | Std | Core demand data |
| `Rate`, `Amount` | Std | Estimated value (shown in grid) |
| `Actual Qty` | Std | Current stock in the selected warehouse |
| `Total Warehouse Qty` | 🔶 | Stock across raw-material warehouses (auto-filled) |
| `Vendor` | 🔶 | Suggested supplier for the line |
| `Target Price` | 🔶 | Expected purchase rate (negotiation reference) |
| `Start Date` / `End Date` | 🔶 | Planning dates fetched from the Item |
| `Department` | 🔶 | Line-level department |

### 6.6 Actions and buttons

**Standard buttons (on submitted MR):** **Create → Request for Quotation**, **Create → Supplier Quotation**, **Create → Pick List / Stock Entry** (transfer types), **Stop**, **Amend**.

**🔶 Create → Purchase Order (item picker):** opens the **"Select Items for Purchase Order"** dialog listing the MR's pending items with checkboxes. Select the lines to order and click **Create Purchase Order** — a PO is drafted for the *pending* quantity (requested − already ordered) of the selected lines. (*"Please select at least one item."* appears if nothing is ticked.)

**🔶 Update Item(s) (short close):**

- Appears on a **submitted** MR when at least one line is not fully ordered.
- Opens a dialog listing pending lines: Item Code, UOM, editable **Quantity**, read-only **Completed Quantity** (already ordered).
- **Set all to Completed Qty** short-closes the whole request; **Update Quantity** saves.
- Rules: quantity cannot go **below** the completed quantity (*"Quantity cannot be less than Completed Quantity (n)."*) and cannot be **increased** (*"Quantity cannot be increased (current quantity: n)."*).
- On success (*"Item quantities updated"*) the MR's percent-ordered and status recalculate (Pending / Partially Ordered / Ordered).

💡 **Tip:** Use **Update Item(s)** instead of cancelling an MR when requirements shrink — it preserves the audit trail and releases open demand cleanly.

📘 **Note:** When you create an RFQ or Supplier Quotation from an MR, only lines with **pending (unordered) quantity** are carried over.

### 6.7 Status flow

| Status | Meaning |
|---|---|
| Draft | Being prepared |
| Pending | Submitted, nothing ordered yet |
| Partially Ordered / Ordered | Conversion progress to POs |
| Transferred / Issued / Received | For non-purchase types |
| Stopped | No further processing allowed |
| Cancelled | Document cancelled |

In addition, the MR carries a **workflow state** (Sent for Engineer's Approval, Sent for Purchase Manager's Approval, …) while it moves through approval — see Chapter 15.

### 6.8 Validations and business rules

**Standard:** `Required By` cannot precede the transaction date; positive quantities; ordered quantity per line cannot exceed the requested quantity (plus allowance).

**🔶 Utthunga:**

| Rule | Behaviour |
|---|---|
| WBS mandatory | For budget-controlled projects, every line needs a WBS Code: *"Row #n: WBS Code is mandatory — Project X's Sub Department requires Budget Tracking."* |
| One project per document | *"All rows with a WBS Code must belong to the same Project."* |
| Budget commitment check | The MR's amount/qty per WBS plus all **other submitted MRs** on that WBS must stay within the budget allocation, else **"Budget Limit Exceeded"** (with a detail table) |
| PO Type ↔ item match | Items must match the selected PO Type (a fixed-asset PO Type cannot contain regular material lines, and vice versa): *"Row #n: The item … is …, which does not match the selected PO Type …"* |
| Quantity rounding | Quantities round **up** to the item's `Rounding Value` |
| Maximum stock warning | If projected stock exceeds the item's `Maximum Stock Level` for the warehouse, a non-blocking warning appears: *"…exceeds the maximum stock level (n)"* |
| Insufficient stock alert | On submission of a Purchase-type MR, if requested quantity exceeds available stock, the project's **Purchase Manager** is e-mailed automatically ("Material Request {name}: Insufficient Stock for Items") |
| Auto-assignment | On save, the project's PM, BU Head and Engineers get a ToDo + document share (see 4.3) |

---

## 7. Request for Quotation (RFQ)

### 7.1 Purpose

A **Request for Quotation** sends the same list of items to one or more suppliers and asks for prices — the paper trail for competitive sourcing.

### 7.2 Prerequisites

- 🔶 A **submitted Material Request** — RFQ lines *must* reference one; manual free-standing RFQs will not validate.
- Supplier masters with e-mail contacts if you want to e-mail the RFQ from the system.

### 7.3 Creating an RFQ — step by step

1. Open the submitted **Material Request → Create → Request for Quotation**. Items, pending quantities, and 🔶 WBS codes carry over.
2. In the **Suppliers** table add every supplier you want to ask; tick `Send Email` per supplier and verify the contact.
3. Check the **Items** table: quantities, UOM, required date, warehouse, 🔶 `WBS Code`.
4. Fill `Message for Supplier` — terms, delivery expectations.
5. Save and Submit; use **Send Emails to Suppliers** (standard) or print and send manually.

*[Screenshot placeholder: RFQ form with supplier table]*

### 7.4 Actions and buttons

- **Create → Supplier Quotation** — record a supplier's reply (one SQ per supplier).
- **Send Emails to Suppliers** (standard). Suppliers with portal access can quote online.

### 7.5 Status flow

Draft → Submitted; per-supplier status *Pending / Received* as quotations arrive. The workflow states **Send To Supplier / Sent To Supplier** exist for supplier-facing tracking where configured.

### 7.6 Validations and business rules

**Standard:** at least one supplier and one item; qty > 0.

**🔶 Utthunga:**

- Every item line must reference a Material Request: *"Material Request is mandatory for all Request for Quotation items. Missing in row(s): …"*.
- All items must come from the **same** MR: *"All items in Request for Quotation must be from the same Material Request. Found multiple Material Requests: …"*.
- The read-only `Material Request ID` is set automatically for traceability — one MR ⇒ one RFQ chain.

---

## 8. Supplier Quotation

### 8.1 Purpose

A **Supplier Quotation (SQ)** records a supplier's offer — rates, terms, validity. In Utthunga ERP it is also where competing offers are **compared side by side**, where negotiated **rate revisions** are tracked, and where the sourcing decision is **approved** before a PO is raised.

### 8.2 Prerequisites

- 🔶 A submitted **RFQ** — SQ lines must reference one (single RFQ per SQ).
- Supplier master for the quoting supplier.

### 8.3 Creating a Supplier Quotation — step by step

1. From the RFQ, click **Create → Supplier Quotation** and pick the supplier; items and 🔶 WBS codes carry over. (Portal suppliers generate this automatically.)
2. Enter the supplier's quoted `Rate` per item (⚠️ zero rates are rejected), taxes and charges as offered, and `Valid Till`.
3. 🔶 Select the **PO Type** (mandatory) and verify `Project`, `Business Unit`, `Cost Center`, `Profit Center`, `Sub Department`, `Department` (all mandatory). If the SQ traces to a Material Request, these dimensions are **auto-filled from the MR** (*"Values fetched from Material Request"*) — only blank fields are filled, your entries are never overwritten.
4. 🔶 Optional `Remarks` near the totals.
5. Save — numbered **SUQ-MM-YY-#####** — then send for approval (workflow below).

*[Screenshot placeholder: Supplier Quotation form]*

### 8.4 🔶 Supplier Comparison tab

Every Supplier Quotation has a **Supplier Comparison** tab showing all competing quotations of the same RFQ:

- Per item row: Sr No, Item Code, Qty, UOM, the **last three purchase rates** (rate, PO number, supplier), then one column per supplier with the quoted rate and revision columns (R1, R2, …), and totals.
- A supplier whose quotation is already workflow-**Approved** is tagged **(Approved)** in green.
- Column totals are colour-coded against this document's own total: **green** = cheaper, **red** = more expensive.
- Editable per-supplier commercial terms (while in draft): Discount (%), P & F Charges, Freight Charges, Payment Terms, Delivery, Guarantee, Deputation, Contact Person, Technical Clearance (Yes/No), Position, Approved Vendor (Yes/No), Remarks. Totals recompute as `Sub Total + P&F + Freight − Discount`.
- The terms you enter are synchronised automatically across all draft quotations of the same RFQ, so every SQ shows the same comparison.
- **Print → Print Supplier Comparison** produces an A3 landscape comparison sheet for the approval file.

💡 **Tip:** Fill in freight/discount/P&F before circulating for approval — landed totals often change which supplier is cheapest.

### 8.5 🔶 Rate revision (negotiation history)

1. While the SQ is in draft, click the **rate** on an item line (or the line's **Revise Rate** button).
2. In the **Revise Rate** prompt enter the new rate; keep **Is Revised Rate** ticked.
3. The system stores the original rate (`Initial Rate`), appends the new rate with a timestamp to the **Revised Rates** history, applies the new rate, and confirms *"Rate revised successfully."*
4. The history appears when the row is expanded and as R1, R2, … columns in the comparison.

📘 **Note:** Untick *Is Revised Rate* only for typo corrections — it changes the rate without a history entry.

### 8.6 Field reference (key fields)

| Field | Std/🔶 | Description |
|---|---|---|
| `Supplier`, `Valid Till` | Std | Quoting supplier, offer expiry |
| `PO Type` | 🔶 | Mandatory procurement classification |
| `RFQ ID` / `Material Request ID` | 🔶 | Read-only source references (set automatically) |
| `Project`, `Business Unit`, `Cost Center`, `Profit Center`, `Sub Department` | 🔶 | Mandatory dimensions, auto-fetched from the MR |
| `Remarks` | 🔶 | Free text near totals |
| Items → `WBS Code` | 🔶 | Budget line per item |
| Items → `Initial Rate` / `Revised Rates` | 🔶 | Read-only negotiation history |

### 8.7 Approval workflow (active)

| Step | State | Who acts | Action / routing |
|---|---|---|---|
| 1 | Draft | Purchase User | **Send for Purchase Manager's Approval** |
| 2 | Sent for Purchase Manager's Approval | Purchase Manager | **Send for Project Cost Controller Approval** (or **Reject**) |
| 3 | Sent For Project Cost Controller | Project Cost Controller | **Send for Finance Controller's Approval** |
| 4 | Sent for Finance Controller's Approval | Finance Controller | Grand total ≤ ₹1,00,000 → **Approve**; ≥ ₹1,00,000 → **Send for MD's Approval** (or **Reject**) |
| 5 | Sent for MD's Approval | MD | **Approve** / **Reject** |

**Approved** submits the document (docstatus 1); **Rejected** keeps it editable for correction.

### 8.8 Validations and business rules

**Standard:** valid supplier; tax template consistency.

**🔶 Utthunga:**

- Every item must reference an RFQ: *"Request for Quotation is mandatory for all Supplier Quotation items. Missing in row(s): …"*; all items from the **same** RFQ: *"All items in Supplier Quotation must be from the same Request for Quotation. Found multiple RFQs: …"*.
- *"Rate cannot be zero for item {item}."*
- Dimensions auto-fill from the MR; comparison terms are editable only in draft and are shared across sibling quotations of the RFQ.
---

## 9. Purchase Order

### 9.1 Purpose

The **Purchase Order (PO)** is the legally binding commitment to a supplier: items, quantities, rates, taxes, delivery schedule, and terms. It is the control point of the buying cycle — 🔶 in Utthunga ERP the PO is validated against the project's WBS budget, must trace to Material Requests, is classified by PO Type, routes through value-based approval, and is e-mailed to the supplier automatically on final approval.

### 9.2 Prerequisites

- Approved supplier (not disabled/frozen; temporary vendors for exceptional cases only).
- A submitted Material Request — 🔶 **every non-subcontracted PO line must reference one**.
- Usually an approved Supplier Quotation.
- 🔶 For budget-controlled projects: submitted Budget Tracking with headroom on each WBS code.

### 9.3 Creating a Purchase Order — step by step

1. Preferred paths (references and WBS codes carry over automatically):
   - **Supplier Quotation → Create → Purchase Order** (after comparison approval), or
   - **Material Request → Create → Purchase Order** (via the 🔶 item-picker dialog).
2. Header: `Supplier ID` (the supplier), 🔶 **PO Type** (mandatory — see 9.4), `Date`, `Required By`, `Currency`, `Company`, `Project`, and the mandatory dimensions 🔶 — `Business Unit`, `Profit Center`, `Department`, `Sub Department`, plus `Cost Center`.
3. Items table per line: 🔶 `WBS Code`, `Item Code`, `Quantity`, `Rate`, `Schedule Date`, `Warehouse`. 🔶 `Total Warehouse Qty` and a stock-by-warehouse box under the open row show current availability; tick `Update Price in Price List` on a line to update the buying price list with this PO's rate on submission.
4. Taxes and Charges: select the purchase tax template. 🔶 For **Import** PO Types the system prompts you to consider enabling **Is Reverse Charge** (*"For PO Type …, enable the Is Reverse Charge checkbox if required."*).
5. 🔶 Commercial detail fields printed on the PO: `Version`, `Delivery Schedule`, `Acceptance Criteria`, `IEC No`, `Reference`, `MRF No`, `Infra's PO Number`, `Mode of Delivery`, `Warranty`, `Taxes`, `Contact Person`, `Terms of Payment`, `Mode of Payment`.
6. `Payment Terms` — 🔶 the payment schedule is **mandatory**; mark advance instalments with `Is Advance` so Accounts is alerted automatically.
7. Save → numbered **PO-MM-YY-#####**. 🔶 Quantities round up to the item's `Rounding Value`; a max-stock warning appears if a warehouse would exceed its maximum stock level.
8. Send through the approval workflow (see 9.8). ⚠️ Validation runs the **budget check** — see 9.9.
9. 🔶 On final approval, the PO is e-mailed to the supplier automatically (if enabled in Buying Settings), with project stakeholders in CC.

*[Screenshot placeholder: Purchase Order form — items grid with WBS Code]*

### 9.4 🔶 PO Type and currency rules

`PO Type` (mandatory, default *Material & Services - Domestic*) classifies the purchase:

| PO Type | Currency rule (validated on save) |
|---|---|
| Material & Services - Domestic | Currency **must be** the company default (INR) |
| Fixed Asset - Domestic | Currency **must be** the company default (INR) |
| Material & Services - Import | Currency must **not** be the company default — select the import currency |
| Fixed Asset - Import | Currency must **not** be the company default |

Error messages: *"For PO Type …, Purchase Order Currency must be the Company Default Currency …"* / *"…cannot be the Company Default Currency … Please select an import currency."*

Items must match the PO Type (fixed-asset types only accept asset items, material/services types only regular items): *"Row #n: The item … does not match the selected PO Type …"*. The PO Type flows forward to the Purchase Receipt and Purchase Invoice automatically.

### 9.5 Vendor acknowledgment (🔶)

After submission, record the supplier's confirmation on the PO itself (both fields editable after submit):

- `Vendor Acknowledged` — tick when the supplier confirms the order.
- `Date` — becomes visible **and mandatory** once `Vendor Acknowledged` is ticked.

💡 **Tip:** Filter the PO list on `Vendor Acknowledged = No` to chase unconfirmed orders.

### 9.6 Actions and buttons

**Standard:** **Create → Purchase Receipt**, **Create → Purchase Invoice**, **Create → Payment Entry / Payment Request**, **Hold / Close**, **Amend** after cancel.

**🔶 Utthunga:**

- The standard *Update Items* button is **hidden**. In its place, an **Update Item(s)** button (visible on submitted POs that are not fully received/billed) opens the **Update Items** dialog listing every line: Item Code, **Short Close** checkbox, Reqd-by date, UOM/conversion, Qty, Rate, Received Qty, plus a header checkbox **Short Close Purchase Order** ("Check this box if you want to short close the Purchase Order"). Its primary button **Create Amendment** creates a **Purchase Order Amendment** (Chapter 10) — or **View Amendment** if one is already open.
- The PO dashboard links to its **Purchase Order Amendments** under "Related".

### 9.7 Status flow

Draft → To Receive and Bill → To Receive / To Bill → Completed, with On Hold / Closed / Cancelled as manual states — plus the **workflow state** while in approval (9.8). Cancelling a PO 🔶 releases its WBS budget commitment automatically.

### 9.8 Approval workflow

Routing depends on the profit centre (services vs manufacturing) and the value involved:

**Services flow (profit centre "Utthunga - Services"):**

| Step | State | Who acts | Action / routing |
|---|---|---|---|
| 1 | Draft | Purchase User | **Send for Purchase Manager's Approval** |
| 2 | Sent for Purchase Manager's Approval | Purchase Manager | **Send for Finance Controller's Approval** (or **Reject**) |
| 3 | Sent for Finance Controller's Approval | Finance Controller | **Send for the Deputy Purchase Head** (or **Reject**) |
| 4 | Sent for Deputy Purchase Head | Deputy Purchase Head | Value below threshold and no amount increase vs the MR → **Approve**; amount increased vs the MR or grand total above threshold → **Send for MD's Approval** (or **Reject**) |
| 5 | Sent for MD's Approval | MD | **Approve** / **Reject** |

**Manufacturing flow (profit centre "EOU" / "Utthunga - Manufacturing"):** Purchase Executive sends Draft → Purchase Manager → Finance Controller → **Manufacturing Head Approval** → MD approves.

**Approved** submits the PO. A submitted, approved PO that is later amended can carry the state **Sent To Supplier** after re-issue. 📘 The system tracks internally whether the PO's value **exceeds the originating MR's estimate** (`Change In Amount` flag) — such POs always escalate to the MD.

### 9.9 Validations and business rules

**Standard:** valid rates/quantities; schedule date; over-billing/receipt allowances; same-rate enforcement if configured.

**🔶 Utthunga:**

| Rule | Behaviour |
|---|---|
| Material Request mandatory | Non-subcontracted lines without an MR reference are blocked: *"Row n: Material Request is mandatory for item …."* (title "Missing Material Request") |
| WBS mandatory + one project | Same as MR (Chapter 6.8) |
| Budget commitment check | This PO + all other **submitted POs** per WBS must stay within allocation, else **"Budget Limit Exceeded"** with a detail table. Cancel releases the commitment |
| Over-ordering vs MR | PO qty per MR line may not exceed the requested qty (plus Stock Settings allowance). Without the bypass role: *"This document would exceed limit by Stock Qty n for item … against the linked Material Request Item."* (title "Limit Crossed"). With the bypass role, a warning appears instead |
| Amount increase flag | If the PO value exceeds the MR estimate, the internal `Change In Amount` flag is set → routes approval to the MD |
| PO Type ↔ currency / items | See 9.4 |
| Qty rounding & max-stock warning | As on the MR |
| Price list update | Lines with `Update Price in Price List` update the buying Item Price on submit |
| Advance payment alert | If any payment-schedule row is marked `Is Advance`, all **Accounts Users** are e-mailed "Advance Payment Required - Purchase Order {name}" on submit |
| Auto e-mail to supplier | On submit, if enabled: PDF of the configured print format to the supplier, project stakeholders in CC (Chapter 18) |

---

## 10. Purchase Order Amendment (Utthunga Document)

> 🔶 This entire chapter describes a custom Utthunga doctype (not part of standard ERPNext).

### 10.1 Purpose

Once a PO is submitted and approved, it must not be edited casually — but real life brings quantity changes, rate changes, and short closures. The **Purchase Order Amendment (POA)** is the controlled way to change a submitted PO: it captures the requested changes, routes them through approval, and applies them to the PO.

### 10.2 Creating an amendment — step by step

1. Open the submitted PO → **Update Item(s)** → the **Update Items** dialog.
2. Adjust quantities/rates per line, tick **Short Close** on lines to close, or tick **Short Close Purchase Order** to close the whole order.
3. Click **Create Amendment** — a POA (numbered `POA-FY-#####`) is created referencing the PO with the amended item lines.
4. The POA routes through approval (10.3). On approval/submission the changes take effect against the PO; the PO's dashboard shows all its amendments.

### 10.3 Approval workflow (active)

| Step | State | Who acts | Action / routing |
|---|---|---|---|
| 1 | Draft | Purchase User | Regular change → **Send for Purchase Manager's Approval**. Pure short-close (no value change) → **Submit** directly (no approval needed) |
| 2 | Sent for Purchase Manager's Approval | Purchase Manager | **Send for Finance Controller's Approval** (or **Reject**) |
| 3 | Sent for Finance Controller's Approval | Finance Controller | **Send for the Deputy Purchase Head** (or **Reject**) |
| 4 | Sent for Deputy Purchase Head | Deputy Purchase Head | Total ≤ ₹1,00,000 → **Approve**; ≥ ₹1,00,000 → **Send for MD's Approval** (or **Reject**) |
| 5 | Sent for MD's Approval | MD | **Approve** / **Reject** |

📘 **Note:** Short-closing releases the unreceived/unbilled portion of the PO (and its budget commitment headroom) without cancelling what has already been received or billed.

---

## 11. Purchase Receipt and Quality Inspection

### 11.1 Purpose

The **Purchase Receipt (PR)** records goods arriving from the supplier and posts them into stock. 🔶 In Utthunga ERP, receiving is quality-controlled: items flagged for inward inspection are received into a **Quality Inspection warehouse**, inspected item by item, and only then moved to the accepted (usable) warehouse — automatically.

### 11.2 Prerequisites

- Submitted, approved Purchase Order.
- 🔶 Item masters correctly flagged: `Inward Inspection Required`, `GR Processing Time`, `Has Expiry Date` (Chapter 5.2).
- 🔶 A designated **Quality Inspection warehouse** and, where used, warehouse locations.

### 11.3 Creating a Purchase Receipt — step by step

1. Open the PO → **Create → Purchase Receipt**. Items, quantities, rates, 🔶 WBS codes and the PO Type carry over (the exchange rate refreshes to the receipt date if globally enabled).
2. Header: verify `Posting Date`; fill the 🔶 mandatory dimensions (`Project`, `Business Unit`, `Cost Center`, `Profit Center`, `Department`, `Sub Department`) and the warehouses:
   - `Accepted Warehouse` (standard `Set Warehouse`) + 🔶 `Accepted Warehouse Location` — where inspection-free goods go.
   - 🔶 `Quality Inspection Warehouse` + `Quality Warehouse Location` — where inspection-required goods go.
3. Items table: received quantity per line. 🔶 The system routes each line's warehouse automatically — lines with `Inward Inspection Required` (fetched from the Item, read-only) get the **Quality Inspection warehouse**; other lines get the accepted warehouse. Warehouse-location pickers are filtered to the selected warehouse.
4. 🔶 For items with expiry tracking: fill `Expiry Date` per line (must be after the posting date).
5. Save and Submit. Stock posts into the respective warehouses. ⚠️ Rejected-warehouse fields are managed by the Quality Inspection flow and are cleared/read-only on the PR itself (an info message confirms this).

*[Screenshot placeholder: Purchase Receipt with QI warehouse fields]*

### 11.4 🔶 Quality Inspection flow

**Step 1 — create the inspections.** On the submitted PR, the **Create → Quality Inspection(s)** button (visible when any line requires inward inspection) generates one **Quality Inspection (QI)** per inspection-required line and shows a summary table (Item / Quality Inspection / Status). Lines that already have a QI are linked instead of duplicated. (*"No items require Quality Inspection in this Purchase Receipt."* appears when nothing needs inspection.)

**Step 2 — inspect.** Each QI is created as *Incoming* inspection with `Sample Size` = received quantity and the PR's warehouse as `Source Warehouse`. The inspector records:

- `Accepted Qty` and `Rejected Qty` — ⚠️ together they must equal the sample size: *"Total Inspected Quantity (Accepted + Rejected) must equal Sample Size for Item …"*.
- `Accepted Warehouse` (+ location) — mandatory when accepted qty > 0; `Rejected Warehouse` (+ location) — mandatory when rejected qty > 0. Source, accepted, and rejected warehouses must all differ.
- For serialised/batched items: the accepted serial/batch entries are pre-filled from the receipt; move rejects with **Add Serial/Batch Nos** on the rejected grid (**Refresh Entries** resets the accepted grid). The system validates serial↔batch consistency, totals, and duplicates.
- Readings/parameters per the item's inspection criteria (standard ERPNext).

**Step 3 — submit the QI.** On submission the system automatically:

1. Creates and submits a **Stock Entry (Material Transfer)** moving the accepted quantity from the QI warehouse to the accepted warehouse, and the rejected quantity to the rejected warehouse (*"Stock Entry {name} created."*). Accounting dimensions are copied from the PR.
2. Updates the PR line's read-only counters `Quality Inspection Accepted Qty` / `Quality Inspection Rejected Qty` (*"Item … updated in Purchase Receipt … Accepted: n, Rejected: n"*).
3. Cancelling the QI reverses these updates.

**Step 4 — rejects (optional).** On a submitted QI with rejected quantity, the **Create Repair and Rework** button creates a **Repair and Rework** document (with the item's BOM) to process the rejects; re-inspection moves recovered quantity from rejected to accepted.

**Deadline watch:** items with a `GR Processing Time` must be inspected within that many days of receipt — overdue inspections trigger a daily reminder e-mail to the PO owner: *"Reminder: Quality Inspection pending for {item} (PR: {name})"*.

### 11.5 Validations and business rules

**Standard:** over-receipt allowance vs PO; batch/serial validity; stock ledger posting rules.

**🔶 Utthunga:**

| Rule | Message |
|---|---|
| Non-inspection item placed in QI warehouse | *"Row #n: Please enable Inward Inspection Required for Item …, or select a warehouse other than the Quality Inspection Warehouse."* |
| QI warehouse missing for inspection items | **"Missing Quality Inspection Warehouse"** — *"Quality Inspection Warehouse is required for the following rows: …"* |
| QI warehouse ≠ item warehouse | **"Invalid Quality Inspection Warehouse"** — *"…must match the item's warehouse for the following rows: …"* |
| Expiry date missing / in the past | **"Missing Expiry Date"** / **"Invalid Expiry Date"** (must be after posting date) |
| Location-tracked warehouse without location | *"Row #n: Target Warehouse Location is required for Item …"* |
| One QI per PR line | *"Quality Inspection already exists for Purchase Receipt Item …"* |
| Sample size integrity | *"Sample Size (n) must match Received Quantity (n) for Item …"* |
| Warehouse conflicts on QI | *"Source and Accepted Warehouses cannot be same."* (and rejected-warehouse variants) |

---

## 12. Purchase Invoice

### 12.1 Purpose

The **Purchase Invoice (PI)** records the supplier's bill in the accounts: it debits expense/asset accounts, credits the supplier, and drives payment. 🔶 It also posts **actual spend** against the project's WBS budget for **service (non-stock) items**, and carries compliance fields (GSTR claim status, TDS challan) maintained by the accounts processes.

### 12.2 Prerequisites

- Submitted Purchase Order / Purchase Receipt (mandatory if configured in Buying Settings).
- Supplier bill number and date. 🔶 `Bill Date` is **mandatory**.
- 🔶 A **Zoho Ticket No** (mandatory) — the reference of the invoice-processing ticket.
- 🔶 WBS codes on service lines for budget-controlled projects.

### 12.3 Creating a Purchase Invoice — step by step

1. Preferred: **Purchase Receipt → Create → Purchase Invoice** (or from the PO). Items, rates, taxes, 🔶 WBS codes and PO Type carry over; the exchange rate refreshes to the invoice date if globally enabled.
2. Enter `Supplier Invoice No` (`Bill No`) and `Bill Date` from the supplier's bill; 🔶 fill `Zoho Ticket No`.
3. 🔶 Verify `PO Type` (fetched from the PO, mandatory) and the mandatory dimensions (`Business Unit`, `Profit Center`, `Sub Department`, …).
4. Verify posting date, due date, items, quantities, rates, and taxes against the bill.
5. Save → numbered **PINV-MM-YY-#####** → submit (through the AP approval workflow where active). Accounting entries post; 🔶 service-item amounts post as actuals to the WBS budget.

📘 **Note:** The read-only fields `GSTR Claimed` (+ date/month/year) and `TDS Challan Number` are updated by the GST-claim and TDS processes — do not attempt to fill them manually.

*[Screenshot placeholder: Purchase Invoice form]*

### 12.4 Status flow

Draft → (AP approval workflow) → Submitted (Unpaid / Partly Paid / Paid / Overdue) → Cancelled / Debit Note. The AP workflow defined for Purchase Invoices routes Draft → **Sent for AP Manager's Approval** → **Approved** (AP Manager can also **Return** the bill to draft).

### 12.5 Validations and business rules

**Standard:** supplier ledger and tax validation; over-billing allowance vs PO/PR; duplicate bill-number warning; GST validations (India Compliance).

**🔶 Utthunga:**

- WBS Code mandatory on lines of budget-controlled projects; budget check compares this PI's **service-item** amounts plus recorded actuals against the WBS allocation — over-budget bills are blocked with **"Budget Limit Exceeded"**.
- On submit, actuals post to the Budget Tracking (stock items excluded — their consumption is tracked through stock movements); on cancel, actuals reverse.
- `Zoho Ticket No`, `Bill Date`, `PO Type`, and the dimension fields are mandatory.

---

## 13. Subcontracting

### 13.1 Purpose

Subcontracting sends your raw material to a vendor who processes it and returns finished goods, billing a service charge:

```
Purchase Order (Is Subcontracted) → Subcontracting Order → transfer raw material
      → Subcontracting Receipt (finished goods in, raw material consumed)
      → Purchase Invoice (service charges)
```

### 13.2 Standard behaviour

- A PO with subcontracted items generates a **Subcontracting Order (SCO)** listing the finished items and the raw materials to supply.
- Raw material transfers to the supplier's warehouse; the **Subcontracting Receipt (SCR)** records finished goods received and raw materials consumed.
- Service charges are billed via Purchase Invoice against the PO.

### 13.3 🔶 Utthunga customizations

- **WBS Code on every table:** SCO items and supplied (raw-material) items, SCR items and supplied items all carry a `WBS Code` column filtered by project + item (raw-material lines filter by the raw-material item code).
- **Mandatory dimensions:** `Project`, `Business Unit`, `Cost Center`, `Profit Center`, `Department`, `Sub Department` are mandatory on both SCO and SCR.
- **Budget effect:** raw-material consumption recorded on the SCR (supplied items consumed qty/amount) counts as actual spend against the WBS budget. ⚠️ It is **not** posted at SCR submission — it is picked up when **Recalculate Actuals** runs on the project's Budget Tracking. Budget figures can lag until then.
- Subcontracted PO lines are exempt from the "Material Request mandatory" rule (Chapter 9.9).
---

## 14. WBS-Based Project Budget Tracking (Utthunga Feature)

> 🔶 This entire chapter describes an Utthunga-specific control layer that does not exist in standard ERPNext. It extends the standard account/cost-centre "Budget" with a project **Work Breakdown Structure (WBS)** budget.

### 14.1 Concept

Every budget-controlled project has a **Budget Tracking** document — a tree of budget lines ("budget heads") derived from the project's Delivery Handover financial milestones. Every leaf line gets a dotted **WBS code** (e.g. `2.1.3`) with a budgeted **quantity** and **amount**.

Buying and consumption documents reference these WBS codes line by line:

| Document | Role in budget | When it affects the budget |
|---|---|---|
| Material Request | Commitment check | Validated against other **submitted MRs**; never posts actuals |
| Purchase Order | Commitment check | Validated against other **submitted POs**; never posts actuals |
| Purchase Invoice | **Actuals** (service items only) | Posts on submit, reverses on cancel |
| Stock Entry (Material Issue / Manufacture) | **Actuals** (material consumption) | Posts on submit, reverses on cancel |
| Journal Entry (credit lines) | **Actuals** (direct bookings) | Posts on submit, reverses on cancel |
| Subcontracting Receipt (supplied items) | **Actuals** (raw material consumed) | Only when *Recalculate Actuals* runs |

📘 **Why two layers?** MRs and POs measure what you have *promised to spend* (commitment); invoices, stock issues and journal entries measure what you have *actually spent*. Both are validated against the same WBS allocation, each against its own baseline.

### 14.2 When is the budget check active?

Budget control is switched on **per department**: the project's `Sub Department` must have the **Budget Mandatory** checkbox enabled. For projects whose sub-department is not flagged, WBS codes are optional and no budget validation happens.

📘 **Note:** This is why two users can see different behaviour — one project blocks POs without WBS codes, another does not. Check the project's sub-department configuration.

### 14.3 Budget Tracking document (structure)

- Numbered `EPCC-BT-#####`; created per project from its **Delivery Handover**; submittable.
- `Budget Level`: **Project** (the master budget) or **Month** (a monthly slice, used when `Enable Monthly Budget` is ticked).
- The **Budget Details** table is a tree: top-level rows must be **BBU** or **BOQ** heads; under them **Master Head**, **Service Item**, **Stock Item**, or **Account** rows. Heads are added with the **Add Head** action on a parent row (*"Add Head under {parent}"* dialog: Budget Type + Budget Head).
- WBS codes are assigned automatically: root rows take the financial-milestone serial number; children get `parent.n` (e.g. `3.2.1`). You never type WBS codes manually.
- Each leaf row: `Qty`, `Rate`, `Amount` (budget) and `Actual Qty`, `Actual Amount` (consumption to date). Parent rows roll up their children.
- Budget heads must exist in the Delivery Handover's Financial Milestone Items, and each head's amount cannot exceed the corresponding milestone amount. The sum of leaf heads cannot exceed the overall `Budget Amount`.

*[Screenshot placeholder: Budget Tracking form with budget details tree]*

### 14.4 Monthly budgets

If `Enable Monthly Budget` is ticked on the Project budget:

- A **Month Budget** must exist (and be submitted) for the month of every transaction date; otherwise the transaction is rejected with *"No Budget Tracking was found for Project … set up a Project Budget (and, if Enable Monthly Budget is checked on it, a submitted Month Budget for this transaction's date) before using WBS Codes."*
- Month budgets are created from the Project budget (**Create Monthly Budget**) — they copy the head structure; you fill the month's Qty/Rate/Amount. ⚠️ Heads cannot be added or removed in a Month Budget — only Qty, Rate and Amount can change.
- The sum of Month budgets cannot exceed the Project budget per WBS line, and the Project budget cannot be reduced below what its months already commit.
- Actuals recorded on Month budgets roll up automatically to the Project budget.

### 14.5 Amending a submitted budget

Submitted Budget Trackings are changed through a **Budget Tracking Amendment** — it copies the current rows, lets you edit them, and applies the changes back on submit while **preserving actuals** and progress fields. For Month budgets, only Qty/Rate/Amount may change.

### 14.6 What users see on buying documents

- The **WBS Code** dropdown on each item line offers only codes valid for the document's project *and* that item. An empty dropdown means the budget has no line for that item, or the Budget Tracking is missing/not submitted.
- Over-budget documents are blocked with the **Budget Limit Exceeded** table showing Row #, WBS Code, Budget Head, Type (Amount/Qty), Allocated, Current Commitment, Transaction, and Exceeded By.
- Cancelling a document reverses its budget effect.

### 14.7 Budget alerts (automatic e-mails)

- **75% threshold:** when a Month budget's actuals reach 75%, the Project Cost Controller, Project Manager, and MD receive *"Budget Alert: {project} reached {percent}% of monthly budget."*
- **ABG/PBG heads:** Month budgets containing ABG/PBG (bank-guarantee) heads notify the Finance Controller, PM, and MD on submission.
- **MR reminder:** 7 days before each month starts, Project Managers of projects with a next-month budget receive *"Reminder: Raise Material Request for {project} for {month}."*

### 14.8 Recalculate Actuals

**Budget Tracking → Recalculate Actuals** (submitted budgets only) recomputes actual consumption from all submitted transactions in the period — run it if figures look stale. It is also the only path that picks up **Subcontracting Receipt** consumption.

---

## 15. Approval Workflows

> 🔶 Approval workflows are an Utthunga configuration on top of the standard Frappe workflow engine. States and transitions below reflect the workflow definitions on the reviewed environment; administrators may adjust them over time. The Supplier Quotation, Purchase Order Amendment and Supplier workflows are currently **active**; the Material Request, Purchase Order and Purchase Invoice definitions exist and are enabled per environment.

### 15.1 How workflows behave (all buying documents)

- While a document is in a workflow, the normal *Submit* button is replaced by **action buttons** (e.g. *Send for Purchase Manager's Approval*, *Approve*, *Reject*). Only the role allowed for the current transition sees the action.
- Each state defines who may still **edit** the document. Reaching the final approved state submits the document (docstatus 1).
- 🔶 **Project-based approver resolution:** where enabled, the action is restricted to the *specific user* named for that role on the document's Project (Chapter 4.2).
- 🔶 **Approval e-mails:** when a document enters a state, the users who can act receive an e-mail listing the actions available to them (sent once per state).
- 🔶 **Reminders:** pending approvals are re-notified after the workflow's configured number of **working hours** (Mon–Fri, 09:00–18:00), then every 2 working hours, with the approver's supervisor and the Projects Manager in copy.
- 🔶 **Workflow Log:** every state change is logged ("Workflow state changed to {state} by {user} on {time}") for audit.

### 15.2 Material Request workflow

Two routing branches by profit centre:

**Services branch ("Utthunga - Services"):**

```
Draft ──Save──▶ Sent for Engineer's Approval ──Approve──▶ Sent for Project Manager's Approval
      ──Approve──▶ Sent for Business Head's Approval ──Approve──▶ Sent for Purchase Manager's Approval
      ──Approve──▶ Sent for Deputy Purchase Head ──┬─ total < ₹1,00,000 ──Approve──▶ APPROVED
                                                   └─ total ≥ ₹1,00,000 ──▶ Sent to Purchase Head ──Approve──▶ APPROVED
```

Each approver can **Reject** (back to Draft; the Purchase Manager's rejection marks the document *Rejected*, from where **Review** reopens it). **Approved** = submitted; the Store Manager handles approved MRs; **Cancel** is available to the System Manager.

**Manufacturing branch ("EOU"):** routing depends on the request type — *Material Issue*: Production Engineer → Production Head approves. *Purchase*: Manufacturing User → Production Head → Purchase Executive → Plant Head approves. *Manufacture*: Sales Manager → PPC Head → Manufacturing Head approves.

### 15.3 Supplier Quotation workflow (active)

See Chapter 8.7 — Purchase User → Purchase Manager → Project Cost Controller → Finance Controller (≤ ₹1,00,000 approves; above that the MD approves).

### 15.4 Purchase Order workflow

See Chapter 9.8 — services branch through Purchase Manager → Finance Controller → Deputy Purchase Head (→ MD when the value is high or the amount increased vs the MR); manufacturing branch through the Manufacturing Head with MD approval.

### 15.5 Purchase Order Amendment workflow (active)

See Chapter 10.3 — pure short-closes submit directly; value changes route Purchase Manager → Finance Controller → Deputy Purchase Head (→ MD when total ≥ ₹1,00,000).

### 15.6 Purchase Invoice workflow

Draft (AP Executive) → **Sent for AP Manager's Approval** → **Approve** = submitted; the AP Manager may **Return** the bill to draft or **Cancel** an approved bill.

### 15.7 Supplier workflow (active)

See Chapter 5.1 — AR Executive → AP Manager → Finance Controller; approved suppliers become read-only; temporary vendors bypass approval.

*[Screenshot placeholder: workflow action buttons on a Purchase Order]*

---

## 16. Reports

### 16.1 Standard ERPNext buying reports

Available under **Buying → Reports** (behaviour unchanged):

| Report | Use |
|---|---|
| Purchase Analytics | Trends by supplier/item/group over time |
| Purchase Order Analysis | Ordered vs received vs billed amounts |
| Requested Items to Order and Receive | Open MR pipeline |
| Purchase Order Trends / Purchase Receipt Trends | Period comparisons |
| Items to Order and Receive / Item-wise Purchase History | Item views |
| Supplier Ledger Summary / Accounts Payable | Financial views |

### 16.2 🔶 Utthunga custom reports

**Projects Master Budget** *(Budget Tracking)* — the approved Project-level budget as an indented WBS tree: WBS Code, Budget Head, Unit Rate, UOM, Planned Qty, Budgeted Amount, Actual Amount. Filters: Project, Delivery Handover.

**EPCC Budget vs Actual** *(Budget Tracking)* — planned vs actual per WBS/budget head across months for EPC projects, with an optional weekly drill-down. Filters: Project (mandatory, EPC category), Month, Year, Weekly Budget. Per selected month: Planned Qty, Amount, Actual Amount columns (plus Week-N actuals in weekly mode).

**Monthly Budget Variance** *(Budget Tracking; roles: System Manager, Accounts Manager, Projects Manager)* — month-by-month variance per budget head: Planned vs Actual Amount and Qty, variance and variance % (negative variances shown red, positive green), with a planned-vs-actual bar chart. Filters: Project (mandatory, monthly budgeting must be enabled), Month, Year, Budget Head, Head Type.

**Advance Payment Purchase** *(Purchase Order)* — tracks advance instalments on POs: PO No, Supplier Name, PO Date, Due Date, Advance Amount, Payment Voucher No, Payment Entry Date, Paid Amount, and derived **Paid/Unpaid** status. Filters: Supplier, From/To Date, Status.

*[Screenshot placeholder: Monthly Budget Variance report with chart]*

---

## 17. Print Formats

| Print format | Document | Notes |
|---|---|---|
| **Purchase Order** 🔶 | Purchase Order | Company PO layout, used for supplier communication and as the default attachment for the automatic PO e-mail (configurable in Buying Settings) |
| **Purchase Order - Guidant** 🔶 | Purchase Order | Alternate customer-specific PO layout |
| **Supplier Comparison sheet** 🔶 | Supplier Quotation | Not a regular print format — generated by the **Print → Print Supplier Comparison** button as an A3 landscape PDF: items × suppliers matrix, revision columns, last-3-purchase history, and per-supplier final totals (subtotal − discount + P&F + freight) |
| Standard formats | All buying documents | Standard ERPNext print formats remain available |

🔶 On both **Purchase Order and Purchase Invoice prints**, the `Due Date` is shown and the payment schedule table is hidden by default.

💡 **Tip:** Choose the print format from the print view's sidebar. Administrators set the default per doctype and the auto-e-mail format in Buying Settings.

---

## 18. Notifications and Automation

All alerts below are 🔶 Utthunga automation (standard ERPNext e-mail alerts remain available in addition).

| # | Trigger | Recipients | Message |
|---|---|---|---|
| 1 | MR saved | Project's PM, BU Head, Engineers | ToDo + document share: "Material Request {name} Has Assigned To You" |
| 2 | MR (type Purchase) submitted with stock shortfall | Project's Purchase Manager | "Material Request {name}: Insufficient Stock for Items" |
| 3 | PO finally approved/submitted (switch in Buying Settings) | Supplier (To), project stakeholders (CC) | PO e-mail with PDF attachment (configured print format & template) |
| 4 | PO submitted with `Is Advance` payment rows | All Accounts Users | "Advance Payment Required - Purchase Order {name}" |
| 5 | Document enters a workflow approval state | Users who can act (project-resolved) | "{doctype}: {name} - Approval Required" with available actions |
| 6 | Approval pending past the reminder interval | Approver (+ supervisor, Projects Manager in CC) | Working-hours reminder (Mon–Fri 09:00–18:00; then every 2 working hours) |
| 7 | Quality Inspection overdue (past the item's GR Processing Time) | Purchase Order owner | Daily: "Reminder: Quality Inspection pending for {item} (PR: {name})" |
| 8 | Month budget actuals reach 75% | Project Cost Controller, PM, MD | "Budget Alert: {project} reached {percent}% of monthly budget" |
| 9 | Month budget with ABG/PBG heads submitted | Finance Controller, PM, MD | "Budget Tracking {name}: {heads} configured for Project {project}" |
| 10 | 7 days before month start (projects with a next-month budget) | Project Manager | "Reminder: Raise Material Request for {project} for {month}" |

Other automation:

- **Quantity rounding** — MR/PO quantities round up to the item's `Rounding Value`.
- **Price list update** — PO lines flagged `Update Price in Price List` update the buying Item Price on submission.
- **Exchange-rate refresh** — documents created from a PO/PR/PI recalculate the exchange rate to their own transaction date (global switch in Buying Settings).
- **QI stock movement** — Quality Inspection submission auto-creates the Material Transfer Stock Entry (Chapter 11.4).
- **Dimension carry-forward** — SQ dimensions auto-fill from the MR; QI stock entries copy dimensions from the PR; PO Type flows MR → PO → PR → PI.
- **GSTR/TDS flags on PI** — maintained automatically by the GST-claim and TDS processes.
---

## 19. Common Business Scenarios

### Scenario 1 — Project material purchase (full cycle with budget)

*A project engineer needs 50 flow sensors for project PRJ-0042 in September.*

1. Engineer opens a **Material Request**, selects project PRJ-0042, PO Type *Material & Services - Domestic*, adds the item with `WBS Code` 2.1.4 (Instrumentation), qty 50, required-by date; saves and starts the approval flow (Engineer → PM → BU Head → Purchase Manager → Deputy Purchase Head).
2. The system validates WBS 2.1.4 headroom, rounds the quantity to the item's rounding value, and — since warehouse stock is short — e-mails the Purchase Manager automatically on submission.
3. The purchase user creates an **RFQ** from the MR to three suppliers.
4. Each reply is recorded as a **Supplier Quotation**. After negotiation the buyer revises supplier B's rate with **Revise Rate** (history preserved), fills freight/discount in the **Supplier Comparison** tab, prints the A3 comparison sheet, and routes the winning SQ through approval (PM → PCC → FC; ≥ ₹1 lakh goes to the MD).
5. The approved SQ becomes a **Purchase Order**. Validation checks the budget commitment and MR traceability; approval routes by value; on final approval the PO PDF is e-mailed to the supplier automatically.
6. Goods arrive → **Purchase Receipt**; the sensor is flagged *Inward Inspection Required*, so it is received into the QI warehouse, inspected, and the accepted quantity moves to stores automatically on QI submission.
7. Supplier bill → **Purchase Invoice** (with Zoho ticket no and bill date); service portions post actuals to the budget; payment follows.

### Scenario 2 — Requirement reduced after partial ordering (short-close the MR)

*An MR asked for 100 units; 60 were ordered; the remaining 40 are no longer needed.*

1. Open the submitted MR — the **Update Item(s)** button is visible because qty ≠ ordered qty.
2. In the dialog, type 60 in the Quantity column (or click **Set all to Completed Qty**).
3. Click **Update Quantity**. The MR becomes **Ordered** and the open demand is released.

### Scenario 3 — Changing or short-closing a submitted PO

*After the PO went out, the supplier can only deliver 80 of 100 units.*

1. Open the submitted PO → **Update Item(s)**.
2. In the Update Items dialog, reduce the quantity (or tick **Short Close** for lines to drop entirely; tick **Short Close Purchase Order** to close the whole order).
3. Click **Create Amendment**. A **Purchase Order Amendment** is created: pure short-closes submit directly; quantity/rate changes route through approval (PM → FC → Deputy Purchase Head → MD if ≥ ₹1 lakh).
4. On approval the change applies to the PO; the amendment history stays linked on the PO dashboard.

### Scenario 4 — PO blocked by budget

*Submitting a ₹8,00,000 PO on WBS 3.2 fails with "Budget Limit Exceeded".*

1. Read the error table: Allocated, Current Commitment (other submitted POs), this Transaction, Exceeded By.
2. Options, in order of preference:
   - Reduce quantity/rate or split the purchase across periods;
   - Short-close an older PO on the same WBS via a PO Amendment (its commitment counts);
   - Ask the project manager to raise the budget line via a **Budget Tracking Amendment**;
   - If monthly budgeting is on, check the transaction date falls in a month whose Month Budget has room.
3. Re-submit after one of the above.

### Scenario 5 — Receiving with quality inspection and a rejection

*100 machined parts arrive; 8 fail inspection.*

1. **Purchase Receipt**: the part is flagged *Inward Inspection Required*, so its line is routed to the **Quality Inspection warehouse**. Submit the PR.
2. Click **Create → Quality Inspection(s)** — one QI is created for the line (sample size 100).
3. The inspector records readings, sets Accepted 92 / Rejected 8, picks the accepted and rejected warehouses (+ locations), and submits.
4. The system creates the Material Transfer automatically: 92 to stores, 8 to the rejected warehouse, and updates the PR line's accepted/rejected counters.
5. For the 8 rejects, **Create Repair and Rework** starts the rework cycle; the supplier return/debit note is handled per policy.

### Scenario 6 — Onboarding a new supplier urgently

1. Create the Supplier with `Temporary Vendor = Yes` — no approval needed; purchases can proceed.
2. After the engagement proves out, click **Convert to Regular Vendor**; the supplier is renumbered and then routed through the AP Manager → Finance Controller approval; once **Approved**, the record locks.

### Scenario 7 — Supplier confirms a PO

1. Supplier replies to the automatic PO e-mail confirming the order.
2. The purchase user opens the submitted PO, ticks `Vendor Acknowledged`, and fills the now-mandatory `Date`. No amendment needed — both fields are editable after submission.

---

## 20. Error Messages and Troubleshooting

### 20.1 Budget and WBS errors

| Message | Cause | What to do |
|---|---|---|
| *"Row #n: WBS Code is mandatory — Project X's Sub Department requires Budget Tracking."* | Budget-controlled project, line without WBS code | Pick a WBS code on every line; if the dropdown is empty, the budget has no line for that item — contact the project manager |
| *"All rows with a WBS Code must belong to the same Project. Found: …"* | Lines reference different projects | Split into one document per project |
| *"No Budget Tracking was found for Project … before using WBS Codes."* | No submitted Budget Tracking; or monthly budgeting is on with no submitted Month Budget for the transaction date | Create/submit the Project or Month budget, or correct the transaction date |
| *"WBS Code(s) … were not found in Budget Tracking …"* | The WBS code no longer exists in the budget (restructured) | Re-select valid WBS codes |
| **"Budget Limit Exceeded"** (table) | Commitment/actuals would exceed the allocation on the listed WBS lines | See Scenario 4 |
| *"Row #n: Qty is mandatory — WBS Code … belongs to a … Budget Head."* (Journal Entry) | JE line with an item-type WBS but no quantity | Enter the quantity |

### 20.2 Material Request and ordering errors

| Message | Cause | What to do |
|---|---|---|
| *"Quantity cannot be less than Completed Quantity (n)."* | Update Item(s): qty set below what is already ordered | Enter at least the completed quantity |
| *"Quantity cannot be increased (current quantity: n)."* | Update Item(s): tried to raise a quantity | Create a new MR for additional demand |
| *"Row n: Material Request is mandatory for item …"* (Missing Material Request) | PO line without an MR reference | Create the PO from the Material Request (only subcontracted lines are exempt) |
| *"This document would exceed limit by Stock Qty n for item … against the linked Material Request Item."* (Limit Crossed) | PO orders more than the MR requested beyond the allowance | Reduce the PO qty, or a user with the over-delivery role submits, or raise the allowance in Stock Settings |
| *"Row #n: The item … does not match the selected PO Type …"* | Item type conflicts with the PO Type (e.g. asset item on a material PO) | Correct the PO Type or remove the item |
| *"For PO Type …, Purchase Order Currency must be / cannot be the Company Default Currency …"* | Domestic PO in foreign currency, or import PO in INR | Align the currency with the PO Type |
| *"…exceeds the maximum stock level (n)"* (warning) | Projected stock above the item's maximum for the warehouse | Review the quantity — the warning does not block |

### 20.3 RFQ / Supplier Quotation errors

| Message | Cause | What to do |
|---|---|---|
| *"Material Request is mandatory for all Request for Quotation items. Missing in row(s): …"* | RFQ lines added without an MR reference | Always create RFQs from the Material Request |
| *"All items in Request for Quotation must be from the same Material Request…"* | Items pulled from more than one MR | One RFQ per Material Request |
| *"Request for Quotation is mandatory for all Supplier Quotation items…"* | SQ lines not linked to an RFQ | Create the SQ from the RFQ |
| *"All items in Supplier Quotation must be from the same Request for Quotation…"* | Items from multiple RFQs | One SQ per RFQ per supplier |
| *"Rate cannot be zero for item …"* | SQ line with rate 0 | Enter the quoted rate or remove the line |

### 20.4 Receiving / Quality Inspection errors

| Message | Cause | What to do |
|---|---|---|
| *"Row #n: Please enable Inward Inspection Required for Item …, or select a warehouse other than the Quality Inspection Warehouse."* | A non-inspection item was pointed at the QI warehouse | Correct the line's warehouse (or fix the Item master flag) |
| **"Missing/Invalid Quality Inspection Warehouse"** | Header QI warehouse blank, or different from the inspection lines' warehouse | Set the QI warehouse; let the system route line warehouses |
| **"Missing/Invalid Expiry Date"** | Expiry-tracked item without an expiry date, or a date before posting | Fill a future expiry date per line |
| *"Row #n: Target Warehouse Location is required for Item …"* | Location-tracked warehouse without a location | Pick the warehouse location |
| *"Total Inspected Quantity (Accepted + Rejected) must equal Sample Size for Item …"* | QI quantities don't add up | Adjust accepted/rejected so they sum to the sample size |
| *"Sample Size (n) must match Received Quantity (n) for Item …"* | Sample size edited away from the received qty | Restore the received quantity |
| *"Source and Accepted/Rejected Warehouses cannot be same."* | Same warehouse selected twice on the QI | Use distinct source/accepted/rejected warehouses |
| *"Quality Inspection already exists for Purchase Receipt Item …"* | Second QI attempted on the same PR line | Open the existing QI instead |

### 20.5 Supplier errors

| Message | Cause | What to do |
|---|---|---|
| *"For Domestic vendor type, Default Currency must be INR"* / *"For International vendor type, Default Currency cannot be INR"* | Vendor type vs currency mismatch | Align currency with vendor type |
| *"Temporary Vendor status cannot be changed after the Supplier is saved"* | Tried to flip the temporary flag directly | Use **Convert to Regular Vendor** on temporary vendors |

### 20.6 General troubleshooting

| Symptom | Likely cause | Check |
|---|---|---|
| WBS Code dropdown is empty | Missing/wrong project; item not in the budget; budget not submitted | Header `Project`; Budget Tracking details |
| Supplier did not receive the PO e-mail | Auto-e-mail off; supplier has no e-mail; missing template/print format | Buying Settings → Vendor Email Configuration; supplier's primary contact |
| Budget actuals look stale | Subcontracting or backdated entries not recalculated | Run **Recalculate Actuals** on the Budget Tracking |
| Two projects behave differently on WBS enforcement | `Budget Mandatory` differs on their sub-departments | Department master |
| Workflow buttons missing | Your role has no transition from the current state, or project-based validation restricts the action to a named user | Chapter 15; the project's stakeholder assignments |
| Cannot edit an approved Supplier | Approved suppliers are read-only | Finance Controller: **Send Back to Draft** |
| Wrong exchange rate on a PR/PI created from a PO | Global transaction-date exchange-rate switch | Buying Settings |

---

## 21. Frequently Asked Questions

**Q1. Do I always need a Material Request before a Purchase Order?**
Yes, for everything except subcontracted lines — PO validation rejects lines without an MR reference. RFQs and Supplier Quotations equally require their source documents.

**Q2. Why does my PO submit fine on one project but get blocked for WBS codes on another?**
Budget enforcement is per sub-department (`Budget Mandatory` flag). Only projects under flagged sub-departments are controlled.

**Q3. Does a Material Request or Purchase Order consume budget?**
They *commit* budget (each is checked against other submitted documents of the same type) but never post actuals. Actual spend is recorded by Purchase Invoices (service items), Stock Entries, and Journal Entries.

**Q4. What happens to the budget when I cancel a PO or PI?**
The commitment (PO) or actuals (PI) reverse automatically on cancellation.

**Q5. How do I change a submitted PO?**
Through a **Purchase Order Amendment** (PO → Update Item(s) → Create Amendment). Pure short-closes submit directly; value changes go through approval.

**Q6. Can I change quantities on a submitted Material Request?**
You can only *reduce* toward the ordered quantity using **Update Item(s)**. Increases need a new MR.

**Q7. Where do I compare supplier offers?**
Any Supplier Quotation of the RFQ → **Supplier Comparison** tab; **Print → Print Supplier Comparison** for the approval sheet.

**Q8. Why does clicking a rate on a draft Supplier Quotation open a "Revise Rate" prompt?**
Rate changes are captured as revisions so the negotiation history is preserved and shown in the comparison (R1, R2, …).

**Q9. Who receives the automatic PO e-mail?**
The supplier (To) and the project's stakeholders in CC: BU Head, MD, Engineer, Finance Controller, Purchase Manager, Project Manager, Purchase User, Project Cost Controller — as named on the Project.

**Q10. Why is my received item sitting in the Quality Inspection warehouse?**
It is flagged *Inward Inspection Required*. Create/submit the Quality Inspection; on submission the accepted stock moves to the accepted warehouse automatically.

**Q11. Budget figures look outdated — why?**
Subcontracting consumption (and backdated corrections) enter the budget only when **Recalculate Actuals** runs on the Budget Tracking.

**Q12. Why can't I edit an approved supplier?**
Approved supplier records are locked. The Finance Controller can send the record back to Draft for changes.

**Q13. What does PO Type change?**
It classifies the purchase (Material & Services / Fixed Asset, Domestic / Import), validates the currency, prompts reverse-charge for imports, restricts allowable items, and flows forward to the PR and PI.

---

## 22. Best Practices

1. **Always start from the previous document** (MR → RFQ → SQ → PO → PR → PI). The chain is enforced by validation, and it carries WBS codes, PO Type, dimensions, and references forward.
2. **One project per document.** Mixed-project documents are rejected by the budget engine.
3. **Pick the WBS code and PO Type at the Material Request stage.** Everything downstream inherits them; fixing them later means touching every document in the chain.
4. **Use Update Item(s) to short-close MRs** and **PO Amendments to change POs** — never cancel-and-recreate; the audit trail and budget commitments stay clean.
5. **Record negotiations with Revise Rate,** not by overwriting rates — approvers rely on the revision trail in the comparison sheet.
6. **Complete the Supplier Comparison commercial terms (freight, discount, P&F) before seeking approval** — landed totals often change the ranking.
7. **Submit Month Budgets before the month begins** when monthly budgeting is on; otherwise all buying for that month is blocked.
8. **Watch the 75% budget alerts** — act before the line is exhausted rather than when a PO is blocked.
9. **Maintain Item quality flags correctly** (`Inward Inspection Required`, `GR Processing Time`, expiry) — they drive the entire receiving flow.
10. **Complete Quality Inspections within the GR processing time** — accepted stock is unusable (sitting in the QI warehouse) until the QI is submitted.
11. **Tick Vendor Acknowledged promptly** so open-order chasing reflects real supplier commitments.
12. **Keep supplier masters clean** — one record per supplier, correct vendor type/MSME classification, and a valid primary e-mail so auto-e-mails and price history work. Convert temporary vendors to regular ones as soon as the relationship continues.
13. **Don't bypass over-order warnings casually.** The bypass role exists for genuine tolerance cases.
14. **Run Recalculate Actuals after heavy subcontracting activity** or at period-end, before reviewing budget reports.

---

## Appendix A — Custom Field Reference

Fields added by Utthunga apps, by document. System-internal fields (hidden flags, layout breaks) are omitted.

### Material Request

| Field | Type | Notes |
|---|---|---|
| PO Type | Select | Purchase requests; 4 options; flows to PO |
| Project / Project Name | Link / Data | Project mandatory; name auto-fetched |
| Business Unit, Cost Center, Department, Profit Center, Sub Department | Link | Accounting dimensions |
| Currency, Total | Link, Currency | Document currency; auto-computed total |
| Items: WBS Code | Autocomplete | Budget line, filtered by project + item |
| Items: Vendor, Target Price | Link, Float | Sourcing references |
| Items: Start/End Date | Date | Fetched from Item |
| Items: Total Warehouse Qty | Float | Auto stock across raw-material warehouses |
| Items: Department | Link | Line dimension |

### Request for Quotation

| Field | Type | Notes |
|---|---|---|
| Material Request ID | Data (read-only) | Source MR, set automatically |
| Items: WBS Code | Autocomplete | Carried from MR |

### Supplier Quotation

| Field | Type | Notes |
|---|---|---|
| PO Type | Select | Mandatory |
| RFQ ID / Material Request ID | Data (read-only) | Source references |
| Project, Business Unit, Cost Center, Profit Center, Sub Department, Department | Link | Mandatory dimensions, auto-fetched from MR |
| Remarks | Small Text | Near totals |
| Supplier Comparison tab | HTML/JSON | Comparison grid + terms (Discount, P&F, Freight, Payment Terms, Delivery, Guarantee, Deputation, Contact Person, Technical Clearance, Position, Approved Vendor, Remarks) |
| Items: WBS Code | Autocomplete | Budget line |
| Items: Revise Rate (button), Initial Rate, Revised Rates | Button / Currency / Log | Rate revision history |

### Purchase Order

| Field | Type | Notes |
|---|---|---|
| PO Type | Select | Mandatory; currency/item validation |
| Department, Business Unit, Profit Center, Sub Department | Link | Mandatory dimensions |
| Version, Delivery Schedule, Acceptance Criteria, IEC No, Reference, MRF No, Infra's PO Number, Mode of Delivery, Warranty, Taxes, Contact Person, Terms of Payment, Mode of Payment | Various | Commercial print fields |
| Vendor Acknowledged / Date | Check / Date | Editable after submit; date mandatory when acknowledged |
| Items: WBS Code | Autocomplete | Budget line |
| Items: Total Warehouse Qty | Float | Auto stock display |
| Items: Update Price in Price List | Check | Updates buying Item Price on submit |
| Items: Department | Link | Line dimension |

### Purchase Order Amendment (custom doctype)

Purchase Order (link), Items (qty/rate/short-close per line), Short Close Purchase Order, Total, PO Type, Project — numbered `POA-FY-#####`.

### Purchase Receipt

| Field | Type | Notes |
|---|---|---|
| Accepted Warehouse Location | Link | For inspection-free goods |
| Quality Inspection Warehouse / Quality Warehouse Location | Link | Receiving location for inspection items |
| PO Type | Data (read-only) | Fetched from PO |
| Department (+ mandatory Project/BU/CC/PC/Sub Dept) | Link | Dimensions |
| Items: WBS Code | Autocomplete | Budget line |
| Items: Expiry Date | Date | Mandatory for expiry-tracked items |
| Items: Inward Inspection Required | Check (read-only) | Fetched from Item |
| Items: QI Accepted/Rejected Qty | Float (read-only) | Updated by Quality Inspections |
| Items: Target Parent Warehouse Location | Link (read-only) | Auto from location |

### Purchase Invoice

| Field | Type | Notes |
|---|---|---|
| Zoho Ticket No | Data | **Mandatory** |
| PO Type | Select (fetched) | **Mandatory** |
| TDS Challan Number | Data (read-only) | Maintained by TDS process |
| GSTR Claimed (+ Date/Month/Year) | Check/Date (read-only) | Maintained by GST-claim process |
| Bill Date | Date | Made mandatory |
| Items: WBS Code | Autocomplete | Budget line (service items post actuals) |
| Items: Department | Link | Line dimension |

### Supplier

| Field | Type | Notes |
|---|---|---|
| Vendor Type | Select | Domestic / International — **mandatory**; drives currency rule |
| Vendor Classification | Select | MSME / Non MSME — mandatory for Domestic |
| MSME Number | Data | Mandatory for MSME |
| Temporary Vendor | Select | Yes/No — **mandatory**, locked after save |
| Account Number / IFSC Code | Data | Bank details |

### Subcontracting Order / Receipt

WBS Code on all item and supplied-item tables; Department mandatory; Project/BU/CC/PC/Sub-Department mandatory.

### Buying Settings

Vendor Email Configuration tab (`Email supplier on PO submit`, `PO Auto Email Print Format`, `PO Email Template`); `Use Transaction Date Exchange Rate Globally`.

### Item (buying-relevant)

Purchasing Group; Rounding Value; Inward Inspection Required; GR Processing Time; Has Expiry Date; Start/End Date; ABC Classification; Procurement Type; part-number fields; Item Reorder → Maximum Stock Level.

---

## Appendix B — Naming Series Reference

| Document | Utthunga series | Example |
|---|---|---|
| Material Request | `MR-.MM.-.YY.-.#####` | MR-07-26-00042 |
| Supplier Quotation | `SUQ-.MM.-.YY.-.#####` | SUQ-07-26-00108 |
| Purchase Order | `PO-.MM.-.YY.-.#####` | PO-07-26-00231 |
| Purchase Order Amendment | `POA-.FY.-.#####` | POA-2026-00017 |
| Purchase Invoice | `PINV-.MM.-.YY.-.#####` | PINV-07-26-00312 |
| Supplier | `SUP-.MM.-.YY.-.#####` (series hidden, auto-assigned) | SUP-07-26-00054 |
| Budget Tracking | `EPCC-BT-.#####` | EPCC-BT-00009 |

📘 Standard ERPNext series remain available as secondary options on some documents; the Utthunga series is the default.

---

*End of manual. For corrections or additions, contact the ERP functional team.*
