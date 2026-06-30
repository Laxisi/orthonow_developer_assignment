# Task 1 - GTM Event Schema (OrthoNow)

## Event List

| Event Name | Trigger Type | Parameters | Where it shows in GA4 |
|---|---|---|---|
| call_now_click | Click Trigger (matches "Call Now" button) | page_url, clinic_name, button_location | Events report - shows how many people try to call |
| whatsapp_click | Click Trigger (matches link with wa.me) | page_url, clinic_name, button_location | Events report - shows WhatsApp interest |
| guide_download | Form Submission Trigger (on the gated form) | page_url, name_entered, phone_entered | Events report, can also mark as a Conversion |
| clinic_page_view | Page View Trigger (URL has /clinic/) | page_url, clinic_name, city | Pages and Screens report |
| blog_scroll | Scroll Depth Trigger (50%, 90%) on /blog/ pages | scroll_percentage, page_url, article_title | Engagement report |
| booking_step1 | Custom Event (dataLayer push) | step_number, clinic_name, specialty | Funnel Exploration |
| booking_step2 | Custom Event (dataLayer push) | step_number, clinic_name, phone_entered | Funnel Exploration |
| booking_complete | Custom Event (dataLayer push) | step_number, clinic_name, booking_id | Conversions report - imported to Google Ads |

## Booking Form - How I would track drop-off between steps

The booking form has 3 steps, but it is all on one page - the website URL does not change when someone moves from step 1 to step 2. Because of this, GTM cannot automatically tell when someone finishes a step. GTM only notices things like a click, a page load, or a normal form submit - moving between steps inside the same page is not any of these.

So the website developer needs to add a small bit of code that sends (pushes) information to the `dataLayer` every time a step is completed. GTM is then set up to "listen" for that information using a Custom Event trigger.

Here is what I would ask the developer to send at each step:

**Step 1 (after picking clinic and specialty):**
```json
{
  "event": "booking_step1",
  "step_number": 1,
  "clinic_name": "OrthoNow Indiranagar",
  "specialty": "Knee Care"
}
```

**Step 2 (after entering contact details):**
```json
{
  "event": "booking_step2",
  "step_number": 2,
  "clinic_name": "OrthoNow Indiranagar",
  "phone_entered": "yes"
}
```

**Step 3 (after the booking is confirmed):**
```json
{
  "event": "booking_complete",
  "step_number": 3,
  "clinic_name": "OrthoNow Indiranagar",
  "booking_id": "12345"
}
```

In GTM, I would make one Custom Event trigger for each of these 3 events, and connect each one to a GA4 tag so the data goes to GA4.

**To see the drop-off in GA4:** I would go to Explore > Funnel Exploration and add these 3 events as 3 steps, in order. GA4 will then show what percentage of people who did step 1 also went on to do step 2, and then step 3. Whichever step loses the most people is the one that needs improvement.

## Which event to send to Google Ads

I would send only **booking_complete** to Google Ads, not the other events.

Reason: Google Ads uses this information to find more people who are likely to do the same thing. A click on "Call Now" or downloading a PDF just shows someone is a little interested - it does not mean they actually booked an appointment. If I sent those events instead, Google Ads would try to find people who click buttons, not people who actually become patients. booking_complete is the one event that means a real appointment was booked, so that is the one that should guide where the ad money goes.
