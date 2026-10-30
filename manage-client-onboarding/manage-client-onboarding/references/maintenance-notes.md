# Maintenance notes

## Google Sheets connector

Inspect metadata and the precise target row before writes. Keep the worksheet numeric ID distinct from its title. Read the connector Row ID from current Get rows output; never hardcode a historical row number or Row ID.

The personal Drive connection previously returned 403 for the business-owned tracker while the signed-in business browser and Power Automate connection could access it. Check account scope before concluding that Sheets itself is broken. Do not broaden sharing as an automatic fix; use an authorized account or approved fallback.

The designer previously returned 429 during static file/table schema lookups. Runtime references to configuration values for dataset/table avoided repeated design-time schema lookup. Do not classify a generic 429 as bot detection unless the site explicitly identifies it as such.

Use complete business-row objects for update actions when schema-based individual fields fail. Preserve existing row values, remove connector-only fields as required by the live schema, and supply the connector Row ID separately. Remove obsolete advanced parameter mappings such as item.item/LastAction when the designer reports they are no longer in the operation schema; do not leave duplicate per-field and root-item mappings.

Enable Get rows pagination and verify continuation handling. A large Top Count alone did not guarantee all rows were returned. Do not infer the tracker is empty from an incomplete first page.

DATE_TIME table columns previously flagged ISO strings written by the connector. Diagnose the live stored value and column type separately from whether the event time is correct; do not claim that issue is fixed solely because a flow succeeded.

## Power Automate browser editing

Use supported UI controls only. Read fresh page state after mutations; the designer and Sheets can briefly expose the preceding selection or run. A successful click is not proof that a save, enable toggle or paste completed.

- Canvas actions can be virtualized. Expand the relevant scope and use Zoom view to fit before assuming an action is missing.
- Code view is useful to inspect saved definitions. Use the visible editor's select-all/copy and read the clipboard after the copy has actually completed.
- Copy entire action for scopes and Copy action for individual actions use different clipboard structures. Do not assume they share a schema or paste stale clipboard data over a newer flow.
- If replacing a scope through the supported clipboard UI, preserve current connection metadata, action names, runAfter dependencies and all descendants. Keep a current rollback copy and verify the saved result.
- Rich text fields can retain expression chips when filled. Explicitly remove the old chip before inserting a replacement and verify the final code has one body expression, not duplicated content.
- Formula-bar reads in Sheets can lag a new Name box selection. Verify the active cell and close an open dropdown before treating a blank value as data loss. Do not write several cells based on stale readback.
- Resubmit can open a JavaScript confirmation. Inspect and accept the actual dialog only for an authorized replay; a button click alone may not start a run.
- Flow status controls can change layout between an Enabled switch and Turn on/Turn off menu items. Re-read the current controls and verify the Status group after changing them.

## Verification and messages

A Running parent or Waiting child can persist while email has already been delivered. Inspect completed run output and the actual tracker before retrying. Connector retries previously took minutes.

Keep serial processing for booking/idempotency protection. Investigate trigger concurrency warnings using actual skipped/queued runs; do not disable serialization merely to hide a warning or launch overlapping tests repeatedly.

Verify actual recipient-side delivery when that mailbox is accessible; otherwise report send-action success and ask the user to confirm receipt. Earlier Gmail access represented a different personal mailbox and did not provide access to every test address.

Separate initial preparation mail, the agreement, the analysis booking-link email, Calendly's standard confirmation, and the portfolio evidence email. A link sent is not a meeting booked; a form sent is not a form submitted; a spreadsheet status is not a signed legal agreement.
