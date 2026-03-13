## Floowe – High-Level System Architecture

## Description

Floowe is a SaaS application built around a five-layer architecture. The **React SPA** handles all user interactions — content configuration, editing, and channel management. The **Node.js REST API** layer routes requests across four domain services: Auth, Billing, Content, and Publish. The **AI/Automation Engine** sits behind the backend and orchestrates LLM-based idea generation, article drafting, SEO optimization, and the publication queue (BullMQ). All state is persisted in **PostgreSQL**, partitioned by domain: users, articles, channel connections, and job logs. The **Integrations layer** connects outward to social APIs (Facebook Graph, LinkedIn Share, X v2), the WordPress plugin for direct site publishing, and the external LLM provider (OpenAI GPT-4).

## Layer Responsibilities

| Layer | Technology | Key responsibilities |
|---|---|---|
| Frontend | React, REST | UI, article wizard, channel OAuth, analytics display |
| Backend API | Node.js, Express | Auth, billing enforcement, content CRUD, publish routing |
| AI Engine | LLM API, BullMQ | Idea generation, drafting, SEO, async publication queue |
| Database | PostgreSQL | Users, articles, OAuth tokens, job state |
| Integrations | OAuth 2.0 / REST | Facebook, LinkedIn, X, WordPress, OpenAI |

##  Author's Perspective

The current architecture is simple and well suited for SaaS. The biggest architectural risk is the dependency on the LLM vendor. If OpenAI changes prices or rate limits, it affects the entire content generation process.
A recommendation could be to implement an abstraction service that normalizes requests from different vendors.


---
![Architecture Diagram](./im.png)