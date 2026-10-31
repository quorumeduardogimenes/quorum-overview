---
title: Brands, products and coverage in Q Analytics
summary: Valsoft > Quorum > 4 brands > products structure, with what each product does (public website and Sam), the status of each in Q Analytics according to Rick's diagram, examples of ingested sources and the known infrastructure.
updated_at: 2026-10-30
sources: [follow-up with Rick 2026-10-30 (../meetings/2026-10-30-rick-follow-up.md), Jira DR-2910 (Atlassian connector, 2026-10-30), Rick's diagram, image sent by Eduardo on 2026-10-27 (transcribed here), meeting with Rick 2026-10-26 (../meetings/2026-10-26-rick-kickoff.md), meeting with Todd 2026-10-26 (../meetings/2026-10-26-todd-technical-tour.md), call with Todd 2026-10-27 (../meetings/2026-10-27-todd-ingestion-call.md), conversation with Sam 2026-10-29 (../meetings/2026-10-29-sam-business-rules.md), public website consulted on 2026-10-27 (pages https://quoruminfotech.com/brands/, /dms/, /crm/, /sales-retailing/ and /service-retailing/)]
status: confirmed
---

# Brands, products and coverage in Q Analytics

## Structure (Rick's diagram)

```
Valsoft
└── Quorum
    ├── Quorum DMS
    ├── DealerMine
    ├── Autovance
    └── Accessible Accessories
```

Quorum is a company of the **Valsoft** group and has **four brands**: Quorum DMS, DealerMine, Autovance and Accessible Accessories. Each brand groups products. **PowerLane is a product of the Quorum DMS brand**, not a brand.

(The diagram image was shared in the conversation and is not archived in the project; the transcription below is the reference.)

## Coverage legend in Q Analytics

| Color | Text in the diagram | Reading |
|---|---|---|
| Purple | "Data Flowing in QAnalytics" | Data already flowing in Q Analytics |
| Yellow | "Early Progress into QAnalytics" | Early progress (in the meeting notes: early-stage ETL) |
| Red | "Little or no data in QAnalytics" | Little or no data (in the meeting notes: limited reports) |

## Products: what they do and status in Q Analytics

The "What it does" column comes from the **public website** (consulted on 2026-10-27), not from someone at Quorum; what Sam added is in the next section. The website does not use the same groupings as the diagram; where the website does not name the product, it says "not on the website".

| Brand | Product | Color | What it does (website) |
|---|---|---|---|
| Quorum DMS | Quorum DMS | Purple | The dealership's core system: **service and parts** operations (fixed ops), **sales and leads** (variable ops) and **accounting** |
| Quorum DMS | PowerLane | Purple | Modernize the "service lane" (the workshop's reception area): mobile check-in/check-out, digital inspections with media and real-time communication. The **Declined Services** feature (declined services, to be re-quoted) is part of it |
| Quorum DMS | Communicator | Purple | The DMS "Communications" module: real-time messages between customer and staff, digital marketing and automated campaigns |
| Quorum DMS | Sales Planner | Purple | Not on the website |
| Quorum DMS | Forecast Tool | Red | Not on the website |
| DealerMine | DealerMine Service CRM | Yellow | Service CRM: increase contacts, appointments and service revenue, combining automation and personal contact (includes "AI Marketing", with SMS and voicemail messages) |
| DealerMine | DealerMine Sales CRM | Yellow | Sales CRM: tracks each customer through the funnel, captures leads with automatic follow-ups and offers mobile access |
| DealerMine | MORI | Yellow | Not on the website. Rick (2026-10-30): "Mori" is the **AI brand**; everything AI at Quorum falls under it. Per Jira (DR-2910), Mori AI answers inbound calls to the dealership's call center, and its per-call data is in Q Analytics (`dm.mori_call`) |
| Autovance | Autovance Desk | Yellow | "Desking": structure the deal (rates, residual values, discounts) with mobile desking and clear worksheets for the customer |
| Autovance | Autovance Menu | Yellow | F&I menu (financing and insurance) presented in every sale, for the customer to choose from |
| Autovance | Autovance MyDeal | Yellow | Digital retailing: the customer builds their own deal online, connected to the in-store transaction |
| Accessible Accessories | Accessible Accessories | Red | Sale of accessories (OEM and aftermarket catalog) online and in store; the **Tire Boost** feature (tire quoting in the deal) is part of it |

Summary: 12 products, of which 4 purple (all from the Quorum DMS brand), 6 yellow (DealerMine and Autovance) and 2 red (Forecast Tool and Accessible Accessories).

**Other offerings on the website that do not appear as a product in the diagram:** the DMS **Analytics** is **Q Analytics** (the DMS page cites it by name) and gathers the stores' activity into real-time reports, including for groups of several dealerships; **Online Scheduling** (customer books service from any device) appears under the DealerMine brand; **BDC Services** (outsourced customer relationship center, focused on service revenue) also appears under DealerMine; **VINN Auto**, listed as a brand, is an online marketplace that connects buyers to dealerships and generates leads.

General company description: [company/overview.md](../company/overview.md). Quorum DMS detail: [products/quorum-dms.md](quorum-dms.md).

## What Sam said about the products (2026-10-29)

The view of someone who works with the data, in an informal conversation (see [meetings/2026-10-29-sam-business-rules.md](../meetings/2026-10-29-sam-business-rules.md)):

- The suite has **Quorum DMS, AutoVance (acquired), DealerMine (CRM) and Accessible Accessories**. The products can be **sold separately or integrated**.
- **AutoVance is "DMS agnostic"**: it works with Quorum DMS or with competitors' DMS. It is the deal structuring tool (cash, finance and lease scenarios).
- **DealerMine** integrates with the DMS by sending appointments and customer prospects.
- **Communicator** is the messaging app tied to the DMS: internal staff messages and outbound customer messages, with support for automated messages.
- **Lead Generator** tracks leads and marketing return (for example, radio ad spend vs. vehicles sold); Sam said it **may still be active**. It does not appear in the diagram or on the website.
- **Sales Planner** is "work in progress".
- **PowerLane** was only mentioned, without details.

## Examples of ingested sources (meetings with Todd)

Todd cited, as **examples** of ingestion tool sources: **QDMS, AutoVance, Communicator, DealerMine, Powerlane, Zoho CRM, Zoho Desk and Carbon60** (see [q-analytics/ingestion-pipeline.md](../q-analytics/ingestion-pipeline.md)). This is consistent with the diagram: the purple and yellow products already have some ingestion. QDMS is probably Quorum DMS (inferred). Zoho CRM, Zoho Desk and Carbon60 do not appear in the product diagram. Sales Planner (purple) was not mentioned, but the list is of examples.

Details from the 2026-10-27 call (see [q-analytics/ingestion-agent.md](../q-analytics/ingestion-agent.md)):

- **Zoho (CRM and Desk) is internal:** it is used by Quorum itself, not a data source for customers. Several jobs can run against a single Zoho client.
- **Powerlane** (a Quorum DMS product) is ingested through an **API**: one client, several jobs with different calls.
- **DealerMine:** there is a DealerMine **MSSQL** test database, with exported real customer data, used in the data migration environment. This indicates that DealerMine has a SQL Server base `(inferred)`; the note does not say this directly.
- **Veeva** was mentioned as one more API client with its own configuration; Eduardo decided it is not a priority right now.

## Infrastructure and databases

- **AutoVance and Accessible Accessories** run on **AWS** (Rick, 2026-10-26).
- The databases include **Postgres, MySQL and T-SQL**, with no information on which brand/product uses which (Rick).
- Quorum DMS and DealerMine infrastructure: not provided.
- **Q Analytics** itself runs self-hosted in a colocation in Calgary (Todd); see [q-analytics/architecture.md](../q-analytics/architecture.md).

## Goal

Expand [Q Analytics](../q-analytics/overview.md) to all products, not just Quorum DMS (Rick's explanation to Eduardo, which the diagram illustrates). In practice, it means moving the yellow and red products to purple.

## Gaps and open questions

- Which brand/product uses which database (Postgres, MySQL, T-SQL) and where does each one run?
- Is there a defined priority among the yellow and red products for the expansion?
- **MORI:** partly answered on 2026-10-30 (the AI brand; Mori AI handles calls; see above and [q-analytics/agent-console-report.md](../q-analytics/agent-console-report.md)). Still open: is Mori a product, a brand above products, or both? Rick calls it "the DealerMind AI brand" (Granola; DealerMind is a known transcription error for DealerMine); is it part of DealerMine only or of all brands? And why is it yellow in the diagram?
- **Sales Planner and Forecast Tool** do not appear on the website: are they internal products, newer ones or products with an internal name? What does each one do? Sam says Sales Planner is work in progress, but the diagram shows it in purple (data flowing): is the product still in development while the data already arrives?
- **Lead Generator:** which brand is it from, and is it still active? It does not appear in the diagram.
- Granola says AutoVance was "acquired from Rick". Who is this Rick (the CTO? someone else? a transcription error) and from whom did Quorum acquire AutoVance?
- Is QDMS Quorum DMS? And what is Carbon60 in relation to the products?
- **VINN Auto**, a marketplace listed on the website as a brand, does not appear in the diagram. Is it out of the scope of Q Analytics, part of another group or discontinued? (The vinnauto.com website was not consulted.)
- Are Online Scheduling and BDC Services (website, under DealerMine) part of the "DealerMine Service CRM" product in the diagram?
- When there is architecture detail for another brand, create `products/<brand>.md` ([quorum-dms.md](quorum-dms.md) already exists) and leave only the summary here (see the guide).
