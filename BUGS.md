# Bugs found

Add one section per issue. Bug 1 is filled in to show the format — fix it, then write what you changed. Copy the blank template for the rest.

Keep this file in the repo and **commit it** with your fixes.

---

## Bug 1

**How to reproduce:** Open the app. The expense list says “Newest first”. The first row is Wine (7 Mar). Board game (15 Mar) is further down.

**What is wrong:** The list is showing oldest expenses first. Newest should be at the top.

**What I changed:** In `src/lib/format.js`, updated the `dateValue` helper function to return a numeric timestamp (`new Date(date).getTime()`). In `src/components/ExpenseList.jsx`, updated the expense list sort comparator to descending order (`(a, b) => dateValue(b.date) - dateValue(a.date)`) so that the newest expenses are displayed first.

---

## Bug 2

**How to reproduce:** Check the Balances panel on initial load. Aisha paid $148.00 and should be owed money, but the UI shows Aisha owes money in red (`owe` class). Members who underpaid are marked as `is owed` in green.

**What is wrong:** A positive net balance (`paid - consumed > 0`) indicates that the group owes money to the member (credit), while a negative balance indicates that the member owes money to the group (debit). The conditions, text labels, and CSS classes were inverted.

**What I changed:** In `src/components/BalancesPanel.jsx`, updated the positive balance condition (`bal > 0.005`) to display `is owed ${formatMoney(bal)}` with class `"owed"` (green), and updated the negative balance condition (`bal < -0.005`) to display `owes ${formatMoney(-bal)}` with class `"owe"` (red).

---

## Bug 3

**How to reproduce:** Set up balances where a debtor's total debt equals a creditor's exact credit amount (e.g., Debtor owes $50 and Creditor is owed $50). Open the Settle Up panel.

**What is wrong:** In the greedy two-pointer settlement loop in `suggestSettlements`, when `d.amount === c.amount`, the `else` branch increments both pointers (`i += 1; j += 1;`) without adding the transfer to the `transfers` array. As a result, the settlement transaction is dropped and never shown.

**What I changed:** In `src/lib/settle.js`, added `transfers.push({ from: d.id, to: c.id, fromName: nameOf(d.id), toName: nameOf(c.id), amount: d.amount })` inside the `else` block before incrementing the indices.

---

## Bug 4

**How to reproduce:** Create an expense where Person A pays $60, but splits it only between Person B and Person C (Person A is unchecked in `splitWith`). Check Person A's balance.

**What is wrong:** `computeBalances` in `src/lib/balances.js` contained an incorrect block that deducted an equal share (`amount / n`) from the payer's balance whenever the payer was not in `splitWith`. As a result, the payer was reimbursed only partially instead of 100%, and the group balances failed to sum to zero.

**What I changed:** Removed the invalid deduction block from `src/lib/balances.js` so that non-participant payers receive full reimbursement for the money they paid.

---

## Bug 5

**How to reproduce:** Filter expenses by Category "Food" or type "Dinner" in the search box. Click "Delete" or edit the amount on any row in the filtered list.

**What is wrong:** `ExpenseList` was passing the array index of the filtered/sorted view (`index`), but the reducer in `src/state/store.js` modified `state.expenses[action.index]` on the master list. As a result, deleting or updating an item while filters or sorting were active corrupted or deleted completely different expenses.

**What I changed:** Updated `DELETE_EXPENSE` and `UPDATE_EXPENSE` actions to target expenses by their unique `id` instead of array index. Updated `ExpenseList.jsx` and `App.jsx` to pass `expense.id` for delete and update handlers.

---

## Bug 6

**How to reproduce:** 
1. Select any member (e.g. Aisha) from the "Paid by" dropdown in the filter bar. The expenses list becomes empty.
2. In the "Add expense" form, choose "%" (custom split) for 3 people, enter `33.33%`, `33.33%`, and `33.34%`, and attempt to submit. An error is shown that percentages must sum to 100%.

**What is wrong:** 
1. In `src/App.jsx`, the "Paid by" filter compared `e.paidBy !== paidBy`. The dropdown state is a string (`"1"`), whereas `e.paidBy` is stored as a number (`1`), causing strict inequality to reject all matches.
2. In `src/lib/money.js`, `percentsSumTo100` performed a strict equality check `sum === 100`. In JavaScript, `33.33 + 33.33 + 33.34` sums to `100.00000000000001`, failing validation for valid splits totaling 100%.

**What I changed:** 
1. In `src/App.jsx`, updated the filter condition to compare string values: `String(e.paidBy) !== String(paidBy)`.
2. In `src/lib/money.js`, updated `percentsSumTo100` to allow an epsilon tolerance: `Math.abs(sum - 100) < 0.01`.

---

## Bug 7

**How to reproduce:**

**What is wrong:**

**What I changed:**

---
