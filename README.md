# Full-Stack Task Tracker — Patch Exercise

A focused debugging and improvement patch for a small full-stack Task
Tracker application built with React, Spring Boot, and H2.

## Tech Stack

- Frontend: React 18, Vite 5, JavaScript
- Backend: Java 17, Spring Boot 3.2, Spring Data JPA, Maven
- Database: H2 in-memory database
- SQL: H2 SQL and Oracle PL/SQL reference artifact

## Project Overview

This project was provided as a Full-Stack Software Engineer technical
exercise. The application contains intentional issues across the
frontend, backend, and SQL layers.

The goal of this submission is to identify and fix the highest-value
issues while keeping the patch focused and avoiding an unnecessary
rewrite.

## Key Fixes

### 1. SQL AND/OR Precedence

The task search query did not group the title and description conditions
correctly. Because SQL evaluates `AND` before `OR`, archived tasks could
appear in some search results and the status filter was not consistently
applied.

**Fix:** Grouped the title/description search conditions with
parentheses so archived and status filters are applied correctly. The
same fix was applied in the repository query, `db/queries/search_tasks.sql`,
and both Oracle queries.

### 2. Removed Artificial API Delay

The backend used `Thread.sleep()` to simulate query complexity. This
blocked the request thread and unnecessarily slowed API responses.

**Fix:** Removed the artificial delay and unnecessary complexity
calculation.

### 3. Invalid Status Handling

An invalid status could cause `IllegalArgumentException` and result in
an internal server error.

**Fix:** Validate the status value and return a clear `400 Bad Request`
response.

### 4. Pagination Validation

Invalid page or page-size values (for example `page=0` or a negative
`pageSize`) caused an internal server error.

**Fix:** Validate pagination parameters and return `400 Bad Request` for
invalid input. `pageSize` is capped at 100.

### 5. Stale Responses (Race Condition)

Every search change started a new request, and an older response could
arrive after a newer one and overwrite the fresh results.

**Fix:** The data-fetching hook ignores responses from superseded
requests, so stale data cannot replace newer results.

### 6. Frontend Loading and Error State

When an API request failed, loading could remain active and an old error
could remain visible.

**Fix:** Clear the previous error when a request starts and use
`finally()` to always reset the loading state.

### 7. Search/Filter Pagination Reset

Changing search or status while on a later page could request a page
that no longer existed.

**Fix:** Reset pagination to page 1 whenever the search query or status
filter changes.

## Project Structure

```text
superrdev-patch-exercise/
├── backend/
├── frontend/
├── db/
├── handwritten/
└── README.md
```

## How to Run

### Prerequisites

- Java 17+
- Node.js 18+
- Git
- No Docker required
- No external database required

### Backend

Windows:

```bash
cd backend
mvnw.cmd spring-boot:run
```

Backend:

```text
http://localhost:8080
```

### Frontend

Open another terminal:

```bash
cd frontend
npm install
npm run dev
```

Frontend:

```text
http://localhost:5173
```

The Vite development server proxies `/api/*` requests to the backend.

## Useful API Requests

Get tasks:

```text
GET /api/tasks
```

Search:

```text
GET /api/tasks?q=api
```

Filter by status:

```text
GET /api/tasks?status=OPEN
```

Search with pagination:

```text
GET /api/tasks?q=api&page=1&pageSize=5
```

## H2 Console

```text
http://localhost:8080/h2-console
```

Connection:

```text
JDBC URL: jdbc:h2:mem:taskdb
Username: sa
Password: leave blank
```

## Testing

The following areas were checked:

- Task search
- Status filtering
- Combined search and status filtering
- Archived task exclusion
- Pagination
- Invalid pagination input
- Invalid status input
- Frontend loading state
- Frontend error handling
- Search/filter pagination reset
- Backend startup
- Frontend startup

## Remaining Risk

The backend currently retrieves all matching records and performs
pagination in Java. This is acceptable for the small exercise dataset,
but a production application with a large dataset should use
database-level pagination with Spring Data `Pageable` or SQL
`LIMIT/OFFSET`.

Other possible future improvements include debounced search, structured
logging, escaping `LIKE` wildcards, automated integration tests, and
richer API error responses.

## AI Usage

AI tools were used as development assistance for code review,
identifying potential bugs, and discussing possible fixes. All applied
changes were reviewed and understood before inclusion. The
implementation was kept focused on the highest-value issues within the
exercise timebox.

## Submission

This repository contains:

- Patched full-stack application
- Focused bug fixes

- `handwritten/` handwritten explanation images

Repository:

`https://github.com/satyajitlangote/superrdev-patch-exercise`
