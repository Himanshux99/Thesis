# Project Hub — PRD & Technical Design

Nov 15, 2026 · @himanshu Choyal

## 1. Executive summary

Project Hub replaces the old ProjectHub site with a faster rebuild that runs the whole final-year project process online: team formation, mentor allocation, topic approval and two graded reviews.

**Problem statement.** The old site is too slow, and its allocation logic leaves teams without a mentor when a mentor fills their third slot. It also hardcodes limits (3 mentors ranked, 3 teams per mentor).

**Proposed solution.** A Next.js application on managed free-tier services, organised as nine service modules. Allocation runs in preference rounds so every rejected team moves to its next mentor automatically. The super admin controls limits, stages and deadlines per project cycle.

**Success criteria.** The targets below are proposed numbers for the four outcomes you chose; adjust any that do not fit.

| Outcome | Measure | Target |
| --- | --- | --- |
| Page speed | Largest Contentful Paint, 75th percentile, on dashboard pages | 2.5 s or less |
| Page speed | API response time, 95th percentile, for read requests | 500 ms or less |
| No stuck teams | Locked teams with a mentor when allocation closes | 100% |
| No stuck teams | Teams fixed by direct database edits | 0 |
| Fully paperless | Topic approvals and review marks recorded on the site | 100%; the signed approval form is uploaded as a file |
| Free-tier cost | Monthly infrastructure bill for all five departments | Rs 0 |

**Scope decisions confirmed on 15 Nov 2026.** Teams hold up to 4 students (leader plus 3). All departments (IT, CS, ECS, ETC, BM) share one system, and each super admin manages only their own department. Target go-live is within one month, by mid-December 2026.

## 2. User experience and functionality

Three roles move one team through six stages: team, mentor, topic, Review 1, Review 2, done. Each stage opens only when the previous one is complete and the super admin has released it.

### Personas

| Persona | Who they are | What they need |
| --- | --- | --- |
| Student (member) | Final-year student in one department | Join a team, add topics, write their own review contribution, chat with the mentor |
| Student (team leader) | The member who created the team, or was handed leadership before lock | Lock the team, rank mentors, submit review forms, edit any member's entry |
| Mentor (faculty) | Faculty member published by the super admin for a cycle | Pick teams, approve topics, review and grade each student |
| Super admin | One per department | Configure the cycle, open and close stages, fix unmatched teams, see all grades |

### End-to-end flow

&#91;embedded content: end-to-end flow · 5 stages, 3 roles\]

Read each row left to right: the student acts, the mentor decides, and the super admin opens the stage or resolves what is left over.

### User stories and acceptance criteria

**Team formation**

1. As a student, I want to create or join a team so that I can take part in the project cycle.
   - A student can create one team and becomes its leader. The team gets a team code and a shareable join link.
   - A team holds at most 4 students, all from one department.
   - A student can join by team code, by join link, or by accepting an invite the leader sent after searching for them.
   - A student who created a team must delete it before joining another team.
   - A member can leave, and the leader can hand leadership to another member, only before the team is locked.
2. As a team leader, I want to lock my team so that we can start choosing mentors.
   - Lock requires the team's domains to be chosen: 3 by default, with the minimum set by the super admin.
   - Domains come only from the admin's list. Students cannot add custom domains.
   - After lock, membership, leadership and deletion are frozen.
3. As a super admin, I want a team formation deadline so that no student is left out.
   - The admin sets the deadline date and time per cycle.
   - After the deadline, the admin assigns every student without a team to a team at random.

**Mentor allocation**

4. As a team leader, I want to rank mentors so that we get the best available match.
   - The preference form opens only for locked teams.
   - The leader ranks exactly N mentors from the published list. N is set by the super admin (for example 8).
5. As a mentor, I want to choose among the teams that ranked me so that I guide projects that fit me.
   - In round 1 the mentor sees only teams that ranked them first.
   - The mentor accepts up to their capacity (3, or 4 if the super admin allows) and must reject the rest. When more teams apply than slots, the mentor chooses manually.
   - A rejected team is shown to the next mentor on its preference list automatically, with no batching.
   - Each preference round closes at a deadline the super admin sets.
6. As a super admin, I want to match leftover teams and mentors so that every team has a mentor.
   - The admin can pair any unmatched team with any mentor, including a team still waiting on a mentor's decision.
   - A manual match can take a mentor above their capacity. No reason is required.
   - The admin cannot change a pair that a mentor has already accepted.

**Topic approval**

7. As a team, we want to propose topics so that the mentor can approve one.
   - Topic submission opens when the team has a mentor and the super admin has started the topic form.
   - A topic has a title, a description, and optional documents, images and links.
   - Any member can add or edit a topic. A team can submit any number of topics.
   - Every add and edit is logged with who made it and when. The log is visible to all team members and their mentor.
   - The team can edit a topic the mentor sent back and add new topics after a rejection.
8. As a mentor, I want to review each topic separately so that feedback is specific.
   - Each topic gets one of: approve, request changes, reject. Each decision can carry written feedback.
   - The mentor can approve exactly one topic, and it becomes the team's final project topic. The team does not confirm.
   - The team and mentor share a chat to settle the topic. All members and the mentor can post.
9. As a team, we want to complete topic approval so that reviews can begin.
   - After a topic is approved, the leader uploads the signed topic approval form (one file, 10 MB or less), as on the old site.
   - The GitHub link can be added once a topic is approved.

**Reviews**

10. As a team member, I want to record my own contribution so that I am graded on my work.
    - Review 1 opens when the super admin releases it, the team has an approved topic, and the signed approval form is uploaded.
    - Each member writes their contribution and can attach documents or images. The leader can add or edit any member's entry without approval.
    - Only the leader can submit, and only when every member has an entry and the completion percentage is set.
11. As a mentor, I want to accept or reject a review so that weak submissions are corrected.
    - Reject requires a reason. The team edits the same form and resubmits.
    - On accept, the mentor fills the evaluation form, marks each student, and can change the completion percentage.
    - A completed review cannot be resubmitted.
12. As a student or super admin, I want to see marks so that results are transparent.
    - Every team member can see their own marks and their teammates' marks.
    - The mentor can see the marks of their own teams.
    - The super admin can see marks for every team and student in their department.
    - Review 2 is the final review and follows the same steps as Review 1.

**Deadlines and notifications**

13. As a team leader, I want to request an extension so that a missed deadline does not block my team.
    - When a form's global deadline passes, the form locks and shows a Request Extension button to the team leader only.
    - The request carries a short reason and covers the whole team.
    - The super admin is notified and either approves it by setting a new date or rejects it.
14. As any user, I want timely alerts so that I do not miss an action.
    - Notifications fire for the events listed in section 3.

### Non-goals

- No custom domains typed by students.
- No third review stage. The old Review 1, Review 2 and Final become Review 1 and Review 2.
- No cross-department teams and no college-wide admin role.
- No sign-up from addresses outside `@vit.edu.in`.
- No student or faculty self-registration as mentor or admin. The super admin creates those accounts.
- No video calling. Teams keep sharing external meeting links in chat.
- No migration of old ProjectHub data in the first release.

## 3. Business rules

These are the decisions from the change log, written as rules the system enforces. Anything the change log left open is listed in section 7, not guessed here.

### Settings the super admin controls per cycle

| Setting | Default | Notes |
| --- | --- | --- |
| Team formation deadline | None; must be set | Date and time. Unteamed students are assigned at random afterwards |
| Minimum domains per team | 3 | Team picks from the admin's domain list |
| Domain list | Per department | Admin adds and removes; no student-created domains |
| Published mentors | Chosen by admin | Only these appear in the preference form |
| Mentors to rank (N) | 8 suggested | Leader ranks exactly N |
| Mentor capacity | 3 teams | Can be raised to 4; admin manual matches can exceed it |
| Preference round deadline | None; must be set | Mentors must accept or reject before it passes |
| Topic submission form | Closed | Admin starts and stops it |
| Review 1 and Review 2 forms | Closed | Admin releases each one |
| Marking categories per review | Old site's scheme | Admin edits category names and maximum marks |
| Form deadlines | Per form | Extended through extension requests |

### Team rules

| Rule | Before lock | After lock |
| --- | --- | --- |
| Join or leave the team | Allowed | Blocked |
| Change the leader | Allowed | Blocked |
| Delete the team | Leader only | Blocked |
| Create a second team or join another | Only after deleting your own team | Blocked |
| Submit mentor preferences | Blocked | Allowed |

### Mentor allocation rules

1. Round 1 shows each mentor only the teams that ranked them first.
2. A mentor accepts at most their capacity and rejects every other team in the round. The choice between teams is the mentor's own.
3. A rejected team is shown at once to the next mentor on its preference list. This also happens when a mentor's last slot fills, which is the step the old site skipped.
4. Teams are not held back or grouped into batches. A mentor with open slots sees each such team as soon as it is passed on.
5. Each preference round has a deadline set by the super admin.
6. A team that runs out of preferences, and a mentor left with open slots, both go to the super admin's manual matching list.
7. The super admin can create a new pair for any unmatched team, even if it takes the mentor above capacity. No reason is required.
8. An accepted pair is final and cannot be overridden by the admin.
9. Every accept, reject and manual match is written to the audit log.

### Topic rules

- Topics can be submitted only after a mentor is assigned and the topic form is open.
- Any team member can add or edit topics. There is no limit on the number of topics.
- Every add and edit is logged and shown to all team members and the team's mentor.
- Each topic is reviewed on its own: approve, request changes, or reject.
- The mentor can approve only one topic per team. That topic is the final project topic.
- After approval, the leader uploads the signed topic approval form.
- An approved topic and an uploaded signed form are both required before Review 1.

### Review and evaluation rules

- There are two reviews. Review 2 unlocks after Review 1 is completed and the admin releases it.
- Review states: not started, in progress, submitted, rejected (with reason), completed.
- The leader can edit any member's contribution entry without that member's approval.
- The mentor can change the completion percentage during evaluation, and the change updates the review.
- Each student is marked out of 25 per review by default. The super admin can change the categories and their maximum marks.
- Marks are visible to every member of the team, the team's mentor, and the department's super admin.

| Review | Default marking categories (maximum marks) |
| --- | --- |
| Review 1 | Progress of implementation (10), individual contribution (10), paper publication status (5) |
| Review 2 | Tech (5), innovation (5), presentation (5), activity (5), synopsis (5) |

Both defaults come from the old site and total 25 marks.

| Evaluation form (Review 2) | Fields |
| --- | --- |
| Details | Date, division, group ID, project guide, project title |
| Classification | Domain of project, quality grade, nature of project, completion percentage |
| Marks per student | Tech (5), innovation (5), presentation (5), activity (5), synopsis (5); total 25 |

### Files and chat

- Each step has its own file rules. The agreed ceiling is 5 files per submission, 10 MB each, documents, PDFs and images.
- Chat between a team and its mentor stays readable after the project cycle ends.

### Notifications

| Event | Recipients | Email | Firebase push | In-app |
| --- | --- | --- | --- | --- |
| Team invite | Invited student | Yes | No | Yes |
| Member joined | Team | No | No | Yes |
| Team locked | Team | Yes | No | Yes |
| Deadline approaching | Students, and mentors for the team selection deadline | Yes | Yes | Yes |
| Mentor allotted | Team | Yes | Yes | Yes |
| Extension requested | Super admin | Yes | Yes | Yes |

### Deadlines and extensions

1. When a form's global deadline passes, the form locks.
2. A locked form shows a Request Extension button to the team leader. Only the leader can ask, because only the leader submits the form.
3. The leader submits a short reason. The request covers the entire team.
4. The super admin is notified and reviews the request.
5. If approved, the admin sets a new date. This creates an `approved` row in `Form_Extensions`, and the form reopens for that team until the new date.
6. If rejected, the request is stored as `rejected` and the form stays locked.

### Project cycles

The system supports multiple academic years and batches. Every team, allocation, topic, review and setting belongs to one cycle of one department, so a new batch starts clean while old cycles stay readable.

## 4. AI system requirements

Not applicable. Project Hub has no AI features in this release, so there are no model, tool or evaluation requirements.

## 5. Technical specifications

Project Hub is one Next.js application on Vercel, split into nine service modules, with PostgreSQL as the single source of truth and managed services for files, chat, email and push.

### Architecture overview

&#91;embedded content: architecture · 1 app, 9 modules, 6 managed services\]

The browser talks only to the Next.js app, except for two direct paths: file uploads to R2 through signed URLs and chat delivery from Supabase Realtime.

### Stack

| Requirement | Choice | Used for |
| --- | --- | --- |
| Frontend and backend | Next.js on Vercel Hobby | Pages, API routes, server-side logic |
| Authentication | Better Auth | Sign-up, login, sessions, roles |
| Database | PostgreSQL on Supabase Free | All relational data |
| Object storage | Cloudflare R2 | Topic and review attachments |
| Real-time chat | Supabase Realtime | Team and mentor chat delivery |
| Push notifications | Firebase Cloud Messaging | Browser push |
| Transactional email | Resend | Invites, lock, deadline and allocation emails |
| Scheduled work | Vercel Cron | Deadline locks and reminders |
| Async processing | Vercel Queues and Vercel Functions | Notification fan-out, round advancement |
| Caching | Next.js cache and Upstash Redis | Reference data, hot dashboards, rate limits |

The database access layer (ORM or query builder) is not chosen yet. The old site used Prisma.

### Service modules

The nine services are modules inside one deployment, not separate servers. Each owns its tables and exposes functions the others call.

| Service | Responsibility | Owns |
| --- | --- | --- |
| 1. Authentication and authorization | Login, sessions, role-based access | Users, sessions |
| 2. User management | Student, mentor and admin profiles, department data | Profiles, departments |
| 3. Team management | Creation, membership, invites, join requests, lock, constraints | Teams, members, invites, team domains |
| 4. Mentor allocation | Preferences, rounds, capacity, accept and reject, manual match | Preferences, requests, allocations |
| 5. Project and topic management | Topic submissions, edits, mentor feedback, approval, GitHub link | Topics, topic feedback |
| 6. Communication | Team and mentor messaging | Conversations, messages |
| 7. Review and evaluation | Review submissions, rejection, resubmission, marks | Reviews, contributions, evaluations |
| 8. Workflow management | Stages, submission windows, deadlines, extensions | Cycles, form windows, `Form_Extensions` |
| 9. Notification | In-app, email and push alerts, reminders | Notifications, push tokens |

### Data model (proposed)

Table names below are a starting proposal, except `Form_Extensions`, which the change log names. Every row outside reference data carries a `cycle_id` and a `department_id`.

| Group | Tables | Key constraints |
| --- | --- | --- |
| Identity | `users`, `sessions`, `profiles`, `departments` | One profile per user; role is student, mentor or super admin |
| Cycle | `project_cycles`, `cycle_settings`, `cycle_mentors`, `domains` | Mentor capacity stored per mentor per cycle |
| Teams | `teams`, `team_members`, `team_invites`, `team_domains` | A student is in one team per cycle; at most 4 members; unique team code |
| Allocation | `mentor_preferences`, `allocation_requests`, `allocations` | Unique rank per team; one allocation per team; count per mentor never exceeds capacity |
| Topics | `topics`, `topic_feedback`, `project_links` | One approved topic per team; every topic edit logged; one signed approval form per team |
| Chat | `conversations`, `messages` | Messages are never deleted at cycle end |
| Reviews | `review_submissions`, `member_contributions`, `evaluations`, `evaluation_marks` | One submission per team per review; one mark row per student per evaluation; marking categories stored per cycle |
| Workflow | `form_windows`, `Form_Extensions` | Extension status is pending, approved or rejected |
| Files | `files` | Points to an R2 object; linked to a topic or a contribution |
| Notifications | `notifications`, `push_tokens` |  |
| Audit | `audit_logs` | Append-only |

### Key technical decisions

- **Allocation correctness.** Accepting a team runs in one database transaction that locks the mentor's capacity row, creates the allocation, and advances every rejected team to its next preference. A database constraint blocks a second allocation for the same team.
- **File uploads.** The browser uploads straight to R2 using a short-lived signed URL from the API. The API checks type, size (10 MB) and count (5) before issuing the URL and records the file in PostgreSQL.
- **Chat.** The API writes each message to PostgreSQL, then Supabase Realtime delivers it to subscribers of that conversation. Because login is handled by Better Auth and not Supabase Auth, the API must issue the token that authorises a client to join a channel.
- **Notifications.** A domain event (invite sent, team locked, mentor allotted) is placed on a queue. A worker writes the in-app notification and sends email or push as the event requires.
- **Deadlines.** Forms check the deadline on every request, so a form locks at the exact time without waiting for a scheduled job. Cron is used only for reminders.
- **Caching.** Domain lists, published mentors and cycle settings are cached and invalidated on admin change. Allocation state and marks are always read fresh.

### Integration points

| Integration | Direction | Notes |
| --- | --- | --- |
| Better Auth to PostgreSQL | Read and write | Auth tables live in the same database |
| API to Cloudflare R2 | Signed URLs | Upload and download links expire |
| API to Supabase Realtime | Publish and subscribe | One channel per conversation |
| Worker to Resend | Outbound | Email templates per event |
| Worker to Firebase Cloud Messaging | Outbound | Push tokens stored per device |
| GitHub | Link only | The project stores a repository URL; no API calls |

### Security and privacy

- Sign-up is limited to `@vit.edu.in` email addresses.
- Sessions are held in HTTP-only cookies, replacing the old site's tokens in browser localStorage.
- One shared access guard checks role, department and cycle on every API route. The old site repeated this check inside each service.
- A student can read only their own team's data. A mentor can read only teams allocated to them or currently requesting them. A super admin can read only their department.
- Marks are writable only by the allocated mentor. They are readable by the members of that team, that mentor, and the department's super admin.
- Every request body is validated against a schema, and unknown fields are rejected.
- Files are private in R2 and served only through signed links.
- Audit logs record admin actions, mentor allocation decisions, topic edits and evaluation changes, with the actor, time and before and after values.

### Observability

| Purpose | Tool | Usage |
| --- | --- | --- |
| Error tracking | Sentry | Exceptions and performance issues |
| Structured logging | Pino | JSON logs from the Next.js backend |
| Uptime monitoring and alerts | Better Stack | Availability checks and downtime alerts |
| Application monitoring | Vercel Observability | Deployments, function duration, runtime logs |
| Database and Realtime monitoring | Supabase Dashboard | Queries, connections, Realtime usage |
| Cache monitoring | Upstash Console | Requests, latency, memory, hit and miss ratio |

Supporting pieces: health checks at `/api/health/live` and `/api/health/ready`, and a request ID carried through every module's logs.

## 6. Risks and roadmap

The one-month target is reachable only if the release follows the order students need the stages: teams first, reviews last. Reviews can ship after go-live because no team reaches Review 1 in the first weeks.

### Phased rollout (proposed)

&#91;embedded content: roadmap · 4 phases, 1 go-live gate\]

The dates are a proposal that fits the one-month target. v1.2 must be live before the super admin releases Review 1.

| Phase | Contents | Exit check |
| --- | --- | --- |
| MVP | Login and roles, profiles, cycles and admin settings, domains, team create, join, lock and delete, mentor preferences, allocation rounds, admin manual match, in-app and email notifications, audit log | A test cycle of 20 teams and 6 mentors ends with every team matched and no manual database edit |
| v1.1 | Topic form, topic review, file uploads, team and mentor chat, GitHub link, signed approval form upload, deadline lock and extension requests | A team gets a topic approved end to end, with files and chat |
| v1.2 | Review 1 and Review 2 forms, contributions, reject and resubmit, evaluation form and marks, admin evaluations view, push notifications | A mentor grades a 4-member team and the admin sees the marks |
| v2.0 | CSV and PDF exports, old ProjectHub data import, admin overview dashboards | Not scheduled |

### Technical risks

| Risk | Why it matters | Mitigation |
| --- | --- | --- |
| Email cap on Resend's free plan | Free accounts send [100 emails a day and 3,000 a month](https://resend.com/docs/knowledge-base/resend-email-quota), and a blocked send is not retried by Resend. A lock or deadline email to one department can exceed 100 in a day | Deadline reminders now go by email as well, so one department-wide reminder can use the whole day's quota. Spread reminder emails over several days, queue and retry blocked sends, and budget for Resend's paid plan |
| Cron limits on Vercel Hobby | Hobby cron jobs run [at most once a day, at an unpredictable minute within the hour](https://vercel.com/docs/schedules/cron-jobs/usage-and-pricing) | Enforce deadlines at request time, not by cron; send reminders as a daily digest |
| Hobby plan terms and quotas | Hobby includes [1,000,000 function invocations and 4 CPU-hours a month, and an exhausted feature can stay blocked for up to 30 days](https://vercel.com/docs/plans/hobby). Vercel's [fair use guidelines](https://vercel.com/docs/limits/fair-use-guidelines) also limit what Hobby may host | Confirm a college deployment is allowed on Hobby; cache read-heavy pages; alert at 70% of each quota |
| Supabase Free quotas | The free plan allows [500 MB of database, 200 peak Realtime connections and 2 million Realtime messages](https://supabase.com/docs/guides/platform/billing-on-supabase). Free projects are also reported to pause after about a week without activity; confirm this in Supabase's docs | Keep files out of the database; open a chat connection only on chat pages; add a daily keep-alive query; watch connection counts during deadline days |
| Realtime authorisation without Supabase Auth | Better Auth sessions are not recognised by Supabase Realtime, so channel access needs a token the API issues | Build and test the token exchange in week 1 of v1.1; fall back to polling every few seconds if it slips |
| Allocation race conditions | Two accepts at the same moment could exceed capacity or strand a team, repeating the old site's bug | Single transaction with a row lock, database constraints, and an automated test that replays concurrent accepts |
| Random team assignment | Leftover students may not divide into valid teams | Admin previews the proposed teams and confirms before they are created |
| One-month schedule | Nine services is a large scope for the time available | Ship in the phases above; cut v2.0 items first, then fall back from live chat to polling |

Free-tier figures were checked on 15 Nov 2026 and change often.

## 7. Open questions and assumptions

All nine open points were settled on 15 Nov 2026 and are written into sections 2, 3 and 5.

### To decide

Nothing is open. Extensions are requested by the team leader for the whole team, and the super admin can approve or reject them.

### Assumptions made in this document

- The backend is Next.js route handlers and server functions on Vercel, with no separate NestJS server. The dependency notes imply this but do not state it.
- The signed topic approval form works as on the old site: the leader uploads one file of 10 MB or less after approval, and reviews stay locked until it is uploaded.
- The topic approved by the mentor is final with no confirmation step from the team.
- Mentor and super admin accounts are created by the super admin, as on the old site, using `@vit.edu.in` addresses.
- In-app notifications exist for every event; email and Firebase push are added per event as listed in section 3.
- Expected user counts per department are unknown. Free-tier risk in section 6 should be rechecked once they are.

### Sources

- Change log, Flow of the Website, External dependency and Services notes, shared 15 Nov 2026
- Old Website Feature Doc, 13 Nov 2026
- [Resend email quota](https://resend.com/docs/knowledge-base/resend-email-quota)
- [Vercel cron jobs usage and pricing](https://vercel.com/docs/schedules/cron-jobs/usage-and-pricing)
- [Vercel Hobby plan](https://vercel.com/docs/plans/hobby)
- [Supabase billing](https://supabase.com/docs/guides/platform/billing-on-supabase)
