## AI Content Scheduler

### 1. User Story

As a content creator, I want Floowe to automatically recommend the best publication times, channels, and supporting assets (e.g., images) for my articles so that I can maximize reach and engagement without manually planning every step of my workflow.

### 2. Business Case

Most Floowe users lack the time or expertise to continually optimize when and where content should be published. By using AI to recommend end-to-end workflows (schedule, channels, assets) and one-click apply them, Floowe reduces operational friction, increases content performance, and improves perceived product value — which directly supports upsell to higher tiers and reduces churn for busy teams.

### 3. Technical Overview

- **Components affected**
  - Existing `ContentItem` / article management (to store AI workflow recommendations attached to articles).
  - Existing scheduling logic extended from simple publish-now to `Schedule` aggregate (`ContentScheduled`, `ScheduledPostPublished`, `ScheduleFailed`).
  - Existing social and website publishing modules (must support scheduled execution and channel-specific overrides).
  - Editor UI in the React front-end (new panel for “AI workflow recommendations” with suggested schedule, channels, and image suggestions).
- **New services required**
  - **Recommendation Engine Service** (backend microservice):
    - Consumes article metadata (topic, keywords, language, target audience) and historical performance metrics (where available).
    - Produces recommended workflow objects: `{ article_id, recommended_slots[], recommended_channels[], recommended_assets[] }`.
    - Emits / listens to domain events: `ContentScheduled`, `ArticleStatsUpdated`, `ImageSuggestionsGenerated`.
  - **Scheduling Service**:
    - Manages the `Schedule` aggregate; persists schedules in a durable store.
    - Uses a job queue/cron worker to fire `ScheduledPostPublished` commands at the right time.
- **External integrations**
  - Social APIs (Facebook, LinkedIn, X) for scheduled posting (reuse existing integrations; extend to support scheduled jobs).
  - Optional use of analytics integrations (Google Analytics / Search Console, social insights APIs) to feed performance data into the Recommendation Engine.
  - Optional image providers (Unsplash, Pexels, AI image API) for recommended images associated with an AI workflow.
- **Proposed tech stack (modern SaaS)**
  - **Frontend**: React + TypeScript, component library such as MUI/Chakra; state via React Query or Redux Toolkit for scheduling and recommendation states.
  - **Backend**: Node.js (TypeScript) or NestJS-based microservices:
    - `content-service` (articles, posts),
    - `scheduler-service` (schedules, cron workers),
    - `recommendation-service` (AI & rules-based workflow suggestions).
  - **AI / data**:
    - Python-based service (e.g., FastAPI) or Node-based integration using hosted LLM APIs for time/channel recommendations.
    - Redis or PostgreSQL for storing feature flags, historical schedule performance, and recommendation cache.
  - **Infrastructure**:
    - Event bus (e.g., Kafka, NATS, or lightweight SNS/SQS) for domain events (`ContentScheduled`, `ScheduledPostPublished`, etc.).
    - Background workers (e.g., BullMQ on Redis) for scheduled jobs.
    - Deployed on a cloud provider (AWS/GCP/Azure) using managed Postgres (RDS/Cloud SQL) and container orchestration (ECS/Kubernetes).

