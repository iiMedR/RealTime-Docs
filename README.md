# Docs

A collaborative document editor built with Next.js, Clerk, Convex, Liveblocks, and TipTap. The app lets authenticated users create documents from templates, edit them in real time, comment inline, and manage access through personal or organization-based ownership.

## Features

- Real-time collaborative editing with Liveblocks + TipTap
- Inline comments, mentions, and discussion threads
- Clerk authentication and organization-aware access control
- Convex-backed document creation, listing, search, rename, and deletion
- Template gallery for quickly creating common document types
- Rich text formatting toolbar with headings, lists, tables, links, images, colors, highlights, alignment, and line spacing
- Shared document layout state for page margins
- Print-friendly document canvas

## Tech Stack

- Next.js 15 App Router
- React 19 RC
- TypeScript
- Tailwind CSS
- Clerk
- Convex
- Liveblocks
- TipTap
- Radix UI + shadcn-style UI primitives

## How It Works

The app stores document metadata and initial template content in Convex. When a user opens a document route, the page preloads the document through a Clerk-authenticated Convex query.

The editor itself runs inside a Liveblocks room keyed by the document ID. Liveblocks handles multiplayer presence, collaboration state, comment threads, and shared storage. TipTap provides the editing surface and formatting extensions. Clerk identities are used to authorize access to Liveblocks rooms and to resolve user data for mentions and avatars.

## Project Structure

```text
docs/
|-- convex/                         # Convex schema, queries, and mutations
|-- public/                         # Static assets and template previews
|-- src/
|   |-- app/
|   |   |-- (home)/                # Dashboard, template gallery, document list
|   |   |-- api/liveblocks-auth/   # Liveblocks auth endpoint
|   |   `-- documents/[documentId]/# Document page, editor, toolbar, room setup
|   |-- components/                # Shared UI and providers
|   |-- constants/                 # Templates, margins
|   |-- extensions/                # Custom TipTap extensions
|   |-- hooks/                     # App hooks
|   `-- store/                     # Editor state store
|-- liveblocks.config.ts           # Liveblocks type definitions
`-- middleware.ts                  # Clerk middleware
```

## Environment Variables

Create `docs/.env.local` with the variables your local environment needs:

```bash
CONVEX_DEPLOYMENT=your_convex_deployment
NEXT_PUBLIC_CONVEX_URL=https://your-project.convex.cloud

NEXT_PUBLIC_CLERK_PUBLISHABLE_KEY=pk_...
CLERK_SECRET_KEY=sk_...

LIVEBLOCKS_SECRET_KEY=sk_...
```

Notes:

- `NEXT_PUBLIC_CONVEX_URL` is used by both the Next.js app and server actions that query Convex over HTTP.
- `LIVEBLOCKS_SECRET_KEY` is used by the `/api/liveblocks-auth` route to authorize room access.
- Clerk must be configured so authenticated users can obtain a Convex token template named `convex`.

## Local Development

Install dependencies:

```bash
npm install
```

Run the Next.js app:

```bash
npm run dev
```

In a separate terminal, run Convex development if you need backend sync/codegen:

```bash
npx convex dev
```

Open `http://localhost:3000`.

## Available Scripts

```bash
npm run dev
npm run build
npm run start
npm run lint
```

## Core Routes

- `/` or the home area under `src/app/(home)` shows the template gallery and document list
- `/documents/[documentId]` opens the collaborative editor for a single document
- `/api/liveblocks-auth` authorizes Liveblocks room access for permitted users

## Data Model

Convex currently defines a `documents` table with:

- `title`
- `initialContent`
- `ownerId`
- `roomId`
- `organizationId`

Search is indexed on `title`, with filtering by `ownerId` and `organizationId`.

## Access Control

The app supports two access modes:

- Personal documents owned directly by the creating user
- Organization documents accessible to members of the same Clerk organization

Liveblocks room access is granted only if the requesting user is the document owner or belongs to the matching organization.

## Collaboration Details

- Each document uses its Convex document ID as the Liveblocks room ID
- User identity, avatar, and a generated cursor/comment color are injected during Liveblocks authorization
- Mentions and room metadata are resolved from Clerk users and Convex document lookups
- Shared storage currently keeps left and right page margins synchronized across collaborators

## Editor Capabilities

The current toolbar/editor supports:

- Headings and paragraph styles
- Font family and font size
- Bold, italic, underline
- Text color and highlight color
- Links and images
- Ordered lists, bullet lists, and task lists
- Tables
- Text alignment and line height
- Comments and threads
- Undo/redo and print

## Deployment Notes

For production deployment, make sure the same environment variables are configured in your hosting platform, and that:

- Clerk production keys are used
- Convex is deployed and reachable from the app
- Liveblocks production credentials are configured

## Status

This README reflects the current codebase structure in `docs/` and replaces the default `create-next-app` placeholder documentation.
