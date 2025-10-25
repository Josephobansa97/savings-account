A **Stacks Clarity smart contract** implementing a simple on-chain **savings account** that automatically **accrues interest over time**.  
Users can **deposit**, **withdraw**, or **claim interest** in STX, with transparent, per-block interest computation.

---

## 🚀 Features

- 🏦 **Deposit STX** — Users deposit STX into the contract and start earning interest immediately.  
- 💸 **Withdraw STX** — Withdraw principal plus accrued interest anytime.  
- 📈 **Accrued Interest** — Interest compounds per block, based on the time elapsed since last update.  
- 💰 **Claim Interest Only** — Claim earned interest while keeping principal deposited.  
- 🔒 **Error Handling** — Includes checks for zero-amount deposits and insufficient balances.  
- 🧮 **Fixed-Point Interest Calculation** — Uses constants for precision in per-block rate calculations.

---

## ⚙️ Constants

| Constant | Description | Example Value |
|-----------|--------------|----------------|
| `ERR_INSUFFICIENT_BALANCE` | Error code for withdrawals exceeding available balance | `u100` |
| `ERR_ZERO_AMOUNT` | Error code for zero or invalid amount | `u101` |
| `INTEREST_RATE_PER_BLOCK` | Approx. 0.000238% per block (5% annualized) | `u238` |
| `RATE_DENOMINATOR` | Denominator used for fixed-point math | `u100000000` |

---

## 🧱 Data Structures

| Name | Type | Description |
|------|------|--------------|
| `deposits` | `(map principal { amount: uint, last-update: uint })` | Stores each user’s deposit amount and last update block |
| `contract-balance` | `(var uint)` | Tracks the contract’s total balance for verification |

---

## 🧩 Public Functions

### `deposit (amount uint)`

Deposit STX into the savings account and start earning interest.

```clarity
(contract-call? .savings deposit u1000000)
Validations:

Fails if amount <= 0

Updates user deposit and timestamp

Updates internal contract-balance

withdraw (amount uint)
Withdraw STX (principal + accrued interest).

clarity
Copy code
(contract-call? .savings withdraw u500000)
Validations:

Fails if withdrawal exceeds total balance

Updates deposit record and transfers STX to caller

claim-interest
Claim accrued interest while keeping your principal in the contract.

c
Copy code
(contract-call? .savings claim-interest)
Validations:

Fails if accrued interest is zero

Updates last-update block and transfers only the interest

🔍 Read-Only Functions
Function	Description
get-deposit (user principal)	Returns the current deposit and last-update block for a given user
get-accrued-interest (user principal)	Computes and returns current accrued interest for a user

🧠 Internal Logic
Interest is calculated as:

ini
Copy code
interest = (blocks_passed * amount * INTEREST_RATE_PER_BLOCK) / RATE_DENOMINATOR
blocks_passed = current block height − last update height

Uses fixed-point division for precision

Accrued interest added on each deposit, withdrawal, or claim action

🧪 Example Workflow
User deposits 1,000,000 µSTX:

clarity
Copy code
(contract-call? .savings deposit u1000000)
Waits several blocks while interest accrues.

Checks accrued interest:

clarity
Copy code
(contract-call? .savings get-accrued-interest tx-sender)
Claims interest only:

clarity
Copy code
(contract-call? .savings claim-interest)
Withdraws full balance:

clarity
Copy code
(contract-call? .savings withdraw u1000000)
🔐 Error Codes
Code	Meaning
u100	Insufficient balance
u101	Invalid or zero amount
