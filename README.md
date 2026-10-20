# ContentFlow

A focused content planning workspace for capturing ideas, managing drafts, and keeping a publishing calendar visible.

## What This App Does

ContentFlow gives a solo creator one place to:

- Create and edit content posts
- Assign a platform and workflow status
- Schedule posts on a monthly calendar
- Search and filter the full content library
- Review upcoming content and recent activity
- Switch between light and dark mode
- Keep posts available between browser sessions

## Screens & Views

### Overview

A dashboard with headline metrics for total posts, scheduled content, ready-to-publish posts, and posts currently in editing. It also surfaces upcoming content and recent activity.

### Calendar

A monthly planning view where scheduled posts appear on their dates. Use the month controls to move through the calendar, select Today, create a post directly on a date, or open an existing post.

### All Posts

A searchable content library with filters for status and platform, plus sorting by recently updated or scheduled date. Each result opens in the post editor.

### Post Editor

A modal form for creating and updating a title, body, platform, status, and scheduled date. Existing posts can also be deleted after confirmation.

## Tech Stack Decisions

- Next.js 16 with the App Router
- React 19 and TypeScript
- Tailwind CSS v4 for project styling
- shadcn/ui Button for primary actions
- lucide-react for interface icons
- Browser `localStorage` for lightweight persistence
- Native date and select controls for simple, accessible forms

## Project Structure

```text
app/
├── globals.css     # App theme, layout, responsive styles, and component styling
├── layout.tsx      # Root metadata and document layout
└── page.tsx        # ContentFlow views, state, and reusable page components

components/
└── ui/
    └── button.tsx  # shadcn/ui button primitive

public/             # Static assets
package.json        # Scripts and dependencies
```

## Architecture Decisions (Start to End)

### Decision 1: localStorage as the persistence layer

This is a browser-first personal workspace, so posts are stored under `contentflow-posts-v1` in localStorage. The app does not require an account, backend, or network request to manage content.

### Decision 2: One shared post model

Overview, Calendar, All Posts, and the editor all work from the same `ContentPost` shape. This keeps edits consistent across every view.

### Decision 3: Explicit workflow statuses

Posts use three statuses: `idea`, `editing`, and `ready`. The values are explicit and typed so labels, badges, filters, and calendar styling stay aligned.

### Decision 4: Explicit platform registry

Supported platforms are defined in one registry: LinkedIn, X, Instagram, YouTube, Blog, and Other. The registry drives labels, form options, and filtering options.

### Decision 5: Derived statistics

Dashboard metrics are calculated from the current posts array instead of being stored separately. This avoids stale totals after create, edit, or delete operations.

### Decision 6: Search, filter, then sort

The All Posts view derives its list from the source array in a predictable pipeline: text search, status filter, platform filter, and finally sort order.

### Decision 7: Calendar dates use local date keys

Scheduled dates are stored as `YYYY-MM-DD` strings. Calendar rendering converts those keys into local dates without timezone conversion, preventing common off-by-one display errors.

### Decision 8: Modal-based editing instead of routes

Creating and editing a post happens in a modal so users can stay in their current dashboard, calendar month, or filtered library state.

### Decision 9: Optimistic local updates

Create, edit, and delete actions update React state immediately. The persistence effect then mirrors the latest state to localStorage rather than refetching after every mutation.

### Decision 10: Client-side validation

The editor requires a title before saving and reports the validation error next to the title field. Content length is bounded at 5,000 characters.

### Decision 11: Confirm destructive actions

Deleting a post uses a browser confirmation dialog and clearly communicates that the action cannot be undone.

### Decision 12: Responsive layout

The desktop sidebar becomes a compact mobile header, while cards, tables, filters, and calendar cells adapt to smaller screens through the global stylesheet.

### Decision 13: Light and dark themes

Theme state is held in the page and applied through the document root's `dark` class. Both themes reuse the app's semantic color variables and preserve readable contrast.

### Decision 14: Empty states at data boundaries

Overview sections and filtered post results provide explicit empty states with a clear next action instead of leaving blank space.

### Decision 15: Accessible interaction labels

Icon-only controls have accessible labels, the editor exposes dialog semantics, and buttons are used for interactive rows and actions.

### Decision 16: `@/` path alias

The app uses the configured `@/` alias for internal imports, such as the shadcn/ui Button component.

## File-by-File Breakdown

### `app/page.tsx`

Contains the main ContentFlow experience and its reusable local components:

- `Page` — global state, navigation, persistence, and view composition
- `Overview` — dashboard metrics and activity sections
- `Calendar` — monthly schedule grid
- `Posts` — search, filters, sorting, and post table
- `PostForm` — create and edit modal
- `PostRow`, `StatusBadge`, and `EmptyState` — shared display primitives

### `app/globals.css`

Defines the visual system, including colors, typography, sidebar layout, cards, calendar grid, post table, modal styles, responsive breakpoints, and dark theme overrides.

### `app/layout.tsx`

Defines the root HTML structure, page metadata, and global stylesheet import.

### `components/ui/button.tsx`

Provides the shadcn/ui Button primitive used for primary and outlined actions.

## Data Model

```ts
type PostStatus = 'idea' | 'editing' | 'ready'

type ContentPlatform =
  | 'linkedin'
  | 'x'
  | 'instagram'
  | 'youtube'
  | 'blog'
  | 'other'

type ContentPost = {
  id: string
  title: string
  body: string
  platform: ContentPlatform
  status: PostStatus
  scheduledDate: string | null
  createdAt: string
  updatedAt: string
}
```

## Getting Started

Install dependencies and start the development server:

```bash
pnpm install
pnpm dev
```

Then open [http://localhost:3000](http://localhost:3000).

To create a production build:

```bash
pnpm build
pnpm start
```

## What Comes Next

Potential next steps for a production version include:

- Replace localStorage with authenticated database storage
- Add user accounts and multiple workspaces
- Add drag-and-drop calendar rescheduling
- Add publishing integrations for each platform
- Add recurring content and content templates
- Add import and export for backups
- Add keyboard shortcuts beyond the displayed New post shortcut
