# Assignment-of-implementing-stack-and-queues-in-real-world-problems



## 1. Stack Applications

A **Stack** follows LIFO (Last In, First Out). The last item added is the first item removed.

### 1.1 AI Agent Rollback Engine

Imagine an AI agent performing these actions:

`Create Account → Add Permissions → Deploy Service`

If deployment fails, a stack can help reverse completed actions in the opposite order.

**How it works:**
1. Push each successful action and its compensation operation onto a stack.
2. If a later action fails, pop actions and execute their compensations.
3. Restore the system as closely as possible to its previous state.

**Real-world uses:**
- AI agents
- Cloud automation
- Deployment platforms

> Note: This is a compensating-action pattern. Some operations, such as sending an email, cannot truly be undone.

### 1.2 Production API Failure Tracer

A backend request passes through authentication, validation, business logic, and database operations.

**How it works:**
- Push a frame when entering a function.
- Pop it when the function returns.
- Capture the call stack when an exception occurs.

**Real-world uses:**
- Debuggers
- Runtime environments
- Backend observability

**Project idea:** Build a visualizer that shows where an API request failed.

### 1.3 HTML Security Scanner

A stack can validate nested HTML or XML tags.

Example:

```html
<div>
  <script>
    suspiciousCode()
  </div>
</script>
```

The scanner tracks opening tags and checks whether closing tags match.

**Real-world uses:**
- Parsers
- Linters
- Document validation
- Security analysis

**Project idea:** Flag dangerous HTML patterns before rendering user-generated content. Proper XSS protection also requires context-aware sanitization and other security controls.

### 1.4 Workflow Builder with Nested Conditions

Example:

`IF referral → IF employee eligible → IF candidate hired → Calculate bonus`

A stack can track nested conditions while parsing and evaluating workflows.

**Real-world uses:**
- Workflow engines
- Rule engines
- Interpreters

**Project idea:** Create a visual interface where HR teams define nested approval rules.

---

## 2. Queue Applications

A **Queue** usually follows FIFO (First In, First Out). The first item added is the first item removed.

### 2.1 AI Request Traffic Manager

Suppose 1,000 employees upload resumes simultaneously, but an AI service can process only 20 requests at a time.

**How it works:**
1. Place incoming jobs in a queue.
2. Let a controlled number of workers process jobs.
3. Retry failed jobs or send them to a dead-letter queue.
4. Display Pending, Processing, and Completed statuses.

**Real-world uses:**
- AI inference systems
- Background jobs
- Email processing

**Project idea:** Add priorities so urgent recruiter requests are processed before bulk analysis.

### 2.2 API Rate Limiter

Prevent users from making too many API requests within a short period.

**How it works:**
1. Add the current request timestamp to a queue or deque.
2. Remove timestamps outside the allowed time window.
3. Count the remaining requests.
4. Accept or reject the new request.

**Example:** Allow at most 100 requests per minute per user.

**Real-world uses:**
- API gateways
- SaaS platforms
- Public APIs

A **deque** is useful because old timestamps are removed from the front while new timestamps are added at the back.

### 2.3 Smart Recruitment Pipeline

Example:

`Submitted → Screening → Interview → Offer`

**How it works:**
- Maintain queues of candidates waiting at each stage.
- Process eligible candidates.
- Track waiting time and identify bottlenecks.
- Escalate applications that remain untouched too long.

**Real-world uses:**
- Applicant tracking systems
- Ticketing systems
- Approval workflows

**Project idea:** Use a priority queue to rank tasks by urgency, SLA deadlines, and business rules.

### 2.4 Real-Time Security Event Processor

A service receives thousands of login events per second.

**How it works:**
1. Enqueue login events.
2. Let workers analyze repeated failures and suspicious patterns.
3. Trigger alerts when predefined rules are met.
4. Track queue depth to detect overload.

**Real-world uses:**
- Security monitoring
- Fraud detection
- Event processing pipelines

**Project idea:** Combine a queue with a sliding time window to detect brute-force attacks.

---

## 3. Combined Project: AI Workflow Orchestrator

### Project Overview

Build an **AI Workflow Orchestrator with Rollback and Rate Control**.

Example: A user submits 200 resumes for analysis.

### Architecture

```text
User submits workflow
        |
        v
Queue stores pending jobs
        |
        v
Workers execute workflow steps
(Parse → Embed → Search → Generate Report)
        |
        +---- Success: Save output and history
        |
        +---- Failure: Execute eligible
                       compensating actions
                       using a stack
```

### Components

| Component | Data Structure | Responsibility |
|---|---|---|
| Job scheduler | Queue | Store waiting jobs |
| Urgent job handler | Priority Queue | Select high-priority work |
| Workflow executor | Stack | Track nested execution or compensation |
| Rate limiter | Deque | Track requests in a time window |
| Job history | Database | Persist status and execution details |

### Technology Stack

- Python
- FastAPI
- React
- MongoDB
- Redis
- Optional durable task queue or message broker

### Implementation Roadmap

1. Implement an in-memory queue and stack.
2. Create a FastAPI backend.
3. Build a React dashboard.
4. Display Pending, Processing, Completed, and Failed jobs.
5. Add retries and duplicate-job protection.
6. Add execution history and compensating actions.
7. Use Redis or a durable task queue for more reliable job processing.

### Important Engineering Considerations

- **Durability:** In-memory queues can lose pending jobs after a restart.
- **Duplicate jobs:** Use idempotency keys or equivalent safeguards.
- **Worker crashes:** Define retry limits and a dead-letter strategy.
- **Concurrency:** Prevent workers from incorrectly claiming the same job.
- **Rollback:** Some operations cannot be undone; use compensating actions.
- **Priorities:** Prevent low-priority jobs from being starved indefinitely.

---

