# Project map

Use these identifiers to locate the existing project; verify access, metadata and current contents before editing. Do not create duplicates if an item is temporarily inaccessible.

## Accounts and applications

- Business Google/Calendly account: globalbridgemusicartandculture@gmail.com.
- Last verified Outlook sender: Mo.Ogunmokun@climate-kic.org; Reply-To business Gmail. Inspect the live connection before assuming it is still the sender.
- Existing event: Global Talent Visa Consultation, 45 minutes, Google Meet; booking URL https://calendly.com/globalbridgemusicartandculture/consultation . Payment collection is disabled. The event page retained initial-consultation pricing wording; reusing the link does not remove that wording.
- Power Automate environment: Default-28818939-0afb-44b2-b6c2-8eb0a57cca64.
- Flow 1: Client - Consultation Booking; ID 5d9aff7e-f470-4c06-9a61-327b74121e90.
- Flow 2: Client - Onboarding; ID e6cb955a-da3e-4a75-8b11-ae18feec9aea.

## Tracker

Edit the business-owned **Client Consultation & Onboarding Tracker - Global Bridge**:
https://docs.google.com/spreadsheets/d/1btS1ACeKsFo2NWT_LflH2qFVDFp4jZ4z8qv0iBlyctA/edit

Worksheet Clients; numeric worksheet ID 2002801046. Preserve the earlier personal-owned original spreadsheet (ID 1iW416rMxjeewmI9-AVsHXzBqZtGI8qnrcMciMK4XxCI); it is not the flows' active destination.

| Columns | Headers |
| --- | --- |
| A:E | ClientID, FullName, Email, CalendlyBookingID, ConsultationDate |
| F:J | PaymentStatus, Eligibility, ClientDecision, AgreementStatus, AnalysisMeetingStatus |
| K:O | PortfolioFormStatus, PortfolioFormLink, LastAction, LastActionDate, ClientStatus |
| P | __PowerAppsId__ (connector metadata; preserve) |
| Q:S | AnalysisBookingID, AnalysisMeetingDate, AnalysisMeetingLink |

Inspect live validation before writing status values. The configured choices were:
- Eligibility: Talent, Promise, Not Eligible, Pending.
- ClientDecision: Proceed, Declined, Pending.
- AgreementStatus: Not Sent, Sent, Signed.
- AnalysisMeetingStatus: Not Booked, Booked, Completed.
- PortfolioFormStatus: Not Sent, Sent, Submitted.
- PaymentStatus for new bookings: Not Required.

CalendlyBookingID currently stores the initial Google Calendar event ID, not the actual Calendly booking ID. Preserve that distinction in explanations and future migrations.

## Agreement and evidence form

Agreement: **Global Bridge Portfolio Services Agreement Template**
https://docs.google.com/document/d/1xijpIs65UMIwog7E2pd0AnKKNejk7hi8EizQyBKHrPA

The user accepted placeholders for now and explicitly approved edit access for the business Google account. Do not assume that approval authorizes broader sharing. Flow 2 sends an inline static text snapshot held in Agreement_text. Updating the Google Doc alone does not refresh that snapshot.

Older form: **GBMAC - UK Global Talent, Full Evidence & Career Information**
https://docs.google.com/forms/d/e/1FAIpQLSenJs9tfa1qNT5j35633FxW1BM-C0MXM3GDNUfq9TaunjMF4g/viewform

The form has multiple sections and file-upload questions, which require Google sign-in. Preserve existing responses. Use matching name/email and manual Submitted status; no automatic submission matching was implemented. Do not use the newer short draft called Global Bridge - Portfolio Evidence Form.

## Continuity checkpoint (last verified/reported 29 October 2026)

Treat this checkpoint as historical and re-read live state:
- Flow 1 was On; Flow 2 was Off under the user's earlier configuration/testing instruction.
- Test-only name and email restrictions were removed from both flows at the user's explicit request. Flow 2 settings included TestMode false and ClientEmailsEnabled true; the old TestRecipient value was unused.
- Flow 1 gained Get_an_event to fetch the current trigger event, and downstream actions used its body. No historical booking recovery override was retained.
- The user cancelled two skipped bookings and explicitly excluded them from recovery.
- The user subsequently rebooked and reported the new booking appeared in Sheets. Full onboarding with that new row was not yet verified.
- Earlier calendar-trigger tests passed analysis matching, preservation of the original consultation, new-client insertion/preparation email, and duplicate replays. Earlier onboarding tests delivered agreement, analysis-link and older-form emails to a separate test mailbox. Do not present those as a complete production end-to-end run.
