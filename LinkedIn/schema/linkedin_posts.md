# Data table: `linkedin_posts`

n8n Data Table used by every LinkedIn workflow in this folder. Create it with this exact name. The Data Table nodes look the table up by name (`mode: name`, value `linkedin_posts`).

`id`, `createdAt`, and `updatedAt` are system columns n8n adds on the table. Add the other columns yourself before publishing the workflows that write rows.

## Columns

| Column | Type | Source | Role |
| --- | --- | --- | --- |
| `id` | number | system | Row id. Tools and the web UI call this `draft_id`. |
| `content` | text | user | LinkedIn post body. |
| `status` | text | user | Lifecycle. See values below. |
| `scheduled_at` | datetime | user | When workflow 03 may publish the row. ISO 8601 with a timezone offset. |
| `published_at` | datetime | user | Set when workflow 03 marks the row published. |
| `approved_at` | datetime | user | Set when workflow 02 approves or reschedules a row. |
| `session_id` | text | user | Chat or web UI session that owns the draft. |
| `createdAt` | datetime | system | Row creation time. |
| `updatedAt` | datetime | system | Last update time. |

## Status values

| Status | Meaning |
| --- | --- |
| `pending_approval` | Draft saved by workflow 04. Not approved for publishing. |
| `scheduled` | Approved by workflow 02, with a future `scheduled_at`. Workflow 03 picks these up. |
| `publishing` | Workflow 03 claimed the row and is calling LinkedIn. |
| `published` | LinkedIn accepted the post. `published_at` is set. |
| `failed` | LinkedIn create failed. Workflow 03 sets this, then raises the error so workflow 06 can email. |

Workflow 02 schedules a row only from `pending_approval` or `scheduled`. A `published` row cannot be rescheduled. The web UI can reopen a `failed` row back to `pending_approval` before saving edits.

## Who writes which fields

| Workflow | Writes |
| --- | --- |
| 04 Save LinkedIn Draft | Inserts `content`, `status=pending_approval`, `session_id`. Updates `content` (and keeps `pending_approval`) when the session already has a pending draft or a `draft_id` is supplied. |
| 02 Save Approved Post | Updates an existing row to `status=scheduled` with `content`, `scheduled_at`, and `approved_at`. Requires a real `draft_id`. |
| 03 Publish Due Posts | Reads `status=scheduled`. Sets `publishing`, then `published` plus `published_at`, or `failed`. |
| 05 Get LinkedIn Drafts | Reads rows. Returns `content`, `status`, `session_id`, `scheduled_at`, `published_at`, `approved_at`. |
| 07 Overdue Post Watchdog | Reads all rows. Emails about `scheduled` rows whose `scheduled_at` is in the past, any `publishing` row, and any `failed` row. |
| 08 Web UI | Reads all rows for the queue. Can delete a row, reopen a `failed` row to `pending_approval`, and calls 04 and 02 for save and schedule. |
