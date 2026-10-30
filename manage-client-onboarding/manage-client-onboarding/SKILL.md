---
name: manage-client-onboarding
description: Maintain, troubleshoot and test Global Bridge Music Art And Culture's Calendly Free, Google Calendar, Google Sheets and Power Automate consultation and client onboarding process. Use for continuing this project, repairing booking-to-tracker or email issues, matching analysis bookings by email, configuring agreement and evidence-form stages, or running an end-to-end test.
---

# Global Bridge Client Onboarding

## Start with live evidence

Read [project-map.md](references/project-map.md) for the known files, flow identities, worksheet schema and business rules. Treat the recorded configuration and status as a starting point, not proof of current state. Inspect the current saved flow, recent run and relevant tracker row before changing them. User edits, cleared test rows and cancelled appointments can make conversation history stale.

Keep the workflow in two flows. Use the existing Calendly event on the Free plan for both appointment stages. Keep payment collection disabled unless the user explicitly changes that requirement. Do not add a paid dependency or another event type as a routine repair.

Use the Google Drive/Google Sheets skill for spreadsheet work, Documents or Google Docs skills for agreement edits, and Resolve Recipients for person-directed tests. Prefer connected tools for supported operations. Use the browser for Power Automate and for previously authorized operations not supported by a sufficient connector. Follow the current browser and authentication guidance; do not use direct service requests or hidden application state to bypass it.

## Preserve the agreed business rules

- Accept any client name and email. Do not silently restore a test-name prefix, a single-email allowlist or forced routing to an old test recipient.
- Match returning clients only by normalized email: lowercase and trim surrounding spaces. Do not require clients to quote ClientID or a booking reference.
- Retain ClientID and calendar event IDs internally for stable identity and replay protection. A second booking has a new event ID and must not create another client row merely for that reason.
- Treat the same email as a prerequisite for an analysis booking to match the original consultation. Do not infer that two different addresses belong to the same client.
- Preserve human decisions. Do not independently decide visa eligibility, accept/sign an agreement, or mark a real meeting Completed. Simulate those statuses only on a clearly identified test record when authorized.
- Keep the agreement's existing placeholders when requested; communicate that it is a draft requiring completion before real signing. Do not turn placeholder text into invented commercial terms.
- Reuse the older full evidence form. Do not substitute the newer incomplete draft.
- Leave cancelled or explicitly excluded bookings out of recovery. Do not reprocess them simply because a restriction was removed.

## Flow 1: Consultation Booking

1. Read the Google Calendar event from the business calendar. Confirm the consultation marker and confirmed status, identify exactly one client attendee after excluding the organizer, and use the actual event description, start time and meeting link. Do not assume attendees are an array: the connector used here exposes a string. Inspect live output before parsing a new representation.
2. Get the tracker rows with pagination. Check both initial and analysis event IDs for prior processing before any insert, update or email.
3. Match the client email against the existing rows:
   - No match: insert one initial-consultation row and send the preparation email once.
   - Exactly one match with AgreementStatus Signed, ClientDecision Proceed and AnalysisMeetingStatus Not Booked: update that existing row with AnalysisMeetingStatus = Booked and ClientStatus = Analysis Meeting Booked, storing the analysis event ID, date and meeting link. Skip the initial-consultation preparation email.
   - Multiple matches or an existing client at another stage: stop without inserting or sending the preparation email. Explain the review requirement from the run evidence.
4. Preserve original consultation fields, agreement/portfolio status and unrelated row values when recording the analysis booking. Update by the connector Row ID returned by Get rows; do not use the client email as the connector Row ID.
5. Let Calendly send its standard invitation and confirmation. Do not add an analysis confirmation email unless requested.

## Flow 2: Onboarding

Use the scheduled 15-minute flow when enabled; allow one run at a time. Read the current flow status before telling the user an edit will automatically send mail.

Process only rows with an identifiable client email, ClientDecision Proceed and Eligibility Talent or Promise:

| Ready state | Action | Successful tracker update |
| --- | --- | --- |
| AgreementStatus Not Sent | Send the draft agreement | AgreementStatus Sent; ClientStatus Agreement Sent |
| AgreementStatus Signed; AnalysisMeetingStatus Not Booked; analysis invitation not already sent | Send the existing Calendly link and request the same booking email | LastAction Analysis meeting invitation sent; leave meeting status Not Booked |
| AgreementStatus Signed; AnalysisMeetingStatus Completed; PortfolioFormStatus Not Sent | Send the older evidence-form link | PortfolioFormStatus Sent; store the form URL; ClientStatus Portfolio Evidence Requested |

Preserve the claim-before-send and record-after-success sequence. A row left at LastAction Sending onboarding email needs run and mail investigation before retrying; clearing the claim blindly can send a duplicate. Do not claim transactional exactly-once delivery: Sheets and email are separate systems.

Send to the row's email in unrestricted mode. Keep duplicate protections and stage gates when removing test restrictions. Distinguish those controls from the obsolete test-only name and recipient filters.

Do not claim that the flow books a separate Calendly analysis event, signs the agreement, or automatically detects evidence submissions. Analysis self-booking reuses the consultation event; the form's Submitted status currently needs a manual update.

## Diagnose failures in this order

1. Inspect enabled status, trigger calendar, trigger conditions and run history. An enabled flow can still skip all bookings because of an obsolete trigger filter.
2. Inspect the branch expressions and configuration values. Look for test email allowlists, startsWith/name filters, TestMode and forced email routing. Removing only one restriction is insufficient.
3. Compare the event's email, title/description, status and ID with the current tracker. Use actual outputs, not a visually similar previous test.
4. Inspect the first failing action, connection account, dynamic worksheet identifier, pagination and schema error. Follow [maintenance-notes.md](references/maintenance-notes.md) for project-specific connector and browser behavior.
5. Check the sending account's run action and, when accessible, recipient-side delivery. A Gmail connection to one account does not prove delivery to a different mailbox.
6. Fix the smallest demonstrated cause, save, verify the final definition and rerun an authorized test. Separate a saved configuration, a successful simulated calendar test, and a real Calendly end-to-end test in the report.

## Test and finish

Use the exact recipient supplied for the current test. Never reuse an earlier personal test address by assumption. Treat live test mail and invitations as external actions requiring clear user authorization.

Test initial booking -> one tracker row and one preparation email; replay -> no additional row or email. Then test approved/signed onboarding -> analysis link; a second booking using the same email -> the same row becomes Booked with a separate analysis event ID and no preparation email; replay -> no changes or additional email. Finally simulate Completed on the test row -> one older evidence-form email, then replay -> no duplicate.

Prefer a real Calendly booking when the user requests end-to-end verification. If the user books it, inspect that event rather than making another. A direct calendar appointment is a partial test, not full Calendly verification. The connected Calendly Scheduling API previously required a paid plan; do not keep retrying that paid-only operation. Follow current browser confirmation rules before any action that accepts participant terms.

Make mode and enabled-state changes explicit. Do not turn a flow on for production based solely on a successful partial test. Follow the user's latest enable/disable instruction; when it is unclear, preserve the existing state and report it. Save evidence of successful actions and verify the final flow status. State what passed, what remains untested, and what the user should do next.
