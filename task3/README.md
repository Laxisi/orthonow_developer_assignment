# Task 3 - Integration Design

## How I would connect everything

When a patient submits the form on the landing page, I would use **Zapier** to connect everything together and doesn't need a custom backend to be built.

Here's the order of steps:

1. Patient fills the form and clicks submit on the landing page.
2. The form data (Name, Phone, Clinic Preference) is sent to a **Zapier webhook**, which acts as the trigger.
3. Zapier then does 3 things automatically, one after another:
   - **Step A:** Creates or updates a contact in **HubSpot** using the HubSpot action inside Zapier, filling in Name, Phone, Clinic Preference, Source ("Google Ads - Consultation Landing Page"), and Lead Status ("New Enquiry").
   - **Step B:** Sends a request to **Karix's WhatsApp API** to send the confirmation message to the patient's phone number.
   - **Step C:** The Google Ads conversion is fired separately, directly from the landing page itself using the GTM dataLayer push (`consultation_form_submitted`), so it doesn't have to wait for Zapier or HubSpot to finish - this way the conversion still gets recorded even if something later in the chain is slow.

I chose Zapier over writing a direct API integration myself because as someone learning this for the first time, Zapier lets me see each step visually and test it before going live, instead of writing and debugging custom code for each connection.

## Biggest risk in this setup

I think the biggest risk is **duplicate contacts in HubSpot**. By default, HubSpot checks for duplicate contacts using email address - but this form doesn't collect email, only phone number. So if the same patient (or two different patients with the same phone, like a shared family number) submits the form more than once, HubSpot might create two separate contact records instead of recognizing it's the same person.

**My fix:** I would set up a custom property in HubSpot called something like "Phone Number" and use it as the matching field in the Zapier HubSpot step (Zapier lets you search by a custom field before deciding to create vs. update). This way, if a contact with that phone number already exists, it updates the existing record instead of creating a new one.

## Making sure the WhatsApp message goes out within 2 minutes

A few things could cause delays:
- Zapier itself can be slow if it's on a free/basic plan, since steps run in a queue rather than instantly.
- Karix's WhatsApp API could be temporarily down or rate-limited if many messages are sent at once during a busy campaign period.
- The HubSpot step could fail and block the WhatsApp step from running, if they're set up to run one after another instead of separately.

**To monitor this**, I would turn on Zapier's built-in "Zap History" and error notifications, so if any step fails, an email alert is sent immediately rather than finding out later. I would also keep the WhatsApp step independent of the HubSpot step (not depending on HubSpot finishing first), so that even if HubSpot is slow, the WhatsApp message still goes out on time.
