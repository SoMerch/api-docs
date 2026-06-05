
---

# `docs/approvals.md`

```md
# Approvals

This document describes planned approval-related API concepts for the SoMerch platform.

Approval workflows help teams control budgets, review orders, and keep procurement-friendly records.

---

## Approval statuses

| Status | Description |
|---|---|
| `pending` | Approval is waiting for a decision. |
| `approved` | Approval has been granted. |
| `rejected` | Approval has been rejected. |
| `changes_requested` | The approver requested changes. |
| `expired` | The approval request expired. |
| `cancelled` | The approval request was cancelled. |

---

## Example approval object

```json
{
  "id": "apv_12345",
  "order_id": "ord_98765",
  "status": "pending",
  "approver_email": "ops@example.com",
  "budget_tag": "HR-Onboarding-2026",
  "requested_at": "2026-06-05T10:00:00Z"
}
