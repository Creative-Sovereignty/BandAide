# Band Aide - Band Management Platform

## Overview

Band Aide is a comprehensive, all-in-one management platform designed for bands and musical groups. It provides tools for internal communication, song/setlist management, scheduling, merchandise inventory, fan engagement, financial tracking, press kit creation, and digital distribution monitoring. The application features an AI assistant powered by OpenAI's GPT-5 model for intelligent band management support, automated daily summaries, and voice interaction capabilities.

The platform is built as a full-stack TypeScript application with a React frontend and Express backend, utilizing PostgreSQL for data persistence and integrating with external services like PayPal for e-commerce and OpenAI for AI features.

## User Preferences

Preferred communication style: Simple, everyday language.

## System Architecture

### Frontend Architecture

**Framework & Build System**
- **React 18** with TypeScript for the UI layer
- **Vite** as the build tool and development server
- **Wouter** for client-side routing (lightweight alternative to React Router)
- **TanStack Query (React Query)** for server state management and data fetching

**UI Component System**
- **shadcn/ui** component library built on Radix UI primitives
- **Tailwind CSS** with custom theming and CSS variables for styling
- **New York** style variant from shadcn/ui
- Custom fonts: Cinzel for headings, Inter for body text
- Dark theme with industrial/grunge aesthetic using texture overlays

**State Management Approach**
- Server state managed via TanStack Query with aggressive caching (staleTime: Infinity)
- Local component state using React hooks
- No global client state management library (Redux, Zustand, etc.)
- Form state handled by React Hook Form with Zod validation

**Key Design Patterns**
- Query keys follow REST-like patterns (`["/api/resource"]`)
- Mutations invalidate related queries for cache consistency
- Custom error handling for 401 Unauthorized responses
- Responsive design with mobile-first approach using Tailwind breakpoints

### Backend Architecture

**Server Framework**
- **Express.js** with TypeScript running on Node.js
- HTTP server created using Node's `http` module for WebSocket potential
- Session-based authentication using express-session with PostgreSQL storage
- Middleware for JSON parsing, URL encoding, and raw body capture (for webhooks)

**Authentication & Authorization**
- **Replit Auth** via OpenID Connect (OIDC) with passport.js
- Session management using connect-pg-simple for PostgreSQL-backed sessions
- 7-day session TTL with HTTP-only, secure cookies
- User profile data stored in PostgreSQL users table

**API Design**
- RESTful API endpoints under `/api` prefix
- Standard HTTP methods (GET, POST, PUT, DELETE)
- JSON request/response format
- Authentication middleware (`isAuthenticated`) protecting routes
- Request/response logging with timing metrics

**Build & Deployment Strategy**
- Separate client and server build processes
- Client built with Vite to `dist/public`
- Server bundled with esbuild to CommonJS format in `dist/index.cjs`
- Selective server dependency bundling (allowlist) to reduce cold start times
- Production mode uses built static files, development uses Vite dev server with HMR

### Data Storage & ORM

**Database**
- **PostgreSQL** as the primary relational database
- Connection pooling via `pg` library
- Database URL provided via `DATABASE_URL` environment variable

**ORM & Schema Management**
- **Drizzle ORM** for type-safe database queries
- **Drizzle Kit** for schema migrations
- Schema definitions in `shared/schema.ts` using Drizzle's PostgreSQL column types
- **Zod** schemas auto-generated from Drizzle schemas for runtime validation

**Data Models**
The application manages the following core entities:
- Users (Replit Auth integration)
- Band members with online/offline status
- Messages for internal band communication
- Songs with metadata (duration, key, tempo, status)
- Events (rehearsals, gigs, recordings, meetings)
- Merchandise items with inventory tracking
- Links for link-in-bio functionality
- Opportunities (gig offers, bookings)
- Fan contacts for mailing lists
- Transactions for financial tracking
- Releases for distribution monitoring
- Daily AI-generated summaries
- Streaming platforms and statistics
- Royalty payments

**Storage Pattern**
- Centralized storage layer in `server/storage.ts`
- Type-safe CRUD operations using Drizzle ORM
- Returns typed objects matching schema definitions
- Uses async/await for all database operations

### External Dependencies

**Payment Processing**
- **PayPal Server SDK** for merchandise checkout
- Supports both sandbox and production environments
- Order creation and capture flow for one-time payments
- Critical integration with strict "do not modify" code sections

**AI & Machine Learning**
- **OpenAI API** for conversational AI assistant (GPT-5 model)
- Text-to-speech (TTS) using OpenAI's `tts-1` model with "alloy" voice
- Speech-to-text transcription via OpenAI Whisper
- Daily summary generation for band activity updates
- Context-aware responses with conversation history (last 10 messages)

**Development Tools**
- **Replit-specific plugins** for development environment integration:
  - Runtime error modal overlay
  - Cartographer for code navigation
  - Dev banner for development mode indicators
- Custom Vite plugin for OpenGraph image meta tag updates

**UI Components**
- **Radix UI** primitives for accessible component foundations
- **Lucide React** for icon library
- **Recharts** for data visualization (charts, graphs)
- **CMDK** for command palette functionality
- **date-fns** for date manipulation

**Form Handling & Validation**
- **React Hook Form** for form state management
- **Zod** for schema validation
- **@hookform/resolvers** for Zod integration with React Hook Form

**Styling**
- **Tailwind CSS** v4 with PostCSS
- **class-variance-authority** for component variant management
- **clsx** and **tailwind-merge** for conditional class composition

**Session Storage**
- **connect-pg-simple** for PostgreSQL-backed Express sessions
- Automatic session table management
- 7-day session expiration

**Security Considerations**
- Environment variables for sensitive credentials (API keys, database URL, session secret)
- HTTP-only, secure cookies for session management
- CORS configuration for API endpoints
- Input validation using Zod schemas
- Rate limiting on API endpoints (express-rate-limit imported but implementation not shown)

**Deployment Environment**
- Designed for Replit hosting platform
- Environment detection via `NODE_ENV` and `REPL_ID` variables
- Custom domain handling for OpenGraph images
- Static file serving in production mode