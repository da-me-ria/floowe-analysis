## Floowe Event Storming – AS-IS Model

### 🟠 Domain Events (what happened)

| # | Domain Event | Triggered By | Aggregate | Actor | System |
|---|---|---|---|---|---|
| 1 | **UserRegistered** | User submits registration form | User Account | New User | Floowe Auth / reCAPTCHA |
| 2 | **SubscriptionPlanSelected** | User chooses Free / Basic / Standard / Premium | Subscription | User | Floowe Billing |
| 3 | **ArticleIdeaListGenerated** | User configures topic + keywords | Content | User | AI Engine (LLM) |
| 4 | **ArticleDrafted** | AI generates article from selected idea | Article | AI Engine | LLM / SEO Module |
| 5 | **ContentPublished** | User confirms publish | Publication | User | CMS Plugin / Social APIs |

---

### 🔵 Commands (what triggers the event)

| Command | Event Produced | Actor |
|---|---|---|
| `RegisterUser(email, password, business_info)` | UserRegistered | New User |
| `SelectPlan(plan_id)` | SubscriptionPlanSelected | User |
| `ConfigureArticleTopic(keywords, language, tone)` | ArticleIdeaListGenerated | User |
| `SelectArticleIdea(idea_id)` | ArticleDrafted | User → AI Engine |
| `EditArticle(article_id, changes)` | ArticleEdited | User |
| `PublishContent(article_id, channels[])` | ContentPublished | User |

---

### 🟡 Aggregates (business objects)

| Aggregate | Attributes | Invariants |
|---|---|---|
| **UserAccount** | id, email, plan, business_profile | Email must be unique; plan defines article quota |
| **Subscription** | plan_type, articles_remaining, billing_cycle | Cannot exceed monthly article limit per plan |
| **Article** | id, title, body, seo_keywords, images, status | Must have at least 1 keyword; status: draft → published |
| **Publication** | article_id, channels, scheduled_at, status | Channel must be connected & authorized |
| **SocialConnection** | platform, oauth_token, account_id | Token must be valid; refreshed automatically |

---

### 👤 Actors

| Actor | Role | Capabilities |
|---|---|---|
| **New User** | Visitor registering for the first time | Register, select plan |
| **Content Creator** | Authenticated user on paid plan | Generate ideas, create/edit articles, publish |
| **Free User** | Authenticated on Free plan | Generate up to 3 articles/month, publish to all channels |
| **AI Engine** | Internal system | Generate article ideas, draft content, optimize for SEO |

---

### 🖥️ Systems & Integrations

| System | Role | Integration Type |
|---|---|---|
| **Floowe Auth** | User registration, login, session management | Internal |
| **LLM / AI Engine** | Article idea generation, content drafting, SEO optimization | Internal / External API |
| **SEO Module** | Keyword analysis, meta optimization | Internal |
| **WordPress Plugin** | Auto-publish articles to user's website | External (Plugin) |
| **Facebook API** | Auto-publish social posts | OAuth 2.0 |
| **LinkedIn API** | Auto-publish social posts | OAuth 2.0 |
| **X (Twitter) API** | Auto-publish social posts | OAuth 2.0 |
| **reCAPTCHA** | Bot protection on registration | Google API |
| **Payment Processor** | Handle subscription billing | External (assumed Stripe/PayU) |

---

### 🔴 Hotspots / Pain Points (AS-IS)

| # | Hotspot | Description |
|---|---|---|
| H1 | No content scheduling | Users must publish immediately — no future scheduling |
| H2 | No analytics dashboard | No visibility into post performance after publishing |
| H3 | Limited plan customization | Fixed article counts per plan, no add-ons |
| H4 | No team collaboration | Single-user model only |
| H5 | Manual image selection | User must manually pick graphics for each article |


