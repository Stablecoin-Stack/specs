# SSF-SPEC-001 — Layer 7: Business Models

| Field | Value |
|---|---|
| **Document ID** | SSF-SPEC-001-L7 |
| **Title** | Business Models for the Stablecoin Stack |
| **Layer** | 7 — Business Models |
| **Version** | 1.0.0 |
| **Status** | Draft |
| **Date** | 2026-03-12 |
| **Parent Specification** | SSF-SPEC-001 v1.0.0 |
| **Author(s)** | Adalton Reis [reis@stablecoinstack.org](mailto:reis@stablecoinstack.org) |
| **Reviewers** | — |
| **Organization** | Stablecoin Stack Foundation |
| **Contact** | [specs@stablecoinstack.org](mailto:spec@stablecoinstack.org) |
| **Public Address** | `0x0000000000000000000000000000000000000000` |
| **License** | Apache License 2.0 |


---

## Table of Contents

1. [Purpose of This Document](#1-purpose-of-this-document)
2. [Context: What the Stablecoin Stack Makes Possible](#2-context-what-the-stablecoin-stack-makes-possible)
   - 2.1 [The Problem Being Solved](#21-the-problem-being-solved)
   - 2.2 [What the Stack Provides Out of the Box](#22-what-the-stack-provides-out-of-the-box)
   - 2.3 [Structural Advantages of the Payment Rails](#23-structural-advantages-of-the-payment-rails)
3. [Roles and Responsibilities in the Commercial Ecosystem](#3-roles-and-responsibilities-in-the-commercial-ecosystem)
   - 3.1 [The Payment Processor](#31-the-payment-processor)
   - 3.2 [The Acquirer](#32-the-acquirer)
   - 3.3 [Combined Roles](#33-combined-roles)
4. [Revenue Primitives](#4-revenue-primitives)
   - 4.1 [Transaction Fees (On-Chain)](#41-transaction-fees-on-chain)
   - 4.2 [Subscription Fees (Fiat)](#42-subscription-fees-fiat)
   - 4.3 [Acquirer Commission](#43-acquirer-commission)
   - 4.4 [Custody and Conversion Spread](#44-custody-and-conversion-spread)
   - 4.5 [Float Income](#45-float-income)
5. [Custody Spectrum: A Critical Commercial Dimension](#5-custody-spectrum-a-critical-commercial-dimension)
   - 5.1 [Non-Custodial Model](#51-non-custodial-model)
   - 5.2 [Custodial Model](#52-custodial-model)
   - 5.3 [Hybrid Model](#53-hybrid-model)
6. [Business Models for Payment Processors](#6-business-models-for-payment-processors)
   - 6.1 [Model PP-1: Transaction-Fee Processor](#61-model-pp-1-transaction-fee-processor)
   - 6.2 [Model PP-2: Subscription-Based Processor](#62-model-pp-2-subscription-based-processor)
   - 6.3 [Model PP-3: Blended Fee Processor](#63-model-pp-3-blended-fee-processor)
   - 6.4 [Model PP-4: Custodial Full-Service Processor (Fiat-Out)](#64-model-pp-4-custodial-full-service-processor-fiat-out)
   - 6.5 [Model PP-5: White-Label Processor](#65-model-pp-5-white-label-processor)
   - 6.6 [Model PP-6: SaaS Infrastructure Provider](#66-model-pp-6-saas-infrastructure-provider)
   - 6.7 [Model PP-7: Embedded Finance Processor](#67-model-pp-7-embedded-finance-processor)
   - 6.8 [Model PP-8: Float-Yield Processor (Zero-Fee)](#68-model-pp-8-float-yield-processor-zero-fee)
   - 6.9 [Model PP-9: Vertical-Specific Processor](#69-model-pp-9-vertical-specific-processor)
7. [Business Models for Acquirers](#7-business-models-for-acquirers)
   - 7.1 [Model AQ-1: Classic Distribution Acquirer](#71-model-aq-1-classic-distribution-acquirer)
   - 7.2 [Model AQ-2: Wallet-as-Acquirer](#72-model-aq-2-wallet-as-acquirer)
   - 7.3 [Model AQ-3: Point-of-Sale Hardware Acquirer](#73-model-aq-3-point-of-sale-hardware-acquirer)
   - 7.4 [Model AQ-4: Regional Agent Network](#74-model-aq-4-regional-agent-network)
   - 7.5 [Model AQ-5: Platform Acquirer (Marketplace or App Store)](#75-model-aq-5-platform-acquirer-marketplace-or-app-store)
   - 7.6 [Model AQ-6: Decentralised Autonomous Organisation (DAO) Acquirer](#76-model-aq-6-decentralised-autonomous-organisation-dao-acquirer)
8. [Combined Models: Processor and Acquirer in Conjunction](#8-combined-models-processor-and-acquirer-in-conjunction)
   - 8.1 [Model C-1: Vertically Integrated Full-Stack Operator](#81-model-c-1-vertically-integrated-full-stack-operator)
   - 8.2 [Model C-2: Cooperative Payment Network](#82-model-c-2-cooperative-payment-network)
   - 8.3 [Model C-3: Franchise Payment Network](#83-model-c-3-franchise-payment-network)
9. [Unorthodox and Emerging Models](#9-unorthodox-and-emerging-models)
   - 9.1 [Model U-1: Merchant-Owned Processor Collective](#91-model-u-1-merchant-owned-processor-collective)
   - 9.2 [Model U-2: Pay-as-You-Grow (Revenue-Share Onboarding)](#92-model-u-2-pay-as-you-grow-revenue-share-onboarding)
   - 9.3 [Model U-3: Refund-as-a-Service](#93-model-u-3-refund-as-a-service)
   - 9.4 [Model U-4: Payment Data Marketplace](#94-model-u-4-payment-data-marketplace)
   - 9.5 [Model U-5: Treasury-as-a-Service Processor](#95-model-u-5-treasury-as-a-service-processor)
   - 9.6 [Model U-6: On-Chain DAO Processor](#96-model-u-6-on-chain-dao-processor)
   - 9.7 [Model U-7: Loyalty and Rewards Infrastructure](#97-model-u-7-loyalty-and-rewards-infrastructure)
   - 9.8 [Model U-8: Programmable Escrow Service](#98-model-u-8-programmable-escrow-service)
10. [Comparative Summary](#10-comparative-summary)
11. [Guidance for New Entrants](#11-guidance-for-new-entrants)
12. [License](#12-license)

---

## 1. Purpose of This Document

This document describes the business layer of the Stablecoin Stack ecosystem. It is not a normative technical specification. It does not define protocols, data structures, or conformance requirements. Its purpose is different, and deliberately so.

The Stablecoin Stack Foundation publishes open-source specifications and reference implementations because we believe that open standards lead to better outcomes for merchants, payers, and the broader payments industry. However, open standards only create value when businesses adopt and build upon them. This document exists to make that path concrete.

Specifically, this document addresses:

- **How companies can generate sustainable revenue** by deploying and operating the Stablecoin Stack as a payment processor.
- **How acquirers can build distribution businesses** on top of an existing Stablecoin Stack deployment.
- **What new business models become possible** with this payment architecture that were not previously viable or did not exist.

The intended reader is a businessperson, founder, or executive evaluating whether to build a commercial venture on the Stablecoin Stack. Technical depth is kept to the minimum necessary to understand why certain commercial structures are available. For full technical detail, refer to SSF-SPEC-001.

---

## 2. Context: What the Stablecoin Stack Makes Possible

### 2.1 The Problem Being Solved

Accepting digital payments today is, for most businesses, deceptively expensive and slow. A merchant accepting a card payment typically waits until the following business day — or longer — to receive funds. The payment processor, the card network, and the acquiring bank each take a cut. The merchant bears the cost of disputes and chargebacks, which require administrative overhead and can be exploited fraudulently. Cross-border payments compound these issues with additional fees and settlement delays measured in days.

Digital stablecoin payments can, in principle, resolve all of these issues: settlement is near-instant, fees are low, and payments are cryptographically authorised by the payer and therefore irreversible by design. However, the practical barriers to adoption have been significant. Merchants cannot be expected to understand the underlying technology. Payers should not need to learn new concepts to make a purchase. Developers building payment services should not need to construct the entire technical stack from scratch.

The Stablecoin Stack addresses all three barriers simultaneously.

### 2.2 What the Stack Provides Out of the Box

A company deploying the Stablecoin Stack receives a complete, production-ready payment infrastructure. This includes:

- A **Settlement Contract** — the on-chain component that verifies payment authorisations and transfers funds atomically. It cannot be manipulated by the processor; its behaviour is determined entirely by its published code and the signatures provided by the payer.
- A **Checkout Engine** — the merchant-facing backend that handles session creation, charge management, reconciliation, and webhook delivery. Merchants interact with it through a standard API; they do not need to understand anything about the underlying payment rails.
- A **Broadcast Layer** — the infrastructure that receives signed payment payloads from payer wallets, validates them, and submits them to the network. It also absorbs the transaction costs that would otherwise fall on the payer, recovering them through the processing fee.
- A **Client Wallet** — the payer-facing interface. The payer opens the wallet, sees the payment request, and confirms. The wallet handles all signature construction and communication with the processor. From the payer's perspective, this is no different from any other digital payment method.
- A **Merchant Dashboard** — a self-service interface for merchants to manage their account, monitor transactions in real time, and review reconciliation reports.
- A **Basic Data Service** — a public directory of supported tokens and registered acquirers, usable by wallets to present choices to payers.

The company deploying this infrastructure — the **Payment Processor** — does not need to design or build any of it. The Foundation has done that work. What the company brings is operational capacity, merchant relationships, customer support, regulatory compliance, and commercial strategy.

This is the same model that has allowed dozens of companies to build payment businesses on top of card network rails, without designing the card network themselves. The difference is that the Stablecoin Stack is open-source and free to deploy.

### 2.3 Structural Advantages of the Payment Rails

Understanding the commercial opportunity requires understanding the structural properties of the underlying payment mechanism. Several of these properties are genuinely new — they were not available on traditional payment rails — and they create commercial possibilities that have no prior analogue.

**Instant, final settlement.** Payment finality is determined by the network on which the Settlement Contract is deployed. Networks such as Polygon achieve finality in approximately five seconds. Others may take longer, but even the slower networks settle in minutes, not days. This means a merchant receives their funds in the same session as the payment — not the next business day. The processor never holds the merchant's money overnight in the ordinary course of operations.

**Irreversibility by design.** A payment authorised by the payer's cryptographic signature cannot be reversed by the processor, the acquirer, or the payer. There is no equivalent of a chargeback initiated by the card issuer. The implications for merchants are significant: a category of fraud that costs the industry billions of dollars annually simply does not exist in this model. For processors, this means no chargeback management overhead, no reserve requirements for dispute exposure, and no liability for fraudulent transactions that slipped through authorisation.

**No custodial requirement on the settlement path.** In the non-custodial configuration of the Stablecoin Stack, funds move directly from the payer's wallet to the merchant's wallet through the Settlement Contract, without passing through any account held by the processor. The processor is a relay, not a custodian. This is structurally different from every traditional payment rail, where the processor and acquiring bank are necessarily custodial participants in the flow of funds.

**Gas abstraction.** The payer does not need to hold any asset other than the stablecoin they wish to spend. The processor's Relayer pays the network transaction cost and recovers it through the processing fee. This makes the payment experience indistinguishable from any other mobile payment method from the payer's perspective.

**Open acquirer model.** The Settlement Contract has a built-in acquirer fee mechanism. Any third party can register as an acquirer, distribute the payment service, and earn a share of the processing fee on every transaction they refer — automatically, on-chain, without any trust relationship with the processor. This enables distribution networks that were previously impossible to operate without significant bilateral coordination.

---

## 3. Roles and Responsibilities in the Commercial Ecosystem

### 3.1 The Payment Processor

The Payment Processor is the company that deploys and operates a conformant instance of the Stablecoin Stack. Its responsibilities include:

- Operating and maintaining the technical infrastructure (Checkout Engine, Broadcast Layer, Relayer, Settlement Contract);
- Onboarding and supporting merchants;
- Funding the Relayer account to cover network transaction costs;
- Setting the base fee and related parameters on the Settlement Contract;
- Ensuring regulatory compliance in the jurisdictions where it operates;
- Managing the conversion between digital assets and fiat currency, if the processor offers such services.

The processor is the primary commercial entity. All revenue models described in this document that involve a processor are available to any company that deploys a conformant stack.

### 3.2 The Acquirer

The Acquirer is a third party registered on the Settlement Contract who distributes the payment service and earns a share of the processing fee on payments they refer. Acquirers do not operate the payment infrastructure. They bring merchants, payers, or distribution channels, and the Settlement Contract automatically credits their earning wallet on every qualifying transaction.

Acquirer registration requires a one-time on-chain registration payment to the processor. Once registered, the acquirer's fee percentage is immutably recorded on-chain — the processor cannot unilaterally change it without the acquirer's consent. This provides commercial certainty that is unusual in distribution agreements.

The acquirer role is flexible. An acquirer could be a software company that builds a point-of-sale application, a bank that distributes the payment method to its clients, a marketplace that embeds payment acceptance, or a community of individuals organised as a cooperative. The on-chain mechanism does not distinguish between these cases.

### 3.3 Combined Roles

A single company may operate as both processor and acquirer simultaneously — for example, a company that deploys its own stack and also registers as an acquirer on that same stack to earn the full combined fee on transactions it originates directly. This is a common configuration for vertically integrated operators, and it is fully supported by the architecture.

---

## 4. Revenue Primitives

Before describing specific business models, it is useful to enumerate the fundamental revenue mechanisms available in the ecosystem. Each model described in subsequent sections is a combination of one or more of these primitives.

### 4.1 Transaction Fees (On-Chain)

The Settlement Contract charges a **base fee** on every transaction. This fee is set by the processor and is collected automatically in the same atomic operation as the payment. It is denominated in the stablecoin being transacted, not in fiat. The processor receives it in their fee recipient wallet, from which it can be withdrawn at any time.

The base fee can be structured as a flat absolute amount, as a percentage, or — through the `calculateFees` function — as a combination of both. There is no bilateral agreement with the merchant required; the fee is built into the payment amount signed by the payer.

### 4.2 Subscription Fees (Fiat)

A processor may charge merchants a recurring subscription fee in fiat currency for access to the service. This subscription can be tiered by volume, feature set, or support level. It is entirely independent of the on-chain fee mechanism and is managed through the processor's standard billing infrastructure. Subscription revenue provides predictable cash flow that is decoupled from payment volume.

### 4.3 Acquirer Commission

A registered acquirer earns a percentage of the principal amount on every transaction that references their acquirer ID. This commission is set at registration and is automatically distributed by the Settlement Contract at the moment of settlement. The acquirer does not need to invoice the processor or wait for a remittance cycle; the funds are credited to their on-chain wallet in real time.

A processor who also registers as an acquirer on their own stack collects both the base fee and the acquirer commission on self-originated transactions.

### 4.4 Custody and Conversion Spread

In custodial configurations, the processor receives funds in stablecoin on behalf of the merchant and delivers fiat currency to the merchant's bank account. The difference between the stablecoin value and the fiat amount delivered — after market conversion — represents a conversion spread. This spread can be taken explicitly as a declared fee or implicitly through the rate applied.

This revenue primitive is only available to processors operating a custodial model. It does not exist in the non-custodial configuration, where funds flow directly to the merchant.

### 4.5 Float Income

In a custodial model, there is typically a period between when the processor receives the stablecoin and when the fiat is disbursed to the merchant. During that period, the processor holds a balance of stablecoins. Many stablecoins can be deposited in yield-bearing protocols that generate a return on the balance. This float income accrues to the processor and can be a meaningful revenue line at scale.

Even in a non-custodial model, there may be float in the Relayer account used to fund network transaction costs. A processor who holds a large Relayer balance can deploy idle capital to generate yield.

---

## 5. Custody Spectrum: A Critical Commercial Dimension

The most important decision a payment processor makes — commercially and operationally — is where on the custody spectrum it chooses to operate. This decision affects revenue sources, regulatory exposure, operational complexity, and the value proposition to merchants. The Stablecoin Stack supports the full range.

### 5.1 Non-Custodial Model

In the non-custodial model, the Settlement Contract transfers funds directly from the payer's wallet to the merchant's wallet. The processor never holds the merchant's funds. The processor's Relayer pays the network transaction cost and is reimbursed through the base fee, but this is a cost recovery mechanism, not a custody arrangement.

From the merchant's perspective, this means settlement is instantaneous and direct. There is no counterparty risk on the processor for the principal amount. The merchant receives their funds — in stablecoin — in the same session as the payment.

The commercial implication is that the processor's revenue is limited to the base fee and any subscription charges. There is no opportunity for conversion spread or float income on the settlement flow. However, the operational and regulatory burden is substantially lower, because the processor is acting as a relay rather than a custodian.

This model offers a genuinely new proposition to merchants: **same-session settlement with no custodial intermediary**. No traditional payment rail can offer this. Settlement on card networks takes at minimum one business day because the card issuer, processor, and acquiring bank are all custodial intermediaries in the flow. The non-custodial Stablecoin Stack processor settles in seconds.

### 5.2 Custodial Model

In the custodial model, the processor configures the Settlement Contract to deliver funds to a processor-controlled wallet rather than directly to the merchant. The processor then converts the stablecoin to fiat and remits to the merchant's bank account on a scheduled cycle.

This model closely mirrors the operational model of a traditional payment processor. The merchant sees a familiar experience — fiat settlement to their bank account, on a defined schedule. The merchant does not need to manage stablecoin wallets, understand conversion, or interact with the underlying technology in any way. This lowers the barrier to adoption for merchants who are not yet comfortable holding digital assets.

The custodial model opens additional revenue streams (conversion spread, float income) but also introduces regulatory obligations related to holding client funds, which vary significantly by jurisdiction.

Critically, the custodial model is also where **dispute and refund management** becomes commercially relevant. Since the processor holds the merchant's funds before disbursement, the processor can implement a refund mechanism: accepting refund requests from merchants and reversing the fiat disbursement, rather than attempting to reverse the on-chain transaction (which is not possible). This dispute capability can itself be offered as a premium service.

### 5.3 Hybrid Model

A processor may offer both models simultaneously, allowing each merchant to choose. Enterprise merchants with treasury capabilities may prefer non-custodial settlement. SME merchants may prefer the familiarity of fiat settlement. The processor earns differently from each cohort but serves a broader market.

---

## 6. Business Models for Payment Processors

### 6.1 Model PP-1: Transaction-Fee Processor

**Overview**

The most straightforward model. The processor charges a fee on every transaction, collected automatically through the Settlement Contract. There are no subscription fees and no fiat conversion services. Settlement is non-custodial: funds go directly to the merchant's wallet.

**Revenue Sources**

- On-chain base fee per transaction (percentage, flat, or combination).

**Value Proposition**

Simple, transparent pricing. Merchants pay only when they receive payments. No monthly commitments. Settlement is immediate and direct.

**Operational Profile**

Low complexity. The processor funds and maintains the Relayer, operates the stack, and handles merchant support. No fiat infrastructure is required.

**Suitable for**

Early-stage processors establishing market presence. Processors targeting merchants already comfortable holding digital assets. Niche or developer-focused deployments.

**Notable Consideration**

Revenue is entirely variable, which creates cash flow unpredictability. At low volumes, this model requires careful Relayer economics to ensure network cost recovery is consistent.

---

### 6.2 Model PP-2: Subscription-Based Processor

**Overview**

The processor charges merchants a recurring monthly or annual fee in fiat currency for access to the payment service. On-chain fees are set at or near cost recovery only. The commercial relationship is B2B and predictable.

**Revenue Sources**

- Monthly or annual subscription fee (fiat), tiered by volume or feature set.
- Minimal on-chain fee for Relayer cost recovery.

**Value Proposition**

Predictable cost for merchants. Suitable for merchants with stable, forecastable payment volumes who prefer a fixed operating cost over variable per-transaction fees.

**Operational Profile**

Requires standard SaaS billing infrastructure, customer success, and account management. The subscription pricing model rewards customer retention.

**Suitable for**

Processors targeting SME or enterprise merchants with predictable volumes. Processors who can articulate a broader product suite (dashboard, analytics, integrations) to justify the subscription.

**Notable Consideration**

Acquiring new merchants is harder when upfront commitment is required. A free trial or volume-based entry tier is commonly used to reduce friction.

---

### 6.3 Model PP-3: Blended Fee Processor

**Overview**

The processor combines a lower subscription fee with a reduced per-transaction fee. This is the most common commercial structure in mature payment processing businesses, because it aligns processor incentives with merchant growth while providing a revenue floor.

**Revenue Sources**

- Monthly subscription fee (fiat).
- Per-transaction on-chain fee.
- Optional acquirer commission if the processor self-registers as an acquirer.

**Value Proposition**

The processor benefits from both predictable base revenue and volume-correlated upside. Merchants benefit from lower per-transaction rates compared to a pure transaction-fee model.

**Operational Profile**

Requires both subscription billing and on-chain fee management. Slightly more complex to price and communicate, but the dominant model in payment processing for good reason.

**Suitable for**

Most general-purpose payment processor deployments. Particularly effective for processors with diverse merchant segments.

---

### 6.4 Model PP-4: Custodial Full-Service Processor (Fiat-Out)

**Overview**

The processor accepts stablecoin payments on the merchant's behalf, converts them to fiat, and disburses to the merchant's bank account. The merchant interacts entirely in fiat — they price in fiat, invoice in fiat, and receive fiat. The stablecoin payment rails are invisible to them.

**Revenue Sources**

- Per-transaction fee or subscription.
- Conversion spread (difference between stablecoin value and fiat disbursement amount).
- Float income during custody period.
- Optional premium for accelerated fiat disbursement (e.g., same-day settlement for an additional fee).
- Optional refund management service fee.

**Value Proposition**

Zero operational change for the merchant. The merchant does not need to know or care about the underlying payment technology. The processor provides the same experience as a traditional payment processor, but with materially lower fees on their end and potentially better economics passed to the merchant.

This model also eliminates the need for any dispute mechanism on the settlement path — since payments are irreversible, the processor absorbs no chargeback risk on the stablecoin leg. Any refund policy is managed entirely by the processor in their fiat disbursement layer.

**Operational Profile**

Highest complexity. Requires fiat banking relationships, stablecoin-to-fiat conversion operations, regulatory compliance for holding client funds, and standard payment processor compliance obligations.

**Suitable for**

Processors targeting merchants with no appetite for digital asset management. Businesses entering the market with existing fiat payments experience. Processors in regulated markets who want to offer a compliant, familiar product.

**Notable Consideration**

This model requires the most regulatory infrastructure. However, it also has the largest addressable merchant market, since it requires nothing from the merchant beyond a bank account.

---

### 6.5 Model PP-5: White-Label Processor

**Overview**

The processor deploys the Stablecoin Stack as infrastructure and licenses it — white-labelled — to other companies who want to offer stablecoin payment acceptance under their own brand. The white-label client operates as the merchant-facing brand; the underlying processor provides the infrastructure.

**Revenue Sources**

- Licence fee or revenue share from white-label clients.
- Infrastructure fee (per merchant onboarded, per transaction processed, or flat monthly).
- Optional: the white-label client may register as an acquirer on the underlying stack, with the processor earning a base fee on every transaction regardless.

**Value Proposition**

Banks, fintech platforms, e-commerce platforms, and accounting software companies all have merchant relationships but may not want to build payment infrastructure. A white-label arrangement allows them to launch a stablecoin payment product rapidly, under their brand, without engineering investment.

**Operational Profile**

Requires clear commercial agreements with white-label clients, multi-tenant infrastructure, and robust API and dashboard customisation capabilities. The processor operates in a B2B2B or B2B2C model.

**Suitable for**

Processors with strong technical infrastructure who prefer a wholesale distribution model over direct merchant relationships. Particularly valuable for processors who can serve multiple geographic markets through local white-label partners.

**Notable Consideration**

Revenue per transaction is lower than direct models, but scale can be significantly higher if white-label clients have large merchant networks. Brand visibility is low, but commercial exposure is diversified.

---

### 6.6 Model PP-6: SaaS Infrastructure Provider

**Overview**

The processor does not operate a payment processing service for end-merchants at all. Instead, it sells managed, hosted infrastructure — a fully configured Stablecoin Stack deployment — to other companies who want to become payment processors themselves.

**Revenue Sources**

- Monthly SaaS subscription for hosted stack access (Checkout Engine, Broadcast Layer, Relayer management).
- Usage-based billing (per API call, per transaction processed, per active merchant).
- Optional professional services for deployment and customisation.

**Value Proposition**

The Foundation has made the Stablecoin Stack open-source, but operating it reliably requires engineering capability, infrastructure investment, and ongoing maintenance. Many companies willing to enter the payment processing market do not have this capability. A SaaS provider abstracts it entirely: the customer deploys a payment processor business in days, not months.

This is the payments equivalent of a cloud hosting provider for traditional software. The SaaS provider operates the infrastructure; the customer operates the business.

**Operational Profile**

This model requires significant engineering investment in multi-tenancy, reliability, and developer experience. The SaaS provider's customers are themselves payment processors, so uptime and compliance are critical.

**Suitable for**

Infrastructure-focused companies with deep Stablecoin Stack expertise. Companies already operating cloud or developer platform businesses who want to expand into payment infrastructure.

**Notable Consideration**

This model is indifferent to payment volume at the application layer — revenue is derived from infrastructure usage, not from individual transactions. It scales with the number of processors on the platform rather than payment volume.

---

### 6.7 Model PP-7: Embedded Finance Processor

**Overview**

The processor integrates payment acceptance capabilities directly into a host platform — an e-commerce platform, an accounting system, a business management suite — so that merchants on that platform can accept stablecoin payments without leaving the platform or acquiring a separate payment account.

**Revenue Sources**

- Revenue share with the host platform on transaction fees.
- Direct subscription fee from merchants, shared with the platform.
- Platform referral fee for each merchant onboarded.

**Value Proposition**

Merchants discover and adopt the payment capability within their existing workflow, with minimal friction. For the host platform, adding stablecoin payment acceptance creates a new revenue stream and adds product stickiness.

**Operational Profile**

Requires deep integration work with the host platform's API and UI. Commercial agreements with platform operators are the primary business development activity.

**Suitable for**

Processors with strong developer and partnership capabilities. A particularly effective first entry point into a market, since the host platform provides immediate merchant distribution.

---

### 6.8 Model PP-8: Float-Yield Processor (Zero-Fee)

**Overview**

An unorthodox model in which the processor charges no transaction fee and no subscription fee. Revenue is generated entirely from deploying the stablecoins held in the Relayer and any custodial balances into yield-bearing instruments.

**Revenue Sources**

- Yield on stablecoin balances (Relayer account, any custodial merchant balances during processing window).
- Potential premium services offered to merchants who generate consistently high float.

**Value Proposition**

A zero-fee payment processor is a compelling market differentiator. Merchants pay nothing directly. The processor's incentive is to grow volume, since higher volume means larger float balances and higher yield.

This model was not viable on traditional payment rails, because the intermediary structure of card networks requires fees to compensate each party in the settlement chain. It becomes viable here because there is no mandatory fee sharing with card networks, banks, or issuers. The processor captures all economics.

**Operational Profile**

Requires careful treasury management and a reliable yield deployment strategy. The processor must be comfortable with the balance sheet risk of holding and deploying stablecoin assets. Regulatory treatment of yield operations varies by jurisdiction.

**Suitable for**

Processors with significant capital or access to capital. Treasury-focused operators. Processors operating in markets where the competitive advantage of zero-fee pricing is decisive.

**Notable Consideration**

This model inverts the traditional processor incentive: the processor wants merchants to generate consistent, high-volume payment flows, since the value is in the aggregate balance, not the per-transaction fee. This aligns processor and merchant interests unusually well.

---

### 6.9 Model PP-9: Vertical-Specific Processor

**Overview**

Rather than targeting merchants broadly, the processor focuses on a single industry vertical — hospitality, logistics, freelance services, gaming, cross-border payroll — and builds the checkout and reconciliation experience around the specific needs of that vertical.

**Revenue Sources**

- Any combination of transaction fees, subscriptions, and custodial services, priced for the vertical.
- Premium features specific to the vertical (e.g., split payments, recurring billing, escrow, multi-party disbursements).

**Value Proposition**

Generic payment processors serve every merchant with the same product. A vertical-specific processor can offer deep integrations with the software merchants in that vertical already use, terminology and reporting that matches their workflows, and pricing structures that align with how that vertical transacts.

**Operational Profile**

Requires domain expertise in the chosen vertical in addition to payment processing operations. Customer acquisition is narrower but conversion and retention are typically higher.

**Suitable for**

Founders with domain expertise in a specific industry. Processors entering a crowded market who need a differentiating focus.

---

## 7. Business Models for Acquirers

### 7.1 Model AQ-1: Classic Distribution Acquirer

**Overview**

The acquirer signs up merchants and refers them to a processor. When those merchants process payments, the acquirer's ID is included in the transaction, and the acquirer automatically earns their commission from the Settlement Contract.

**Revenue Sources**

- On-chain acquiring commission on all transactions from referred merchants.

**Value Proposition**

A straightforward channel partnership. The acquirer provides merchant relationships; the processor provides infrastructure; the Settlement Contract enforces the commission automatically with no billing disputes.

**Operational Profile**

Sales-focused. The primary investment is merchant acquisition: direct sales, marketing, and onboarding support. No technical infrastructure is required beyond a wallet to receive commissions.

**Suitable for**

Payment consultants, financial services agents, regional distributors, accountancy practices with merchant clients.

**Notable Consideration**

Commission income scales with the volume of referred merchants and their payment volumes. Building a portfolio of active merchants is the core business activity. Unlike traditional referral arrangements, the commission is guaranteed by the Settlement Contract rather than dependent on the processor honouring a commercial agreement.

---

### 7.2 Model AQ-2: Wallet-as-Acquirer

**Overview**

A digital wallet developer registers as an acquirer on a Stablecoin Stack processor. The wallet's users naturally include the acquirer ID in their payment payloads when paying at participating merchants. The wallet earns a commission on every transaction made through it.

**Revenue Sources**

- Acquiring commission on all payments made through the wallet.
- Optional: wallet subscription or premium features (unrelated to the acquirer model).

**Value Proposition**

The wallet converts its user base — payers — into a revenue-generating distribution channel. The more payers use the wallet, the more transactions carry the acquirer ID, and the more commission is earned. No interaction is required from merchants; merchants benefit from being reachable by wallet users.

**Operational Profile**

The acquirer's investment is in building and maintaining a high-quality payer wallet. The acquiring commission is a passive revenue stream once the user base is established.

**Suitable for**

Wallet developers, digital banking apps, super-apps with a payment component, loyalty card applications.

**Notable Consideration**

This model is novel. Traditional wallet providers do not earn commission from the payment networks whose transactions they facilitate. The Stablecoin Stack's open acquiring model makes this possible for the first time.

---

### 7.3 Model AQ-3: Point-of-Sale Hardware Acquirer

**Overview**

A company manufactures or distributes point-of-sale terminals or QR code display devices that are pre-configured to direct payments through a specific processor with the hardware vendor's acquirer ID embedded. Every payment made on a deployed device earns the hardware vendor an acquiring commission.

**Revenue Sources**

- Acquiring commission on all transactions processed through deployed hardware.
- Hardware sales or rental revenue (independent of the acquirer model).

**Value Proposition**

The hardware vendor creates a passive, ongoing revenue stream that continues for the life of each deployed device. A device installed at a merchant generates commission on every payment, indefinitely.

**Operational Profile**

Requires hardware manufacturing or distribution capability and a commercial relationship with a Stablecoin Stack processor. The acquiring commission is entirely passive once hardware is deployed.

**Suitable for**

Payment terminal manufacturers, QR code display vendors, self-checkout system integrators, POS software companies.

**Notable Consideration**

This model inverts the traditional hardware economics of the payments industry. Historically, terminal manufacturers earn from hardware sales; the payment economics flow to processors and card networks. Here, the terminal manufacturer participates directly in the payment economics, creating a recurring revenue stream on top of hardware margins.

---

### 7.4 Model AQ-4: Regional Agent Network

**Overview**

An acquirer recruits a network of local agents — small business owners, community representatives, informal distributors — who each earn a share of the acquirer's commission by signing up merchants in their area. The acquirer operates as a regional distribution hub, and agents operate as sub-distributors.

**Revenue Sources**

- Acquiring commission (net of agent payouts) on all transactions from the agent network's merchants.

**Value Proposition**

Enables penetration of markets where formal sales channels do not reach. Local agents have trust relationships with local merchants that a centralised processor does not. The commission model creates aligned incentives throughout the network.

**Operational Profile**

Requires agent recruitment, training, and management. Agent payouts are managed off-chain by the acquirer from the commissions received on-chain.

**Suitable for**

Operators targeting emerging markets or underserved geographies. Acquirers with community organising or franchise management experience.

---

### 7.5 Model AQ-5: Platform Acquirer (Marketplace or App Store)

**Overview**

A marketplace platform, app store, or SaaS company registers as an acquirer and embeds the Stablecoin Stack payment method into its platform. Merchants selling on the platform automatically use the platform's acquirer ID for all transactions.

**Revenue Sources**

- Acquiring commission on all transactions by platform merchants.
- Optional: incremental subscription revenue for payment acceptance as a feature.

**Value Proposition**

The platform monetises its existing merchant base through payment commission without building payment infrastructure. Merchants benefit from an integrated, seamless payment experience.

**Operational Profile**

Requires integration of the Stablecoin Stack checkout into the platform's merchant onboarding and product listing flows. Beyond integration, the acquiring commission is passive.

**Suitable for**

E-commerce platform operators, B2B marketplaces, freelance platforms, app stores, SaaS companies with embedded commerce.

---

### 7.6 Model AQ-6: Decentralised Autonomous Organisation (DAO) Acquirer

**Overview**

A collectively governed organisation — a DAO — registers as an acquirer on a Stablecoin Stack processor. The acquiring commission flows into a shared treasury governed by the DAO's members. Members may include merchants, payers, developers, or community contributors, who each hold governance rights over how the treasury is used.

**Revenue Sources**

- Acquiring commission directed to the DAO treasury.
- Treasury yield on accumulated commissions.

**Value Proposition**

A community of merchants or users can collectively register as an acquirer, pool their commission income, and self-govern its use. This creates a model in which the participants in a payment network own a stake in the economics of that network — a structure that has no precedent in traditional payments.

The DAO might, for example, use commission income to fund grants for open-source wallet development, subsidise merchant onboarding fees, or distribute earnings to members proportional to their payment volumes.

**Operational Profile**

Requires establishing governance structures and tooling for the DAO. The Stablecoin Stack's on-chain settlement and commission mechanism is naturally compatible with DAO treasury management.

**Suitable for**

Communities organised around a shared commercial interest. Open-source software ecosystems. Industry associations exploring shared payment infrastructure.

---

## 8. Combined Models: Processor and Acquirer in Conjunction

### 8.1 Model C-1: Vertically Integrated Full-Stack Operator

**Overview**

A single company operates both the payment processor infrastructure and registers itself as the acquirer for all directly acquired merchants. Every transaction processed by the company earns both the processor base fee and the acquirer commission.

**Revenue Sources**

- Processor base fee on all transactions.
- Acquirer commission on self-originated transactions.
- Subscription fees (optional).
- Conversion spread and float income if operating a custodial service.

**Value Proposition**

Maximum revenue capture per transaction. The company controls the full commercial relationship with merchants and payers.

**Operational Profile**

Requires the full operational capability of both a processor and an acquirer. This is the model most comparable to traditional payment processors who manage the full stack.

**Suitable for**

Operators with sufficient capital and operational capacity to build a full-scale payment business.

---

### 8.2 Model C-2: Cooperative Payment Network

**Overview**

A group of companies or industry participants jointly funds and operates a Stablecoin Stack processor as a shared utility. Each participant registers as an acquirer and earns commission on the merchants they bring to the network. The processor infrastructure costs are shared across all participants.

**Revenue Sources**

- Each participant earns acquirer commission on their merchant portfolio.
- Infrastructure costs are split, reducing the per-participant cost of operation.

**Value Proposition**

No single participant bears the full cost of processor infrastructure, but all participants benefit from a shared, reliable payment rail. Competition shifts to merchant acquisition and value-added services rather than infrastructure.

**Operational Profile**

Requires a governance structure for the processor entity, shared infrastructure investment, and agreed operational protocols.

**Suitable for**

Industry associations, banking cooperatives, regional payment networks, trade bodies.

**Notable Consideration**

This model enables groups of companies to launch a payment network that would be uneconomic for any single member to operate alone. It mirrors the cooperative model that underpinned the founding of major card networks, but without the proprietary infrastructure lock-in.

---

### 8.3 Model C-3: Franchise Payment Network

**Overview**

A franchisor establishes and operates the Stablecoin Stack processor infrastructure and recruits franchisees who operate as acquirers in defined geographic or vertical territories. The franchisor sets standards, provides tooling, and earns a base fee on all transactions. Franchisees earn acquiring commission on their territory's merchants.

**Revenue Sources**

- Franchisor: processor base fee on all transactions network-wide; franchise fees from franchisees.
- Franchisee: acquiring commission on merchants in their territory.

**Value Proposition**

The franchisor scales distribution rapidly without the operational overhead of managing a global direct sales force. Franchisees operate businesses with a defined revenue model and infrastructure support.

**Operational Profile**

Requires franchise agreement management, territory allocation, brand standards, and training programmes in addition to processor operations.

**Suitable for**

Companies with strong brand and operational systems seeking rapid geographic expansion.

---

## 9. Unorthodox and Emerging Models

The following models represent commercial structures that were difficult, impractical, or entirely impossible on traditional payment rails. They are presented here not as proven approaches but as commercially credible opportunities that the Stablecoin Stack's architecture makes available for the first time.

### 9.1 Model U-1: Merchant-Owned Processor Collective

**Overview**

A group of merchants collectively deploys and owns a Stablecoin Stack processor, eliminating third-party processor fees entirely. Each merchant in the collective is also registered as an acquirer, earning commission on their own transactions — which flows back to themselves.

**Revenue Sources**

- For the collective: none externally. Fees collected internally fund infrastructure costs.
- For individual merchants: net reduction in payment cost compared to third-party processing.

**Value Proposition**

Merchants pay the minimum fee necessary to sustain the infrastructure, rather than paying a margin to a commercial processor. This is the equivalent of a buying cooperative applied to payment processing.

**Why This Was Not Previously Possible**

Card network rules require merchants to transact through licensed acquirers and processors. A merchant cannot be its own acquirer in the card network model. The Stablecoin Stack has no such restriction; any entity can register as an acquirer on an open deployment, including the merchants themselves.

---

### 9.2 Model U-2: Pay-as-You-Grow (Revenue-Share Onboarding)

**Overview**

The processor onboards new merchants at zero upfront cost. Instead of charging subscription or setup fees, the processor takes a slightly higher transaction fee for an initial period, effectively recovering onboarding costs from merchant revenue rather than charging in advance.

**Revenue Sources**

- Higher transaction fee during the revenue-share period.
- Standard transaction fee or subscription thereafter.

**Value Proposition**

Removes the barrier of upfront cost for new merchants. Particularly effective for early-stage businesses with low initial volume but high growth potential.

**Notable Consideration**

The instant settlement model makes this less risky for the processor than in traditional payments: the processor earns each fee at the moment of settlement. There is no credit exposure to the merchant.

---

### 9.3 Model U-3: Refund-as-a-Service

**Overview**

A custodial processor offers refund management as a premium service. Because on-chain payments are irreversible, the processor implements refunds at the fiat disbursement layer: a merchant can request a refund on a settled transaction, and the processor reverses the fiat disbursement and credits the payer from the processor's own funds.

**Revenue Sources**

- Premium subscription tier that includes refund capability.
- Per-refund fee.
- Float income on merchant balances pending disbursement (a by-product of the custodial model).

**Value Proposition**

Merchants operating in consumer retail typically need to offer refunds to meet customer service expectations and regulatory requirements. A processor who manages this in the fiat layer provides a complete replacement for the card chargeback mechanism, without the card network's chargeback costs or dispute infrastructure.

**Why This Is Different From Traditional Chargebacks**

A card chargeback is initiated by the card issuer, often weeks after the transaction, and the merchant has limited ability to contest it. In this model, refunds are merchant-initiated and processor-managed. The processor has full visibility of the transaction and has already verified the payment cryptographically. There is no third-party dispute mechanism; the processor serves as the final arbiter of refund requests under whatever policy the merchant and processor have agreed.

---

### 9.4 Model U-4: Payment Data Marketplace

**Overview**

A processor — with explicit merchant consent and appropriate regulatory compliance — aggregates anonymised transaction data across its merchant network and offers commercial access to this data to market research firms, financial analysts, or consumer behaviour researchers.

**Revenue Sources**

- Data licensing fees.
- Custom analytics subscription.
- Core payment processing fees (independent revenue stream).

**Value Proposition**

Transaction data is among the most valuable datasets in commercial analytics. A processor with significant transaction volume possesses insights into consumer spending patterns, merchant performance, and market dynamics that are not available from any other source.

**Notable Consideration**

This model requires robust consent management and regulatory compliance (GDPR and equivalent frameworks in other jurisdictions). It is included here not as a primary model but as a secondary revenue stream available to processors at scale.

---

### 9.5 Model U-5: Treasury-as-a-Service Processor

**Overview**

The processor offers merchants a managed treasury service: instead of immediately converting stablecoin receipts to fiat, the processor holds the merchant's stablecoin balance and deploys it in yield-generating strategies, passing a share of the yield to the merchant while retaining a management fee.

**Revenue Sources**

- Treasury management fee (percentage of assets under management or share of yield).
- Core payment processing fees.

**Value Proposition**

Merchants who receive stablecoin payments but have no treasury expertise can earn a return on their payment receipts without managing the complexity themselves. The processor adds a financial management layer on top of the payment layer.

**Why This Is New**

Traditional payment processors settle in fiat and have no ability to offer yield on merchant funds between receipt and disbursement — they have no commercial relationship with the funds once disbursed. A custodial Stablecoin Stack processor who holds stablecoin balances can offer this naturally.

---

### 9.6 Model U-6: On-Chain DAO Processor

**Overview**

The payment processor itself is governed as a DAO. The Settlement Contract, fee parameters, and operational decisions are subject to on-chain governance by token holders. Revenue generated by the processor is directed to the DAO treasury and distributed to governance participants.

**Revenue Sources**

- Transaction fees directed to the DAO treasury.
- Treasury yield.
- Governance token appreciation (not a direct revenue model, but a value capture mechanism for participants).

**Value Proposition**

A processor governed as a DAO is owned by its participants — which could include merchants, acquirers, wallet developers, and community members. No single entity captures all the economics. Governance rights create aligned incentives across the ecosystem.

**Why This Is Possible Now**

On-chain governance and treasury management infrastructure is mature and well-tested. The Stablecoin Stack's Settlement Contract is already an on-chain component; adding governance mechanisms is an architectural extension. A DAO-governed processor is the natural endpoint of a fully open payment network.

**Notable Consideration**

Operating a DAO-governed processor introduces governance complexity and requires a carefully designed token economics model. It is not suitable as a first deployment but represents a compelling long-term evolution for a mature processor network.

---

### 9.7 Model U-7: Loyalty and Rewards Infrastructure

**Overview**

The processor integrates a loyalty programme at the payment layer. When a payer makes a payment, the Settlement Contract (or an accompanying smart contract) automatically issues loyalty points or cashback to the payer's wallet. The processor charges merchants a higher processing fee in exchange for the loyalty mechanism, which drives higher payer engagement and repeat visits to participating merchants.

**Revenue Sources**

- Higher processing fee from merchants who opt into the loyalty programme.
- Optional: programme management fee.

**Value Proposition**

Loyalty programmes have historically been operated separately from payment infrastructure, requiring bilateral integrations and delayed reconciliation. Embedding loyalty at the payment layer — so that every payment automatically triggers a loyalty credit — creates a seamless experience for both merchants and payers.

**Why This Is New**

Card network loyalty programmes are managed by card issuers, not processors. The processor has no ability to modify the economics of each transaction to include loyalty in the card model. In the Stablecoin Stack model, the processor can include any logic at the payment layer, including loyalty credits, because the Settlement Contract is programmable and open.

---

### 9.8 Model U-8: Programmable Escrow Service

**Overview**

The processor offers an escrow service where payment funds are held by the Settlement Contract — or a companion contract — until defined conditions are met. For example, a marketplace transaction might hold payment in escrow until the buyer confirms delivery, at which point the contract releases funds to the seller.

**Revenue Sources**

- Escrow service fee (flat per transaction or percentage).
- Core processing fee.
- Float income on escrowed funds.

**Value Proposition**

Escrow services in traditional commerce require a trusted third party and significant legal and operational infrastructure. Programmable escrow in the Stablecoin Stack model can be implemented in smart contract logic: the conditions are defined at the time of payment, and execution is automatic and trustless. This makes escrow economically viable for transactions of any size, including small individual purchases.

**Why This Was Not Previously Practical**

Traditional escrow requires legal infrastructure and minimum transaction sizes to justify the overhead. Programmable smart contract escrow has marginal cost near zero per transaction, making it viable for everyday commerce. The Stablecoin Stack's open architecture allows a processor to extend the Settlement Contract with escrow logic while remaining conformant with the base specification.

---

## 10. Comparative Summary

The following table provides a simplified comparison of the models described in this document across key commercial dimensions.

| Model | Custody | Primary Revenue | Complexity | Regulatory Exposure | Best For |
|---|---|---|---|---|---|
| PP-1: Transaction-Fee | Non-custodial | On-chain fee | Low | Low | Entry-level, developer-focused |
| PP-2: Subscription | Non-custodial | Subscription (fiat) | Low–Medium | Low | SME/enterprise B2B |
| PP-3: Blended Fee | Non-custodial | Fee + subscription | Medium | Low–Medium | General-purpose processors |
| PP-4: Custodial Fiat-Out | Custodial | Fee + spread + float | High | High | Mainstream merchant adoption |
| PP-5: White-Label | Either | Licence / revenue share | Medium | Medium | Wholesale distribution |
| PP-6: SaaS Infra | Either | SaaS subscription | High | Medium | Infrastructure-as-a-Service |
| PP-7: Embedded Finance | Either | Revenue share | Medium | Medium | Platform partnerships |
| PP-8: Float-Yield | Either | Float yield | High | High | Capital-heavy operators |
| PP-9: Vertical-Specific | Either | Fee + premium features | Medium | Varies | Domain-expert founders |
| AQ-1: Distribution | N/A | Acquiring commission | Low | Low | Sales-driven agents |
| AQ-2: Wallet | N/A | Acquiring commission | Low | Low | Wallet developers |
| AQ-3: POS Hardware | N/A | Commission + hardware | Medium | Low–Medium | Hardware vendors |
| AQ-4: Agent Network | N/A | Commission (net) | Medium | Low | Emerging markets |
| AQ-5: Platform | N/A | Commission | Low–Medium | Low | Marketplace operators |
| AQ-6: DAO Acquirer | N/A | Commission → treasury | Medium | Varies | Community-governed networks |
| C-1: Vertically Integrated | Either | Full stack | High | High | Full-scale operators |
| C-2: Cooperative Network | Either | Shared commission | High | Medium | Industry cooperatives |
| C-3: Franchise Network | Either | Fee + franchise | High | Medium | Rapid-scale operators |
| U-1: Merchant Collective | Self | Cost reduction | Medium | Low | High-volume merchant groups |
| U-2: Pay-as-You-Grow | Either | Deferred fee | Low–Medium | Low | Merchant acquisition |
| U-3: Refund-as-a-Service | Custodial | Premium subscription | Medium | Medium | Consumer retail processors |
| U-4: Data Marketplace | Either | Data licensing | Medium | High | Scale operators |
| U-5: Treasury-as-a-Service | Custodial | Management fee | High | High | Merchant treasury service |
| U-6: DAO Processor | Either | On-chain revenue | High | Varies | Community-owned networks |
| U-7: Loyalty Infrastructure | Either | Premium merchant fee | Medium | Low | Merchant engagement platforms |
| U-8: Programmable Escrow | Custodial (contract) | Escrow fee | Medium | Medium | Marketplace and P2P commerce |

---

## 11. Guidance for New Entrants

The following principles are offered as practical orientation for companies evaluating whether and how to build on the Stablecoin Stack.

**Start with the simplest model that generates revenue.** The blended fee model (PP-3) is a good default starting point for most general-purpose processors. It provides predictable base revenue through subscriptions and volume-correlated upside through transaction fees. Complexity can be added as the business grows.

**Let the open-source infrastructure work for you.** The full technical stack — Settlement Contract, Checkout Engine, Broadcast Layer, reference wallet — is available without licence cost. The Foundation's goal is adoption. The investment required to become a payment processor with the Stablecoin Stack is operational, not technical.

**Choose your position on the custody spectrum deliberately.** The non-custodial model is simpler, faster to deploy, and has lower regulatory exposure. The custodial model opens more revenue streams but requires more infrastructure. Most early deployments start non-custodial and add custodial services once the core business is established.

**The acquirer model is an underutilised distribution mechanism.** Most discussions of payment processing focus on the processor role. The acquirer mechanism in the Stablecoin Stack is a powerful and underappreciated tool for building distribution networks with aligned financial incentives that are enforced on-chain, without bilateral trust arrangements.

**The irreversibility of payments is a commercial advantage, not a limitation.** Many processors initially perceive the absence of a native chargeback mechanism as a disadvantage. In practice, it eliminates an entire category of operational overhead and fraud exposure. The refund-as-a-service model (U-3) shows how consumer-friendly refund policies can be maintained at the fiat disbursement layer without the systemic costs of network-level chargebacks.

**Settlement speed is a merchant value proposition, not just a technical feature.** The ability to tell a merchant that they will receive funds in their wallet within seconds of a payment — not the following business day — is a concrete, quantifiable advantage over traditional payment rails. Building commercial messaging around this advantage is effective, particularly for merchants with cash flow sensitivity.

---

## 12. License

Copyright © 2026 Stablecoin Stack Foundation. All rights reserved.

This document is published under the [Apache License, Version 2.0](https://www.apache.org/licenses/LICENSE-2.0). Unless required by applicable law or agreed to in writing, material distributed under the License is distributed on an "AS IS" BASIS, WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.