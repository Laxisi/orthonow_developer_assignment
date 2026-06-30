# Task 3 - Integration Design

## How I would connect everything

When a patient fills the form on the landing page, I would use a tool called **Zapier** to connect everything. Zapier lets you connect different apps without writing a lot of code, making it easier than building everything from scratch.

Here is the order in which things would happen:

1. The patient fills the form and clicks submit.
2. The form sends the details (Name, Phone, Clinic Preference) to Zapier.
3. Zapier then does three things automatically:
   - It creates a new contact in **HubSpot** (or updates one if it already exists) with the Name, Phone, Clinic Preference, Source ("Google Ads - Consultation Landing Page"), and Lead Status ("New Enquiry").
   - It sends a message through **Karix's WhatsApp API** to confirm the patient's enquiry.
   - At the same time, the website itself sends the conversion event to Google Ads directly (using the dataLayer push we set up in Task 1). This way, even if Zapier or HubSpot is slow, the Google Ads conversion still gets recorded on time.

## What could go wrong

The biggest problem I can think of is **duplicate contacts in HubSpot**. Normally, HubSpot checks if a contact already exists by looking at their email address. But this form does not collect email, only phone number. So if the same patient fills the form twice, or if two different patients share the same phone number (like a family phone), HubSpot might create two separate contacts instead of recognizing it is the same person.

**How I would fix it:** I would set up phone number as a custom field in HubSpot, and tell Zapier to check that field first. If a contact with that phone number already exists, Zapier updates that contact instead of making a new one.

## Making sure the WhatsApp message goes out on time

The message needs to reach the patient within 2 minutes. A few things could cause delay:
- Zapier might be slow if too many people fill the form at the same time.
- Karix's WhatsApp service could be down or busy.
- If the WhatsApp step depends on the HubSpot step finishing first, and HubSpot is slow, the WhatsApp message gets delayed too.

**How I would keep track of this:** I would turn on Zapier's error alerts, so if any step fails I get an email about it right away instead of finding out later. I would also make sure the WhatsApp message does not wait for HubSpot to finish first - they should both happen at the same time, so one being slow does not delay the other.
