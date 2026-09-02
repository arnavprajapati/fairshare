# FairShare

FairShare helps a group of friends share costs on a trip.

Imagine four people on a weekend away. Someone pays for dinner, someone else pays for the cab, someone books the stay. Instead of arguing in a chat thread, they log each expense here: who paid, how much, and who should share it. The app then shows who is in credit, who still needs to pay in, and a simple list of transfers that would settle the group.

This repo is a working app, not a starter template. Get it running, use it the way a traveler would, and read the code when something does not add up. Improve **this** project. Do not start over or add new libraries.

## How bill splitting should work

FairShare is meant to match how people actually split money in real life.

**Recording a bill.** Each expense is one real payment: a description, an amount, who paid the merchant, and who that cost is for. The payer is the person whose card or cash went out. The split is the people who should carry that cost.

**Equal split.** If three people share a dinner equally, each of them is on the hook for the same portion of that dinner. Those portions together should make up the full bill — the group should not “lose” or “invent” money in the rounding.

**Uneven split.** Sometimes one person had the expensive dish, or only two of the four used the cab. Custom percentages are for that. The percentages are just a way of describing the split; in dollars, the shares should still cover the original amount.

**Paying for other people.** It is normal that the person who paid is not on the split. Someone can put a cab on their card even if they did not ride. They should get that fare back in full. Only the people who used it should owe a share. Anyone who was not involved should be left out of that bill.

**Running balance.** Over the whole trip, each person’s position is simple: add up what they paid out, subtract what they actually consumed (their share of each bill they were on). If they have paid more than their share, the group owes them. If they have paid less, they still owe the group. Across everyone, those positions should cancel out — this is a closed group, not a bank.

**Settling up.** The settle-up list is a shortcut so people do not have to ping each other one expense at a time. After those suggested payments, nobody should still be in credit or in debt.

**Using the app.** Filters, edits, new people, and coming back later should not change the story of an expense. What you click is what should change. What the screen claims (who paid, who owes whom, which bills you are looking at) should match the data.

The demo group is a weekend in Goa with a handful of bills already logged. Use those, and add your own, to see whether the app behaves as above.

## Run it


You need Node.js 18 or newer.

```bash
npm install
npm run dev
```

Then open the local URL (usually `http://localhost:5173`).

The demo group is stored in your browser. If you want the original demo data back, delete `fairshare-v1` under DevTools → Application → Local Storage, and refresh.

## What we want from you

Find problems, fix them, and record them in `BUGS.md` (this file is already in the repo). Commit `BUGS.md` together with your code changes.

## How to submit

1. Create a **new public repository** on your GitHub account (do not fork an existing private company repo).
2. Push your work there, including your filled-in `BUGS.md`.
3. Send us the repository URL.

---

## 🛠️ Summary of Fixes & Improvements

Below is the complete summary of all **11 domain logic, mathematical, and React state issues** identified and resolved across the codebase:

| # | Issue Area | Affected Files | Problem Description | Resolution |
| :-: | :--- | :--- | :--- | :--- |
| **1** | **Expense List Sort Order** | `ExpenseList.jsx`, `format.js` | UI claimed "Newest first" but expenses were sorted oldest first; `dateValue` returned raw string. | Updated sort comparator to descending order and made `dateValue` return numerical timestamp. |
| **2** | **Inverted Balances Status** | `BalancesPanel.jsx` | Positive balances were shown as "owes" (red) and negative balances as "is owed" (green). | Inverted condition and CSS classes so positive balance displays "is owed" (green) and negative displays "owes" (red). |
| **3** | **Settlement Transfer Drop** | `settle.js` | Greedy settlement algorithm omitted `transfers.push` when debtor and creditor amounts were exactly equal. | Added transfer push in the `else` branch of `suggestSettlements` before advancing pointers. |
| **4** | **Non-Participant Payer Deduction** | `balances.js` | Payers not included in the split had an equal share erroneously deducted from their reimbursement balance. | Removed erroneous deduction block to ensure non-participant payers receive 100% full reimbursement. |
| **5** | **Data Corruption on Delete/Edit** | `store.js`, `ExpenseList.jsx`, `App.jsx` | Delete and edit operations passed filtered array indices to the reducer, mutating/deleting wrong items under active filters. | Migrated `DELETE_EXPENSE` and `UPDATE_EXPENSE` actions to target items by unique `id`. |
| **6** | **"Paid by" Filter Type Coercion** | `App.jsx` | Filter compared string dropdown value (`"1"`) with number ID (`1`) using strict inequality, hiding all results. | Coerced comparison to strings (`String(e.paidBy) !== String(paidBy)`). |
| **7** | **Floating-Point % Validation** | `money.js` | Custom percentage splits totaling 100% (e.g. `33.33 + 33.33 + 33.34`) failed strict `sum === 100` validation. | Added floating-point epsilon tolerance (`Math.abs(sum - 100) < 0.01`). |
| **8** | **Penny Rounding in Equal Split** | `money.js` | Equal split rounded individual shares independently, losing/inventing pennies (e.g., $100 / 3 = $99.99). | Implemented integer cent division with remainder cent allocation to guarantee exact total match. |
| **9** | **Date Serialization on Reload** | `store.js`, `format.js` | Reloading from `localStorage` left dates as unparsed ISO strings, breaking date formatting helpers. | Hydrated stored data with `hydrate(JSON.parse(raw))` and added safe `new Date(date)` parsing in `formatDate`. |
| **10** | **Summary Cards Missing Dependency** | `SummaryCards.jsx` | `useMemo` calculating per-person totals omitted `members` in dependency array, failing to update on member addition. | Added `members` to `useMemo` dependency array (`[members, expenses]`). |
| **11** | **Form Inputs Reset on Submit** | `AddExpenseForm.jsx` | Description and amount inputs remained filled after submitting a new expense. | Reset form state fields (`setDescription("")`, `setAmount("")`) upon successful submission. |
