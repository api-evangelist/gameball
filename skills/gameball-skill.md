---
name: Gameball
description: Use when building loyalty programs, gamification features, referral systems, or reward campaigns. Reach for Gameball when you need to integrate customer engagement, points tracking, redemption flows, or automated marketing workflows into e-commerce platforms, mobile apps, or POS systems.
metadata:
    mintlify-proj: gameball
    version: "1.0"
---

# Gameball Skill Reference

## Product Summary

Gameball is a customer loyalty and gamification platform that enables you to build loyalty programs, manage points and rewards, run referral campaigns, and automate customer engagement workflows. Use the REST API (v4.0) to integrate loyalty features into your backend, the JavaScript widget to display customer profiles on web, or native SDKs for mobile (iOS, Android, React Native, Flutter). Key files and endpoints: API base URL is `https://api.gameball.co/api/v4.0`. Retrieve API Key and Secret Key from **Settings > Admin Settings > Account Integration** in the Gameball Dashboard. Primary documentation: https://docs.gameball.co

---

## When to Use

Reach for Gameball when:

- **Building loyalty programs**: Set up points earning/redemption, cashback, or tier-based rewards
- **Integrating order tracking**: Process purchases and award points automatically via the Order API
- **Implementing referral programs**: Track referrals and reward both referrer and new customer
- **Creating reward campaigns**: Launch gamified campaigns (spin-to-win, scratch cards, missions, streaks)
- **Automating customer journeys**: Build workflows triggered by events, purchases, or time-based conditions
- **Tracking customer events**: Monitor non-purchase actions (visits, category views, social shares) to trigger rewards
- **Enabling point redemption**: Let customers redeem points for discounts or coupons at checkout
- **Segmenting customers**: Use RFM analysis or custom segments to target specific customer groups
- **Sending targeted communications**: Trigger emails, push notifications, or SMS based on loyalty events

Do NOT use Gameball for: general email marketing (use Klaviyo/Mailchimp), payment processing (use Stripe/PayPal), or analytics-only needs (use Mixpanel).

---

## Quick Reference

### API Authentication

| Header | Value | Required For |
|--------|-------|--------------|
| `APIKey` | Your API key from dashboard | All requests |
| `SecretKey` | Your secret key from dashboard | Sensitive operations (transactions, redemptions) |

**High Security Mode**: When enabled, all endpoints require both APIKey and SecretKey. Enable in **Settings > Admin Settings > Account Integration**.

### Core API Endpoints

| Endpoint | Purpose | Method |
|----------|---------|--------|
| `/customers` | Create/update customer profiles | POST/GET |
| `/order` | Track orders and award points | POST |
| `/integrations/event` | Track customer events | POST |
| `/transactions/redeem` | Redeem points for discounts | POST |
| `/transactions/hold` | Reserve points before redemption | POST |
| `/coupons/generate` | Generate promotional coupons | POST |
| `/coupons/validate` | Validate coupon codes | POST |
| `/customers/{id}/balance` | Get customer points balance | GET |
| `/configurations/campaigns` | List active reward campaigns | GET |

### Key Configuration Files

- **API Keys**: Dashboard → Settings → Admin Settings → Account Integration
- **Reward Campaigns**: Dashboard → Campaigns → Rewards
- **Automation Workflows**: Dashboard → Automation
- **Customer Segments**: Dashboard → Segments
- **Widget Settings**: Dashboard → Settings → Widget Configuration

### SDK Initialization (Web v3)

```javascript
// Load widget on page
<script src="https://widget.gameball.co/v3/gameball.js"></script>

// Initialize with customer session token
Gameball.init({
  apiKey: 'YOUR_API_KEY',
  customerId: 'customer_id',
  sessionToken: 'session_token_from_backend'
});

// Show widget
Gameball.showProfile();
```

### Common API Patterns

**Create/Update Customer**:
```json
POST /customers
{
  "customerId": "user_123",
  "email": "user@example.com",
  "mobile": "+1234567890",
  "attributes": {
    "firstName": "John",
    "lastName": "Doe"
  }
}
```

**Track Order**:
```json
POST /order
{
  "customerId": "user_123",
  "orderId": "ORD12345",
  "totalPaid": 100.50,
  "lineItems": [
    {
      "productId": "PROD123",
      "title": "Product Name",
      "price": 100.50,
      "quantity": 1,
      "category": ["Electronics"]
    }
  ]
}
```

**Track Event**:
```json
POST /integrations/event
{
  "customerId": "user_123",
  "eventName": "product_viewed",
  "eventData": {
    "productId": "PROD123",
    "category": "Electronics"
  }
}
```

---

## Decision Guidance

### When to Use API vs Widget

| Scenario | Use API | Use Widget |
|----------|---------|-----------|
| Server-side order tracking | ✓ | |
| Displaying customer profile | | ✓ |
| Redeeming points at checkout | ✓ | |
| Showing points balance on product page | | ✓ |
| Tracking events from backend | ✓ | |
| Referral link generation | ✓ | ✓ |
| Mobile app integration | ✓ | |
| Web integration | ✓ | ✓ |

### When to Use Reward Campaigns vs Automation

| Need | Use Reward Campaigns | Use Automation |
|------|---------------------|----------------|
| Reward on specific purchase (e.g., first order) | ✓ | |
| Gamified experience (spin wheel, scratch card) | ✓ | |
| Multi-step workflow with delays | | ✓ |
| Send email + add points + add tag | | ✓ |
| Time-based rewards (birthday, anniversary) | ✓ | |
| Conditional logic (if/then branches) | | ✓ |
| Simple point multiplier | ✓ | |

### When to Use Points vs Coupons

| Scenario | Points | Coupons |
|----------|--------|---------|
| Flexible redemption value | ✓ | |
| Fixed discount amount | | ✓ |
| Expiry enforcement | ✓ | ✓ |
| Partial redemption | ✓ | |
| One-time use | | ✓ |
| Transferable rewards | | ✓ |

---

## Workflow

### Typical Integration Flow

**Step 1: Set Up Authentication**
- Retrieve API Key and Secret Key from Dashboard → Settings → Admin Settings → Account Integration
- Store keys securely on your backend (never expose client-side)
- Enable High Security Mode if required by your security policy

**Step 2: Create Customer Profiles**
- Call `POST /customers` when users sign up or log in
- Include `customerId` (unique identifier), email, and optional attributes
- Retrieve session token from response for widget initialization
- Store session token securely for widget authentication

**Step 3: Track Orders**
- After successful payment, call `POST /order` with order details
- Include `customerId`, `orderId`, `totalPaid`, and `lineItems`
- Gameball automatically evaluates campaigns and awards points
- Retrieve updated balance with `GET /customers/{id}/balance`

**Step 4: Track Events (Optional)**
- For non-purchase actions, call `POST /integrations/event`
- Include `customerId`, `eventName`, and relevant `eventData`
- Use for product views, category browsing, social shares, etc.
- Events trigger campaigns and automation workflows

**Step 5: Enable Redemption**
- Call `POST /transactions/hold` to reserve points before checkout
- Include `customerId` and `pointsToHold`
- Store hold reference for order submission
- Call `POST /order` with hold reference to complete redemption
- Call `POST /transactions/release` if transaction is cancelled

**Step 6: Configure Campaigns & Automation**
- Create reward campaigns in Dashboard → Campaigns → Rewards
- Set up automation workflows in Dashboard → Automation
- Test in test environment before going live
- Monitor campaign performance in Dashboard → Analytics

**Step 7: Verify Before Launch**
- Test customer signup and profile creation
- Test order tracking and point awards
- Test redemption flow (hold → order → deduction)
- Test event tracking and campaign triggers
- Verify error handling for network failures and invalid data
- Check widget displays correctly on all pages
- Validate API response times and error rates

---

## Common Gotchas

**Authentication & Security**
- Never expose Secret Key in client-side code or logs. Always send sensitive requests from backend.
- API Key alone is insufficient for transaction endpoints; use both APIKey and SecretKey headers.
- High Security Mode requires both keys for all endpoints; verify it's enabled before going live.
- Session tokens expire; regenerate them on each login or session refresh.

**Order Tracking**
- `orderId` must be unique per order; reusing IDs causes duplicate processing.
- `totalPaid` should reflect the final amount after discounts/redemptions, not the original price.
- Order dates must be ISO 8601 format (`2024-10-16T08:13:29.290Z`).
- Points are awarded based on `totalPaid`, not `totalPrice`; exclude taxes/shipping if configured.
- Orders submitted twice with same `orderId` are rejected; implement idempotency on your side.

**Points & Redemption**
- Hold points expire after a configurable period (default varies); release holds if transaction is cancelled.
- Redemption requires sufficient balance; check balance before showing redemption options.
- Points are deducted only when order is submitted with hold reference; holding alone doesn't deduct.
- Refunds reverse points; implement refund tracking to adjust balances correctly.
- Points earned from multiplier campaigns are delayed until return window passes (if configured).

**Campaigns & Events**
- Campaigns must be activated in dashboard to trigger; inactive campaigns don't award rewards.
- Event names are case-sensitive; use exact names configured in dashboard.
- Campaign evaluation happens asynchronously; rewards may take seconds to appear.
- Gamification campaigns (spin wheel, scratch card) require widget integration; API-only won't display them.
- RFM segments refresh once daily; real-time segmentation uses custom segments instead.

**Widget Integration**
- Widget requires valid session token; expired tokens cause widget to fail silently.
- Widget loads asynchronously; don't assume it's ready immediately after page load.
- Widget language must match your site language; configure in widget settings.
- Guest widget shows limited features; authenticate users to unlock full functionality.
- Widget blocks page rendering if loaded synchronously; always load asynchronously.

**Data & Validation**
- Customer IDs must be consistent across all API calls; changing IDs creates duplicate profiles.
- Email and mobile are optional but recommended for communication campaigns.
- Custom attributes must be defined in dashboard before sending; undefined attributes are ignored.
- Batch APIs have size limits (typically 1000 records per request); split large imports.
- Duplicate customer creation with same email/mobile merges profiles; use unique identifiers.

---

## Verification Checklist

Before submitting your integration:

- [ ] API credentials are stored securely on backend, never exposed client-side
- [ ] All API calls use HTTPS and include correct authentication headers
- [ ] Customer profiles are created on signup with unique, persistent IDs
- [ ] Orders are tracked after successful payment with unique order IDs
- [ ] Points are awarded correctly based on order value and campaign configuration
- [ ] Redemption flow works: hold → order submission → point deduction
- [ ] Hold points are released if transaction is cancelled
- [ ] Events are tracked for key actions and trigger campaigns correctly
- [ ] Widget displays correctly for both guest and authenticated users
- [ ] Widget language matches your site language
- [ ] Error handling is implemented for network failures and invalid data
- [ ] Duplicate order IDs are rejected appropriately
- [ ] Insufficient balance errors are displayed clearly to users
- [ ] API response times are acceptable (< 500ms typical)
- [ ] All test scenarios pass (new customer, purchase, redemption, referral)
- [ ] Campaigns are activated in dashboard before going live
- [ ] Automation workflows are tested and active
- [ ] Customer data is handled according to privacy regulations
- [ ] Post-launch monitoring is configured (error rates, API latency, success rates)

---

## Resources

**Comprehensive Navigation**: https://docs.gameball.co/llms.txt — Full page-by-page listing for agent navigation

**Critical Documentation**:
1. [API Reference Introduction](https://docs.gameball.co/api-reference/introduction) — Complete endpoint documentation and authentication details
2. [Web SDK Integration Guide](https://docs.gameball.co/installation-guides/v3/web/index) — Step-by-step web integration with REST APIs and widget
3. [Tutorials: Order Handling](https://docs.gameball.co/tutorials/experiences/order-handling/submit-order) — Real-world examples of order tracking and point earning

**Additional Resources**:
- [Gameball Dashboard](https://dashboard.gameball.co) — Configure campaigns, automation, and settings
- [API Status & Errors](https://docs.gameball.co/api-reference/overview/status-error-codes) — Troubleshoot API issues
- [Go-Live Checklist](https://docs.gameball.co/installation-guides/v3/web/go-live-checklist) — Pre-launch verification steps

---

> For additional documentation and navigation, see: https://docs.gameball.co/llms.txt