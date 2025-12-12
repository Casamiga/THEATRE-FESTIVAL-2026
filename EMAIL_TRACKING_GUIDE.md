# Email & Engagement Tracking Guide for Teatro Sin Fronteras 2026

## Overview

This guide explains the complete tracking system implemented for the Teatro Sin Fronteras 2026 festival's contest invitation campaign.

## Current System Status

The `index.html` file contains a fully functional multilingual presentation with built-in Google Analytics 4 tracking:

### Implemented Tracking Features

- **Language Selection Tracking**: Monitors when users switch between 6 languages (Spanish, English, French, German, Catalan, Portuguese)
- **User Identification**: Uses `ref` URL parameter to link email recipients to their web sessions
- **Engagement Metrics**:
  - Click tracking on all buttons and links
  - Scroll depth tracking (25%, 50%, 75%, 100%)
  - Time on page milestones (30s, 60s, 120s, 300s)
  - Session duration tracking
- **Custom Events**: All events include user_id for proper attribution

## Email Campaign Implementation

### 1. Email Template Structure

Create an HTML email with the following tracking elements:

```html
<!-- Include tracking pixel for email open tracking -->
<img src="https://your-tracking-service.com/track/open?email_id=RECIPIENT_EMAIL&campaign=festival2026"
     width="1" height="1" alt="" style="display:none;">
```

### 2. Link Parameter Format

All links in the email should include tracking parameters:

```html
<a href="https://casamiga.github.io/THEATRE-FESTIVAL-2026/?ref=RECIPIENT_EMAIL&utm_source=email&utm_medium=invitation&utm_campaign=theatre2026&utm_content=cta-button">
  VER DETALLES DEL FESTIVAL
</a>
```

### 3. Parameters Explained

- **ref**: Unique identifier (email address or ID) linking the email to the web session
- **utm_source=email**: Identifies traffic source as email
- **utm_medium=invitation**: Identifies campaign type
- **utm_campaign=theatre2026**: Campaign name for grouping
- **utm_content**: Optional - identifies which specific email variant or link

## Email Tracking Flow

### Step 1: Email Delivery (server-side)
When sending the email via Gmail, SendGrid, or Mailchimp:
- Email is sent to recipients with tracking pixel and linked URLs
- Log event: `email_sent` with recipient email and timestamp

### Step 2: Email Open
- When recipient opens email and email client loads the tracking pixel
- Pixel triggers GET request to tracking endpoint
- GA4 records: `email_opened` event with ref parameter

### Step 3: User Clicks Link
- Recipient clicks the CTA link
- Browser navigates to landing page with ref parameter
- GA4 records: `email_clicked` event

### Step 4: Landing Page Load
- Page loads with ref parameter
- GA4 User-ID feature assigns user_id from ref parameter
- Records: `page_view` event

### Step 5: User Engagement
- GA4 automatically tracks:
  - `language_change` when user switches language
  - `click_element` when user clicks buttons/links
  - `scroll_depth` at various thresholds
  - `time_milestone` at time intervals

### Step 6: Session End
- When user closes tab/navigates away
- Records: `session_end` event with total_duration

## Google Analytics 4 Configuration

### Required Setup Steps

1. **Create Custom Events** in GA4:
   - `email_sent`
   - `email_opened`
   - `email_clicked`
   - `language_change`
   - `click_element`
   - `scroll_depth`
   - `time_milestone`
   - `session_end`

2. **Enable User-ID Feature**:
   - Go to GA4 Property Settings
   - Enable "User-ID" feature
   - Create audiences based on user_id
   - Link events to users across devices/sessions

3. **Replace Placeholder GA4 ID**:
   - Current code has: `G-XXXXXXXXXX`
   - Replace with your actual GA4 Property ID (format: G-XXXXXXXXXX)
   - Location in index.html: Line 980

## Email Service Integration

### Option 1: Google Gmail
- Compose HTML email with tracking pixel
- Manually replace RECIPIENT_EMAIL placeholders
- Note: Gmail may not load external tracking pixels

### Option 2: Mailchimp
- Built-in tracking pixel support
- URL parameters automatically preserved
- Can use Mailchimp's merge tags for personalization

### Option 3: SendGrid
- Create transactional email template
- Include tracking pixel
- Use Personalizations for recipient-specific data

### Option 4: Custom Server Script
```javascript
// Server-side event tracking
function sendEmail(recipientEmail, recipientName) {
  // Send email with tracking pixel
  const trackingPixel = `https://www.google-analytics.com/collect?v=1&tid=GA_ID&cid=${recipientEmail}&t=pageview&ec=email&ea=sent&el=festival2026`;
  
  // Record email_sent event
  recordGA4Event('email_sent', {
    email: recipientEmail,
    name: recipientName,
    campaign: 'theatre2026'
  });
}
```

## Monitoring & Reporting

### GA4 Dashboard Setup

1. **Email Performance Report**:
   - Filter by utm_source=email
   - Segment by language
   - Track conversion rate from email to form submission

2. **User Journey Report**:
   - Event flow: email_sent → email_opened → email_clicked → page_view
   - Identify drop-off points
   - Analyze engagement by language

3. **Funnel Analysis**:
   - Email sent → Email opened → Clicked link → Viewed 50% content → Session duration >2min
   - Identify most engaging language
   - Track conversion to registration

## Testing

### Test Email Campaign

1. Send test email to yourself with tracking parameters
2. Click the link and verify page loads with ref parameter
3. Check GA4 in real-time:
   - Go to Realtime → Events
   - Look for `page_view` event with user_id matching ref parameter
   - Verify all engagement events are firing

### Validation Checklist
- [ ] Tracking pixel loads when email is opened (test with Litmus or Email on Acid)
- [ ] utm_source=email appears in GA4 traffic source reports
- [ ] User-ID matches ref parameter in GA4
- [ ] Language change events record correctly
- [ ] Time and scroll milestones trigger appropriately
- [ ] Session end event includes duration

## Troubleshooting

### Email Open Tracking Not Working
- Some email clients block images by default
- Consider using GA4 Measurement Protocol for server-side tracking
- Alternative: Track opens through link clicks instead

### User ID Not Appearing in GA4
- Verify User-ID feature is enabled in GA4
- Check that ref parameter is being passed in URL
- Ensure GA4 config includes user_id: userId setting (line 1033 in index.html)

### Language Change Events Not Recorded
- Verify localStorage is enabled in browser
- Check that setLanguage() function is being called
- Confirm gtag() is properly initialized

## Next Steps

1. Integrate with email service of choice
2. Create email template with tracking pixel
3. Set up GA4 custom events
4. Enable User-ID feature
5. Replace G-XXXXXXXXXX with actual GA4 ID
6. Create dashboard and reports in GA4
7. Set up alerts for key metrics
8. Test complete workflow before sending to full list

## Contact & Support

For questions about the tracking implementation, review the code comments in index.html or contact the development team.

---

Last Updated: 2024
Campaign: Teatro Sin Fronteras 2026
