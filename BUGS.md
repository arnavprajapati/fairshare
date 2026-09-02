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

**How to reproduce:**

**What is wrong:**

**What I changed:**

---
