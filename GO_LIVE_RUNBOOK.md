# CND Investors — Go-Live Money Lifecycle Runbook

Run this controlled checklist before accepting production deposits.

## 1. Static deposit addresses
- Enter the real CND-controlled receiving address for every supported asset/network.
- Independently verify each address on the correct blockchain/network.
- Keep addresses inactive until verified.
- Activate only the intended address/network combination.
- Confirm the public client portal displays the exact same address.

## 2. Controlled deposit test
Use a small controlled test transaction.

Expected path:
1. Investor selects asset/network.
2. Client sees the static CND address.
3. Client sends funds.
4. Client submits amount and transaction hash.
5. Operations/compliance verifies destination, asset, network, received amount and confirmations.
6. Compliance approval is recorded.
7. Finance approval is recorded.
8. Server-side ledger credit creates a transaction and ledger entry.
9. Client available balance increases exactly once.
10. Audit log contains the credit event.

Do not manually edit the account balance to make the test pass.

## 3. Investment test
1. Investor has approved KYC.
2. Investor has a current suitability assessment.
3. Plan is active.
4. Amount satisfies minimum and 12-month cap.
5. Risk profile satisfies plan requirement.
6. Operations creates an allocation through the server-side function.
7. Available balance decreases and locked balance increases atomically.
8. Investment principal/current value are created.
9. Trade allocation records strategy and manual execution reference.
10. Transaction, ledger and audit records exist.

## 4. Manual performance update
Record a controlled valuation through the server-side valuation function.

Verify:
- investment current value changes;
- portfolio event is created;
- audit event is created;
- no client balance is silently changed by a valuation update.

## 5. Redemption / maturity
After the investment is eligible for redemption:
- Verify the investment is active/matured.
- Finance/operations invokes the redemption function.
- Locked balance decreases by the investment current value.
- Available balance increases by the same value.
- Redemption transaction and ledger entry are created.
- Investment becomes matured.

## 6. Withdrawal test
1. Investor requests withdrawal.
2. Server reserves funds.
3. Compliance review occurs.
4. Finance review occurs.
5. Staff manually executes the external payment using the approved custody process.
6. Transaction reference/hash is recorded.
7. Withdrawal is completed only after execution is confirmed.
8. Rejected withdrawals release the reserved funds.

Never mark a withdrawal completed merely because the admin clicked an approval button.

## 7. Negative tests
Test that:
- an ordinary investor cannot read another investor's deposits;
- an ordinary investor cannot modify balances;
- an ordinary investor cannot call privileged financial functions;
- an unapproved KYC investor cannot be allocated to an investment;
- a missing suitability assessment blocks allocation;
- a risk mismatch blocks allocation;
- an over-cap allocation blocks;
- a duplicate transaction hash cannot be submitted;
- a deposit cannot be credited twice;
- an unconfirmed deposit cannot be credited;
- a rejected deposit cannot be credited;
- a withdrawal cannot exceed available balance;
- a non-matured investment cannot be redeemed;
- inactive deposit addresses are not shown to clients.

## 8. Production security
- No private keys in GitHub, Supabase tables, browser JavaScript, logs or client-visible metadata.
- No service-role key in the frontend.
- Rotate any secret that was exposed during development.
- Verify Supabase RLS policies using real investor and staff test accounts.
- Enable production backups and recovery procedures.
- Configure domain, HTTPS, email authentication and password reset.
- Confirm admin roles are assigned to named personnel only.
- Confirm maker/checker responsibilities are understood.

## 9. Launch approval
The go-live decision should be signed off by the owners' legal/compliance, finance, operations and security owners. Technical readiness does not itself establish legal authorization.
