## Floowe Event Storming – TO-BE Model (Proposed Enhancements)

There are proposed features with their domain events, commands, actors, and key integrations.

---

## 1. Proposed Features Overview

| # | Feature | Business Goal | Priority |
|---|---|---|---|
| F1 | **AI Content Scheduler** | Automate publication timing for maximum reach | High |
| F2 | **Performance Analytics Dashboard** | Show ROI of content; reduce churn | High |
| F3 | **Multi-User Workspace / Teams** | Enable agency use-case; increase ARPU | Medium |
| F4 | **AI Image Auto-Selection** | Reduce friction in article creation flow | Medium |
| F5 | **Integration Marketplace** | Connect CRM, email tools; expand ecosystem | Low–Medium |

---

## 2. Event Storming Table — Future State

### F1 — AI Content Scheduler

| Element | Detail |
|---|---|
| **Domain Events** | `ContentScheduled`, `ScheduledPostPublished`, `ScheduleFailed` |
| **Commands** | `SchedulePublication(article_id, channels[], datetime)`, `CancelSchedule(schedule_id)` |
| **Actor** | Content Creator |
| **Aggregate** | `Schedule` { article_id, channels, publish_at, status } |
| **Integration** | Internal Scheduler Service (cron/queue), Social APIs |
| **Policy** | If `ScheduledPostPublished` fails → emit `ScheduleFailed` → notify user via email |

---

### F2 — Performance Analytics Dashboard

| Element | Detail |
|---|---|
| **Domain Events** | `ArticleStatsUpdated`, `SocialPostMetricsSynced`, `WeeklyReportGenerated` |
| **Commands** | `FetchArticleStats(article_id)`, `SyncSocialMetrics(connection_id)`, `GenerateReport(period)` |
| **Actor** | Content Creator, AI Engine (automated) |
| **Aggregate** | `ContentMetrics` { article_id, views, clicks, social_reach, seo_rank } |
| **Integration** | Google Search Console API, Facebook Insights API, LinkedIn Analytics API |
| **Policy** | Every 24h → emit `ArticleStatsUpdated` → refresh dashboard |

---

### F3 — Multi-User Workspace / Teams

| Element | Detail |
|---|---|
| **Domain Events** | `WorkspaceCreated`, `TeamMemberInvited`, `RoleAssigned`, `ContentApproved` |
| **Commands** | `CreateWorkspace(name, owner_id)`, `InviteMember(email, role)`, `ApproveContent(article_id)` |
| **Actor** | Workspace Admin, Editor, Viewer |
| **Aggregate** | `Workspace` { id, owner, members[], subscription }, `ContentApproval` { article_id, reviewer_id, status } |
| **Integration** | Email service (invitations), Internal RBAC module |
| **Policy** | `ContentApproved` → unlock `PublishContent` command for Editor role |

---

### F4 — AI Image Auto-Selection

| Element | Detail |
|---|---|
| **Domain Events** | `ImageSuggestionsGenerated`, `ImageSelectedForArticle`, `ImageRejectedByUser` |
| **Commands** | `GenerateImageSuggestions(article_id, keywords[])`, `SelectImage(article_id, image_id)` |
| **Actor** | AI Engine, Content Creator |
| **Aggregate** | `ArticleImage` { article_id, source, url, alt_text, license_status } |
| **Integration** | Unsplash API / Pexels API, AI Image generation API (DALL·E / Stable Diffusion) |
| **Policy** | On `ArticleDrafted` → auto-trigger `GenerateImageSuggestions` |

---

### F5 — Integration Marketplace

| Element | Detail |
|---|---|
| **Domain Events** | `IntegrationConnected`, `IntegrationDisconnected`, `WebhookTriggered` |
| **Commands** | `ConnectIntegration(type, credentials)`, `DisconnectIntegration(integration_id)`, `ConfigureWebhook(url, events[])` |
| **Actor** | Content Creator, External Systems (CRM, Email tools) |
| **Aggregate** | `Integration` { id, type, credentials_ref, status, last_synced_at } |
| **Integration** | HubSpot API, Mailchimp API, Zapier Webhooks, Make (Integromat) |
| **Policy** | On `ContentPublished` → trigger registered webhooks → notify connected CRM/email tools |

---

## 3. TO-BE Actor Map

| Actor | New Capabilities |
|---|---|
| **Content Creator** | Schedule posts, view analytics, connect marketplace integrations |
| **Workspace Admin** | Manage team members, assign roles, approve content |
| **Editor** | Create/edit content, submit for approval |
| **Viewer** | Read-only access to workspace content & analytics |
| **AI Engine** | Auto-suggest images, generate performance insights, optimal publish-time recommendations |
| **External Systems** | Receive webhooks on publish events; push metrics back via API |
