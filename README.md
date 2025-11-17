# buffer_clone

erDiagram

%% ===========================
%% USERS / ORGS / MEMBERSHIPS
%% ===========================

    USERS {
        string id PK
        string name
        string email
        string password_hash
        string role
        json   preferences
    }

    ORGANIZATIONS {
        string id PK
        string name
        string owner_id FK
        datetime created_at
    }

    MEMBERSHIPS {
        string id PK
        string org_id FK
        string user_id FK
        string role
    }

    USERS ||--o{ MEMBERSHIPS : "has many"
    ORGANIZATIONS ||--o{ MEMBERSHIPS : "has many"
    USERS ||--o{ ORGANIZATIONS : "owns"


%% ===========================
%% INTEGRATIONS / SOCIAL ACCOUNTS
%% ===========================

    INTEGRATIONS {
        string id PK
        string org_id FK
        string platform
        string access_token_enc
        string refresh_token_enc
        datetime expires_at
        json meta
    }

    SOCIAL_ACCOUNTS {
        string id PK
        string integration_id FK
        string platform_user_id
        string username
        json meta
    }

    ORGANIZATIONS ||--o{ INTEGRATIONS : "connects"
    INTEGRATIONS ||--o{ SOCIAL_ACCOUNTS : "provides"


%% ===========================
%% COMMENTS INBOX MODULE
%% ===========================

    COMMENTS {
        string id PK
        string org_id FK
        string social_account_id FK
        string platform
        string platform_comment_id
        string post_id
        string post_link
        json   author
        string text
        json   media
        datetime timestamp
        string status
        string assigned_to FK
        json tags
        json ai_insights
        json platform_meta
    }

    REPLIES {
        string id PK
        string comment_id FK
        string author_user_id FK
        string text
        string platform_reply_id
        datetime timestamp
        string status
    }

    SAVED_REPLIES {
        string id PK
        string org_id FK
        string owner_id FK
        string title
        string body
        json placeholders
    }

    AUDIT_LOGS {
        string id PK
        string org_id FK
        string user_id FK
        string action
        string entity_type
        string entity_id
        datetime timestamp
        json meta
    }

    ORGANIZATIONS ||--o{ COMMENTS : "has comments"
    SOCIAL_ACCOUNTS ||--o{ COMMENTS : "produces"
    USERS ||--o{ COMMENTS : "assigned to"
    COMMENTS ||--o{ REPLIES : "has replies"
    USERS ||--o{ REPLIES : "writes"
    ORGANIZATIONS ||--o{ SAVED_REPLIES : "owns"
    USERS ||--o{ SAVED_REPLIES : "created by"
    ORGANIZATIONS ||--o{ AUDIT_LOGS : "logs"
    USERS ||--o{ AUDIT_LOGS : "performed by"


%% ===========================
%% BUFFER PUBLISH MODULE
%% ===========================

    POSTS {
        string id PK
        string org_id FK
        string social_account_id FK
        string caption
        json media_urls
        string status
        datetime scheduled_at
        datetime published_at
        string created_by FK
        string assigned_to FK
        json tags
        json approval_data
        datetime created_at
        datetime updated_at
    }

    POST_APPROVALS {
        string id PK
        string post_id FK
        string approver_id FK
        string status
        string comments
        datetime timestamp
    }

    SCHEDULE_JOBS {
        string id PK
        string post_id FK
        datetime run_at
        int retry_count
        string status
        string last_error
    }

    ORGANIZATIONS ||--o{ POSTS : "creates"
    SOCIAL_ACCOUNTS ||--o{ POSTS : "posted to"
    USERS ||--o{ POSTS : "created by"
    POSTS ||--o{ POST_APPROVALS : "has approvals"
    POSTS ||--o{ SCHEDULE_JOBS : "scheduled"


%% ===========================
%% BUFFER ANALYZE + PAYWALL
%% ===========================

    SUBSCRIPTIONS {
        string id PK
        string user_id FK
        string plan
        boolean active
        datetime expires_on
        json feature_set
        datetime updated_at
    }

    ANALYTICS {
        string id PK
        string org_id FK
        string platform
        date date
        int impressions
        int reach
        float engagement_rate
        int followers
        json meta
    }

    USERS ||--o{ SUBSCRIPTIONS : "has subscription"
    ORGANIZATIONS ||--o{ ANALYTICS : "stores analytics"


%% ===========================
%% START PAGE MODULE
%% ===========================

    START_PAGES {
        string id PK
        string user_id FK
        string title
        string slug
        string theme
        json branding
        string avatar_url
        json settings
        datetime created_at
        datetime published_at
    }

    START_PAGE_LINKS {
        string id PK
        string page_id FK
        string label
        string url
        int position
    }

    ONBOARDING_DRAFT_STATE {
        string user_id PK FK
        int current_step
        string temp_title
        json temp_links
        string temp_theme
        datetime last_saved
    }

    USERS ||--o{ START_PAGES : "creates"
    START_PAGES ||--o{ START_PAGE_LINKS : "has links"
    USERS ||--|| ONBOARDING_DRAFT_STATE : "draft state"

