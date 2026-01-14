# CMS + Public Catalog Platform

A production-ready **Admin CMS + Public Catalog** system that allows internal teams to manage **Programs → Terms → Lessons**, upload media assets, schedule publishing, and expose a public catalog for consumers.
Here are the links for live demo:
 - [Web app](https://cms-client-app.onrender.com)
 - [Backend](https://cms-app-backend-k09y.onrender.com)
### Intitially the apps may time to take load becasue of warm up. please wiat for 2 minutes
This project is built as a **real, deployable product** with:

- Strongly enforced DB schema
- Asset uploads via S3 + CloudFront
- Scheduled publishing via a worker
- JWT-based authentication with RBAC
- Fully reproducible local setup using Docker

---

## 🏗 Architecture Overview

<img width="1536" height="1024" alt="ChatGPT Image Jan 12, 2026, 10_29_56 PM" src="https://github.com/user-attachments/assets/e6066aed-8315-4227-85b4-356ccd84d74a" />


### High-Level Components

- **Frontend (Next.js + shadcn/ui)**
  - CMS for Admins & Editors
  - Public Catalog for Viewers
- **Backend API (Spring Boot)**
  - Auth (JWT + RBAC)
  - CMS APIs
  - Public Catalog APIs
- **Worker**
  - Periodically publishes scheduled Programs & Lessons
- **PostgreSQL**
  - Enforced domain model
  - Flyway migrations
- **AWS S3**
  - Media storage (images, videos)
- **AWS CloudFront**
  - CDN for serving public assets

### Key Flows

- CMS uploads assets → **S3 via presigned URLs**
- Public catalog serves assets → **CloudFront**
- Worker transitions `scheduled → published`
- RBAC enforced at **API + UI**
---

## 🔐 Roles & RBAC

| Role   | Capabilities |
|------|-------------|
| **ADMIN** | Full CMS access, create users |
| **EDITOR** | Manage programs, terms, lessons |
| **VIEWER** | Access public catalog only |

RBAC is enforced using **JWT claims + Spring Security**.
Ive created few users for you guys:

```
admin:
  username: admin@gmail.com
  password: admin
editor:
  username: editor@gmail.com
  password: editor
viewer:
  username: viewer@gmail.com
  password: viewer
```

---

## ⚙️ Local Setup

### Prerequisites

- Docker & Docker Compose
- Java 21+
- Node.js 18+
- AWS credentials (or mocked locally)

---

### 1️⃣ Clone Repository

```bash
git clone https://github.com/FalconSlayer51/chai_shots_assignment --recursive
cd <repo>
````

---

### 2️⃣ Environment Variables

#### Backend (`backend/.env`)

```env
SPRING_DATASOURCE_URL=jdbc:postgresql://db:5432/cms
SPRING_DATASOURCE_USERNAME=postgres
SPRING_DATASOURCE_PASSWORD=postgres

JWT_SECRET=super-secret-key

AWS_S3_BUCKET=cms-s3-file-storage
AWS_REGION=ap-south-1
CDN_BASE_URL=https://<cloudfront-domain>
```

#### Frontend (`frontend/.env.local`)

```env
NEXT_PUBLIC_API_BASE_URL=http://localhost:8081
```

---

### 3️⃣ Run Everything
Do this in the both repos.
```bash
docker compose up --build
```

This starts:

* Backend API
* Worker
* PostgreSQL
* Frontend (CMS + Catalog)

---

## 🗃 Database Migrations

* Managed using **Flyway**
* Located at:

```text
backend/src/main/resources/db/migration
```
## Database Design
<img width="1615" height="838" alt="supabase-schema-jddfrpbwilvgrtruhpeu" src="https://github.com/user-attachments/assets/83dcddc3-7e4f-4026-b5e1-20cba2566b39" />

### How Migrations Run

* Automatically executed on backend startup
* Database can be recreated from scratch
* Enum migrations handled safely (e.g., adding `scheduled` status)

---

## 🌱 Seed Data

On startup (when DB is empty), a seed runner creates:

* Users (Admin, Editor)
* Programs (draft + published)
* Terms
* Lessons
* Sample assets (URLs)

This ensures the app is **demo-ready immediately**.

---

## 📦 Asset Upload Strategy (CMS-grade)

* Backend generates **presigned S3 URLs**
* Frontend uploads files **directly to S3**
* Backend stores **CloudFront public URLs**
* No binary data flows through backend

### Why This Matters

* Scalable
* Secure
* CDN-backed
* Production-standard

---

## 🚀 Deployed URLs

* **Swagger / OpenAPI**
  👉 [Api documentation link](https://cms-app-backend-k09y.onrender.com/swagger-ui/index.html)

---

## 🎬 Demo Flow (Required)

### 1️⃣ Login as Editor

* Login using seeded editor credentials
* JWT issued and stored in frontend

---

### 2️⃣ Create & Edit Content

1. Create a **Program**
2. Upload required **Program posters**
3. Create **Terms**
4. Create **Lessons**
5. Upload **Lesson thumbnails**
6. Schedule lesson publishing

---

### 3️⃣ Worker Publishes Content

* Wait for scheduled time
* Worker runs periodically
* Status transitions:

  * `scheduled → published`

---

### 4️⃣ Verify Public Catalog

* Open public catalog (no auth)
* Published program & lesson now visible
* Assets served via CloudFront

---

## 🧪 Error Handling & Validation

### Backend Enforces

* Required assets before publish
* Valid scheduling timestamps
* Correct status transitions

### Frontend Handles

* Inline validation errors
* 401 → redirect to login
* 403 → access denied
* 409 → conflict messages
* Graceful 500 handling

---

## 📌 Design Decisions
* DB-first correctness over convenience
* Presigned uploads instead of backend streaming
* Worker-based scheduling for reliability
* Simple UI, functionality-first
* API-driven frontend

## Video




https://github.com/user-attachments/assets/1039835e-5681-43f3-9f69-d0fe57bc7bf7



