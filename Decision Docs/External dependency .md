| Requirement             | My Initial Choice                        |
| :---------------------- | :--------------------------------------- |
| **Authentication**      | Better Auth                              |
| **Database**            | PostgreSQL (Supabase Free)               |
| **Object storage**      | Cloudflare R2                            |
| **Real-time chat**      | Supabase Realtime                        |
| **Push notifications**  | Firebase Cloud Messaging                 |
| **Transactional email** | Resend                                   |
| **Frontend hosting**    | Vercel Hobby                             |
| **Background tasks**    | Vercel Cron / in-app jobs where suitable |
| Asycn Processes         | Vercel Queues + Vercel Functions         |

for cachin we will use next.js caching and redis caching as well using vercel-upstash.

## **Observability – Recommended Tools & Services**

| Purpose                    | Tool / Service           | Usage                                                                       |
| -------------------------- | ------------------------ | --------------------------------------------------------------------------- |
| Error tracking             | **Sentry**               | Track application errors, exceptions and performance issues.                |
| Structured logging         | **Pino**                 | Generate structured JSON logs from the Next.js backend.                     |
| Uptime monitoring & alerts | **Better Stack**         | Monitor application availability and send alerts when the system goes down. |
| Application monitoring     | **Vercel Observability** | Monitor deployments, serverless functions, execution time and runtime logs. |
| Database monitoring        | **Supabase Dashboard**   | Monitor PostgreSQL performance, connections, queries and database health.   |
| Cache monitoring           | **Upstash Console**      | Monitor Redis requests, latency, memory usage and cache hit/miss ratio.     |
| Realtime monitoring        | **Supabase Dashboard**   | Monitor Realtime connections and message usage.                             |

### **Supporting implementation**
- **Health Check APIs:** `/api/health/live` and `/api/health/ready`.
- **Request ID / Correlation ID:** Track a request across different modules.
- **Audit Logs:** Record important admin actions, mentor allocation decisions and evaluation changes.

## Services
### 1. Authentication & Authorization
Responsible for user authentication, session management,
and role-based access control.
### 2. User Management
Responsible for student, mentor, and administrator
profiles and institutional information.
### 3. Team Management
Responsible for team creation, membership,
invitations, join requests, and team constraints.
### 4. Mentor Allocation
Responsible for mentor preferences, allocation rounds,
mentor capacity, acceptance/rejection, and manual assignment.
### 5. Project & Topic Management
Responsible for topic submissions, modifications,
mentor feedback, approvals, and GitHub links.
### 6. Communication
Responsible for team-mentor messaging and
conversation management.
### 7. Review & Evaluation
Responsible for review submissions, mentor evaluations,
rejections, resubmissions, and marks.
### 8. Workflow Management
Responsible for controlling project stages,
submission windows, deadlines, and form availability.
### 9. Notification
Responsible for in-app notifications, email alerts,
and deadline reminders.