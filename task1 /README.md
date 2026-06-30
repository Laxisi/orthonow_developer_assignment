# Task 1 - GTM Event Schema (OrthoNow)

## Event List

| Event Name | Trigger Type | Parameters | Where it shows in GA4 |
|---|---|---|---|
| call_now_click | Click Trigger (matches "Call Now" button class) | page_url, clinic_name, button_location | Events report - tracks how many people try to call |
| whatsapp_click | Click Trigger (matches link containing wa.me) | page_url, clinic_name | Events report - WhatsApp interest |
| guide_download | Form Submission Trigger (on the gated form) | page_url, name_entered (yes/no), phone_entered (yes/no) | Events report + can mark as a Conversion |
| clinic_page_view | Page View Trigger (URL contains /clinic/) | page_url, clinic_name | Pages and Screens report |
| blog_scroll | Scroll Depth Trigger (50%, 90%) on /blog/ pages | scroll_percentage, page_url | Engagement report |
| booking_step1 | Custom Event (dataLayer push) | step_number, clinic_name, specialty | Funnel Exploration |
| booking_step2 | Custom Event (dataLayer push) | step_number, clinic_name, phone_entered (yes/no) | Funnel Exploration |
| booking_complete | Custom Event (dataLayer push) | step_number, clinic_name, booking_id | Conversions report - import to Google Ads |

## Booking Form - Tracking Drop-off Between Steps

The booking form has 3 steps, but it's all on one page (the URL doesn't change when you move between steps). This means GTM's normal triggers (like Page View or Form Submit) won't fire when someone moves from step 1 to step 2 - GTM doesn't "see" that as anything happening.

So instead, the **developer who builds the form** needs to add a small piece of code that pushes information into the `dataLayer` every time someone finishes a step. GTM is then set up to listen for that push using a **Custom Event trigger**.

Here is what that push would look like for each step:

**Step 1 (location + specialty chosen):**
```json
{
  "event": "booking_step1",
  "step_number": 1,
  "clinic_name": "OrthoNow Indiranagar",
  "specialty": "Knee Care"
}
```

**Step 2 (contact details entered):**
```json
{
  "event": "booking_step2",
  "step_number": 2,
  "clinic_name": "OrthoNow Indiranagar",
  "phone_entered": "yes"
}
```

**Step 3 (booking confirmed):**
```json
{
  "event": "booking_complete",
  "step_number": 3,
  "clinic_name": "OrthoNow Indiranagar",
  "booking_id": "12345"
}
```

In GTM, I would create one Custom Event trigger per event name (booking_step1, booking_step2, booking_complete) and attach a GA4 Event tag to each one that sends these parameters to GA4.

**To see the drop-off in GA4:** I'd go to Explore > Funnel Exploration, and add 3 steps using these 3 events in order. GA4 will then show what percentage of people who completed step 1 actually went on to complete step 2, and then step 3. Wherever the biggest drop happens, that's the step that needs fixing.

## Which Conversion to Import into Google Ads

I would import **booking_complete**, not the click events or the guide download.

Reason: Google Ads conversions are used to tell the ad campaign what a "successful" outcome looks like, so it can find more people likely to do that. A "Call Now" click or a PDF download just shows interest - it doesn't mean someone actually booked an appointment. If I imported those instead, Google Ads would start showing ads to people who are likely to click a button, not people who are likely to actually become a patient. booking_complete is the one event that means a real appointment got booked, so that's the one that should guide ad spend.
