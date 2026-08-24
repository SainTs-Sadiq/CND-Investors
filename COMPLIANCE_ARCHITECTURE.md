# CND Investors — Compliance & Backend Architecture

## Status
The frontend is connected to the dedicated Supabase project and the database now contains the core investor, KYC/AML, suitability, transaction, withdrawal, ledger, custody/reconciliation, compliance-flag and audit structures.

Financial execution remains gated. The system must not be treated as licensed, compliant, custodial, or authorized to accept customer funds until the business has completed the applicable legal, licensing, AML, operational and financial-control work.

## Configurable compliance engine
Admin-controlled settings include:
- KYC tiers and required evidence
- investment caps
- Travel Rule threshold
- OFAC/UN/EU/UK screening toggles
- risk-warning text
- transaction-monitoring thresholds
- reconciliation alert threshold
- jurisdiction profiles for US/FinCEN, EU/MiCA, UK/FCA and an offshore baseline

Changing a profile is an audited privileged operation. The jurisdiction presets are technical configuration templates, not legal advice and not a substitute for jurisdiction-specific legal review.

## KYC / AML
The schema supports KYC cases, documents, risk levels, source-of-funds/source-of-wealth data, PEP/sanctions flags and review history. Provider adapters are prepared for Sumsub, Onfido, Shufti Pro, ComplyAdvantage, Chainalysis KYT, TRM Labs and Elliptic. Provider API secrets must be stored as server-side Edge Function secrets and never in browser code.

`kyc-provider-webhook` is the provider-neutral webhook endpoint. It requires `KYC_WEBHOOK_SECRET` and maps provider statuses into the platform KYC state machine.

## Suitability
Suitability questions, assessments, plan risk requirements and rolling 12-month investment caps are stored in the database. `validate_investment()` performs server-side eligibility checks for KYC status, minimum amount, suitability and limits.

## Transaction monitoring
`transaction-monitor` provides server-side rule evaluation for large transactions, repeated withdrawals and rapid investment/withdrawal patterns. Compliance flags are persisted in `compliance_flags` for review.

## Travel Rule
Withdrawals use a server-side preparation function. When the configured threshold is reached, recipient name, wallet/destination and country are required and stored with the withdrawal. Sensitive Travel Rule payloads should be encrypted using a production key-management design before live deployment.

## Financial controls
Balances are separated into available, pending and locked amounts. Withdrawal requests reserve available balance and move it to pending. Rejection returns the amount. Privileged approval is performed through server-side functions. Direct client writes to financial tables are revoked.

A full production financial ledger should use a formal double-entry accounting model, immutable posting rules, reconciliation and independent finance approval before real money is enabled.

## Custody
`custody_accounts` separates customer assets, operational assets and liquidity buffer. `custody_reconciliations` stores internal vs custodian balances and percentage differences. Fireblocks, BitGo and Copper are represented as provider registry options. Actual custody credentials and execution should remain in server-side infrastructure.

## Audit trail
Privileged events are stored in `audit_logs`. `audit-chain` adds previous-hash and chain-hash metadata. For regulator-grade immutability, production should additionally write audit snapshots to append-only/WORM storage with restricted deletion permissions and independent retention controls.

## Reporting
`regulatory-report` supports investor, AUM, transaction and suspicious-activity datasets and CSV/XLS/PDF/JSON response formats. Reports should be reviewed and retained according to the applicable regulatory record-keeping requirements.

## Required production controls before real funds
1. Jurisdiction-specific legal opinion and licensing/registration determination.
2. Approved AML/KYC programme and responsible compliance function.
3. Sanctions/PEP/adverse-media vendor contracts and tested screening workflows.
4. Identity-verification provider and secure document-storage configuration.
5. Source-of-funds/source-of-wealth and enhanced-due-diligence procedures.
6. Transaction-monitoring thresholds validated by compliance personnel.
7. Formal double-entry ledger and finance reconciliation procedure.
8. Maker/checker approval and segregation of duties.
9. Custodian/payment-provider contracts and reconciliation process.
10. Security review, penetration testing, backup/recovery and incident response.
11. Privacy/data-protection assessment and lawful data-retention schedule.
12. Terms, risk disclosures, client agreements and complaint-handling procedures.
13. Production secrets/key management and rotation procedures.
14. Regulatory reporting and suspicious-activity escalation procedures.
15. Controlled production go-live approval by legal, compliance, finance and security owners.
