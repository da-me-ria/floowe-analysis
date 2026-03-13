## Note
Since the site registration does not work, the analysis is based on the page content

## 1. Registration and login

- **Actor**: New marketer / business owner / agency user  
- **Goal**: Create a Floowe account and access the dashboard to start generating content.

### Step-by-step flow
1. User lands on the marketing site and clicks `Test for free` or `Register`.
2. System displays registration form (email, company name, phone, password) or Google/Apple registration button.
3. User fills in required fields and accepts terms/privacy.
4. User submits the form.
5. System validates input (format, password strength, uniqueness of email).
6. System creates the user account and stores profile and business data.
7. (Optional) System sends verification email with confirmation link.
8. User clicks confirmation link (if required).
9. System signs the user in and redirects to the onboarding/setup wizard or main dashboard.
10. On subsequent visits, user selects `Sign in`, enters credentials, and is authenticated into the app.

### System interactions
- Create user record and hashed credentials in the user database.
- Store business metadata (industry, target audience, default language) for personalization.
- Trigger email service to send verification and welcome email.
- Generate authentication token / session and set secure cookies.
- Log security events (registration, login, failed login attempts).

### Possible edge cases
- Email already registered (system rejects with clear error and suggests login or password reset).
- Weak or invalid password (system prompts for stronger password).
- Verification link expired or already used (system offers to resend).
- Incorrect login credentials (rate-limited retries and clear feedback).
- Login from unusual device or location (optional additional verification).

---

## 2. Onboarding and external integrations setup

- **Actor**: Newly registered user   
- **Goal**: Configure Floowe with business data and connect website and social media channels for automatic publishing.

### Step-by-step flow
1. After first login, system displays onboarding wizard.
2. User provides additional business info (website URL, main topics, target audience, preferred tone, languages).
3. System suggests installing a website plugin or provides integration code for CMS (e.g., WordPress).
4. User follows instructions to install and authorize the plugin on their site.
5. Back in Floowe, user connects social media accounts (Facebook, LinkedIn, X) via `Connect` buttons.
6. For each platform, user is redirected to the platform’s OAuth consent screen and approves access.
7. System confirms successful connections and shows a summary of connected channels.
8. User finishes onboarding and reaches the main dashboard with integrations active.

### System interactions
- Save business profile, topics, target audience, and localization preferences.
- Generate plugin/API keys or integration tokens for website CMS.
- Handle OAuth flows and securely store access tokens/refresh tokens for social platforms.
- Test connectivity (e.g., send a test request) and surface status indicators for each integration.
- Update user’s account state to reflect connected channels and onboarding completion.

### Possible edge cases
- User skips plugin installation (system allows later setup and clearly indicates limited capabilities).
- CMS plugin not installed correctly or cannot reach Floowe API (system shows integration error and troubleshooting tips).
- Social platform denies scopes or user cancels OAuth (system marks platform as disconnected and allows retry).
- Revoked or expired tokens from social networks (system detects failures, prompts re-authorization).
- Mismatch between configured website URL and plugin configuration (system flags potential misconfiguration).

---

## 3. Content ideation and article generation

- **Actor**: Logged-in content creator / marketer  
- **Goal**: Generate SEO-optimized article ideas and full articles tailored to their business and audience.

### Step-by-step flow
1. User opens the `Article` section in the dashboard.
2. User selects a main topic, goal (e.g., increase SEO for a keyword), and desired language.
3. User either enters keywords manually or chooses to let Floowe analyze popular content in their niche.
4. System analyzes relevant content and key phrases and returns a list of article ideas/titles.
5. User reviews suggestions, selects one or more ideas, and requests article generation.
6. System generates article drafts that mimic an experienced copywriter and embeds SEO-focused keywords.
7. User opens a generated draft in the editor for review.
8. User optionally edits title, headings, body text, and structure.
9. User saves the article as a draft or marks it as ready for publication.

### System interactions
- Retrieve user’s business profile, target audience, and language settings to guide generation.
- Call internal or external AI services to analyze popular content and generate ideas and drafts.
- Store generated ideas and article drafts, linked to the user’s account.
- Apply keyword and SEO optimization heuristics (e.g., density, placement).
- Track article status (idea, draft, ready to publish, published).

### Possible edge cases
- Insufficient or overly broad input from the user (system prompts for more details or suggests defaults).
- AI fails to generate content (retry, fall back to simpler template, or surface helpful error).
- Generated article too long/short vs. user’s expectations (user adjusts target length and regenerates sections).
- Repeated or low-quality ideas (system offers regeneration or alternative suggestions).
- Multi-language inconsistencies (system warns if requested language differs from profile defaults).

---

## 4. Editing, social post creation, and multi-channel publishing

- **Actor**: Logged-in user with content ready for publication  
- **Goal**: Finalize article content, generate social posts from it, and publish across website and social channels.

### Step-by-step flow
1. User opens a `Ready` or `Draft` article in the Word-like editor.
2. User refines copy (tone, structure, CTAs), and selects or uploads article graphics.
3. User clicks `Generate social posts` for connected channels.
4. System generates channel-specific posts (e.g., different lengths for Facebook, LinkedIn, X) based on the article.
5. User reviews and, if needed, edits each social post.
6. User selects publication timing (immediate or scheduled) and target channels (website, Facebook, LinkedIn, X).
7. User confirms publishing.
8. System publishes the article to the website (via plugin/API) and posts to selected social channels.
9. System updates article status to `Published` and logs publishing results.

### System interactions
- Load and save article content in the editor, including graphics metadata.
- Generate social post variants from article content via AI or templating.
- Call website plugin or CMS API to create/update posts on the user’s site.
- Use social platform APIs (via stored OAuth tokens) to publish posts or create scheduled posts.
- Log publishing outcomes (success/failure) and timestamps, and surface them in activity/history views.

### Possible edge cases
- Website plugin offline or misconfigured (system fails to publish to site, marks as failed, and notifies user).
- Social API rate limits or temporary outages (system queues or retries and shows partial success status).
- Image upload failures or unsupported formats (system rejects file with clear reason and alternatives).
- User schedules posts in the past or conflicting times (system validates dates and prompts corrections).
- Inconsistent content across languages/channels (system highlights key differences and asks for confirmation).

---

## 5. Subscription upgrade and payment flow

- **Actor**: Logged-in account owner on Free or lower-tier plan  
- **Goal**: Upgrade to a paid subscription (e.g., Basic, Standard, Premium) to unlock higher article limits and advanced features.

### Step-by-step flow
1. User reaches article or monthly usage limit and sees an `Upgrade` prompt, or navigates to the `Pricing` section.
2. System displays available plans (Free, Basic, Standard, Premium) with prices (e.g., 150 €, 200 €, 250 €) and feature differences.
3. User selects a desired plan and clicks `Upgrade`.
4. System opens a secure payment form (embedded or via payment provider) requesting billing details (card info, company data, VAT).
5. User enters required billing data and confirms subscription.
6. Payment provider validates and processes the transaction (including any 3D Secure flows).
7. On success, system updates the user’s plan and entitlements (article quotas, features).
8. System generates and stores an invoice/receipt and confirms upgrade to the user (on-screen and via email).
9. User immediately gains access to higher limits and any additional features.

### System interactions
- Read current plan, usage, and feature flags to show relevant upgrade options.
- Initiate payment session with external payment gateway (e.g., Stripe, Paddle).
- Handle payment webhooks (success, failure, subscription renewal, cancellation).
- Update subscription records, quotas, and feature toggles in the billing system and user profile.
- Generate invoices and store them for later retrieval; trigger email notifications.

### Possible edge cases
- Card declined or payment failure (system provides clear error, allows retry or alternate method).
- 3D Secure or SCA flow not completed (session times out and payment is marked as failed).
- User closes window during checkout (subscription not upgraded; system returns user to billing screen with current status).
- Currency or tax/VAT calculation issues (system blocks completion and asks user to correct billing country/VAT ID).
- Downgrade or cancellation scenarios (system ensures proration or end-of-term rules are clear and applied correctly).

## Author's perspective

The registration process looks standard. It's unclear whether there is a tutorial after registration or if the user is taken directly to the interface. 