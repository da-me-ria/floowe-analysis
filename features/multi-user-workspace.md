## Multi-User Workspace / Teams

### 1. User Story

As a workspace admin in Floowe, I want to create separate workspaces for different brands or clients and invite team members with specific roles (admin, editor, viewer) so that my team can collaboratively create, review, and approve content in a controlled, auditable way.

### 2. Business Case

Agencies and larger marketing teams need multi-tenant, role-based collaboration to manage many brands and stakeholders. By adding multi-user workspaces with flexible roles and approval flows, Floowe becomes suitable for agencies and mid-market customers, increases ARPU through higher-seat plans, and reduces churn by fitting into existing team structures and governance requirements.

### 3. Technical Overview

- **Components affected**
  - **User & account model**:
    - Extend from single-account to multi-workspace structure: `Account` → many `Workspace` entities.
    - Support role assignments at workspace level (`WorkspaceMember` with roles such as `OWNER`, `ADMIN`, `EDITOR`, `VIEWER`).
  - **Content model**:
    - Associate `ContentItem`, `SocialPost`, and `ContentApproval` with a `workspace_id`.
    - Ensure all content queries are scoped by current workspace for security and data isolation.
  - **Permissions / RBAC**:
    - Introduce or extend an internal RBAC module to evaluate permissions by role and workspace.
    - Enforce that only approved roles can execute commands like `ApproveContent`, `PublishContent`, or `InviteMember`.
  - **UI (React front-end)**:
    - Workspace switcher in the main navigation (e.g., dropdown listing workspaces).
    - Workspace settings screens for managing members, roles, and brand settings.
    - Content lists and dashboards filtered by active workspace.
- **New services required**
  - **Workspace Service**:
    - Manages `Workspace` aggregate and events: `WorkspaceCreated`, `WorkspaceMemberInvited`, `WorkspaceMemberJoined`, `RoleAssigned`.
    - Provides APIs for workspace CRUD, membership management, and role updates.
  - **Content Approval Service** (if not already present):
    - Manages `ContentApproval` aggregate and events: `ContentApproved`, `ContentRejected`.
    - Integrates with publishing logic so that `ContentApproved` unlocks `PublishContent` for certain roles.
- **External integrations**
  - **Email service** (SendGrid, Postmark, SES):
    - Send invitation emails for `WorkspaceMemberInvited` and notifications for approval actions.
  - **Identity / SSO (optional, future)**:
    - For larger customers, support SSO (SAML/OIDC) at workspace or account level to streamline user management.
- **Proposed tech stack (modern SaaS)**
  - **Frontend**: React + TypeScript with a design system (e.g., MUI/Chakra); use React Router for workspace-scoped routes (e.g., `/w/:workspaceId/...`) and React Query for workspace/member APIs.
  - **Backend**:
    - Node.js (TypeScript) or NestJS services:
      - `auth-service` (accounts, users, sessions),
      - `workspace-service` (workspaces, memberships, roles),
      - `content-service` (articles, posts, approvals with `workspace_id` scoping).
  - **Data storage**:
    - PostgreSQL schemas with workspace-aware tables (`workspaces`, `workspace_members`, `content_items`, `content_approvals`), indexed by `workspace_id`.
  - **Infrastructure**:
    - Event bus (Kafka, NATS, or SNS/SQS) to propagate workspace-related domain events to other services (e.g., analytics, billing).
    - Feature flags (e.g., LaunchDarkly or simple in-house flags) to roll out workspace functionality to selected customers or higher plans first.

