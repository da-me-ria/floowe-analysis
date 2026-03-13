## Performance Analytics Dashboard

### 1. User Story

As a marketing manager, I want Floowe to automatically collect, aggregate, and visualize performance metrics for my articles and social posts so that I can clearly see what works, justify content investments, and adjust my strategy without manual reporting.

### 2. Business Case

Floowe’s value increases when users can attribute traffic, leads, and engagement directly to content generated and published through the platform. An automated analytics layer that syncs data from search and social channels, then surfaces it in a simple dashboard with trend lines and reports, makes ROI visible, supports upsell to higher tiers (advanced reporting), and reduces churn by embedding Floowe deeper into the customer’s decision-making process.

### 3. Technical Overview

- **Components affected**
  - `ContentItem` and `SocialPost` aggregates (must be linkable to external metrics via IDs/URLs).
  - New `ContentMetrics` aggregate backing the Performance Analytics Dashboard (`ArticleStatsUpdated`, `SocialPostMetricsSynced`, `WeeklyReportGenerated`).
  - Existing user dashboard UI extended with:
    - per-article metrics panels,
    - cross-channel summary charts,
    - automated report download/email options.
  - Notification / email subsystem for sending scheduled performance summaries.

- **New services required**
  - **Metrics Ingestion Service**:
    - Periodically calls external APIs to fetch metrics mapped to Floowe content (views, clicks, impressions, SEO ranking).
    - Emits `ArticleStatsUpdated` and `SocialPostMetricsSynced` domain events.
  - **Analytics Aggregation Service**:
    - Consumes ingestion events and materializes denormalized views into a reporting store (e.g., time-series DB or OLAP tables).
    - Generates weekly or monthly reports (`WeeklyReportGenerated`) and triggers email/notification jobs.

- **External integrations**
  - **Search/SEO**:
    - Google Search Console API for impressions, clicks, average position per URL.
    - Optional SEO tools (Ahrefs, Semrush, Moz) via their APIs for deeper ranking/backlink data.
  - **Social**:
    - Facebook Insights API, LinkedIn Analytics API, X/Twitter analytics endpoints to collect reach, engagement, and CTR.
  - **Email / notifications**:
    - Transactional email provider (SendGrid, Postmark, SES) for scheduled reports.

- **Proposed tech stack (modern SaaS)**
  - **Frontend**: React + TypeScript; charting via Recharts, Chart.js, or ECharts; data fetching via React Query against analytics APIs.
  - **Backend**:
    - Node.js (TypeScript) or NestJS for `analytics-api` (serves dashboard data to the frontend).
    - Separate `metrics-ingestor` workers (Node or Python) to call external APIs on schedules.
  - **Data storage**:
    - PostgreSQL for `ContentMetrics` aggregate and relational mappings (articles ↔ URLs ↔ external IDs).
    - Time-series or columnar store for analytics (e.g., ClickHouse or TimescaleDB) to support fast aggregations.
  - **Infrastructure**:
    - Scheduled jobs using a managed cron (e.g., Cloud Scheduler, EventBridge) or queue-based workers (BullMQ, Celery).
    - Central event bus to propagate `ArticleStatsUpdated`, `SocialPostMetricsSynced`, `WeeklyReportGenerated`.
    - Secure secrets management (Vault, AWS Secrets Manager) for API keys to analytics providers.

