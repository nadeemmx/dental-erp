# DentalERP - Dental Clinic Management System

A comprehensive, full-stack dental clinic management platform built with modern web technologies. DentalERP demonstrates expertise in full-stack development, database design, API architecture, and AI integration — covering the complete patient lifecycle from appointment scheduling to billing and clinical workflows.

## Project Overview

DentalERP is a multi-tenant Software-as-a-Service platform designed for dental clinics and multi-branch hospital chains. The system manages the complete patient journey, from initial appointment through treatment, billing, and follow-up care. The architecture showcases scalable SaaS patterns including hospital-scoped data isolation, role-based access control, and integration with external services.

**Note**: This is the public portfolio edition. Some advanced features and configuration details are kept private for the complete commercial edition.

## Core Modules

### Patient Management
- Patient records with medical history and dental charting
- Document uploads and storage
- Patient portal for online booking and record access

### Appointment Scheduling
- Calendar view with slot management
- Appointment reminders and no-show prediction
- Check-in/check-out functionality

### Treatment Planning
- Treatment plan creation and tracking
- Procedure catalog with auto-generated codes
- Progress tracking and follow-up

### Billing & Invoicing
- GST-compliant invoice generation
- Payment tracking and payment plans (EMI)
- Integration with payment gateways (Razorpay, PhonePe, Paytm)

### Inventory Management
- Stock tracking with low-stock alerts
- Supplier management
- Batch and expiry tracking

### Staff Management
- Role-based access (ADMIN, DOCTOR, RECEPTIONIST, LAB_TECH, ACCOUNTANT)
- Shift scheduling and attendance tracking
- Performance and attendance reporting

### AI-Powered Features
- 16 built-in AI skills for clinical assistance, billing, inventory forecasting, and patient segmentation
- OpenRouter multi-model gateway for AI model selection
- AI-assisted treatment advice and intelligent scheduling

### Tele-Dentistry
- Video consultation integration
- Virtual consultation rooms
- Consultation documentation

### Laboratory Management
- Lab order creation and tracking
- Vendor management
- Status tracking and history

### Insurance & Claims
- Insurance policy management
- Claim submission and adjudication
- Policy verification and coverage tracking

## Technology Stack

| Layer | Technology |
|-------|-----------|
| Framework | Next.js 16 (App Router) |
| Language | TypeScript 5 |
| Database | MySQL 8 via Prisma 5 ORM |
| Auth | Better Auth (NextAuth v5 beta) with credentials provider |
| UI | Tailwind CSS 3 + Radix UI + shadcn/ui |
| Forms | React Hook Form + Zod validation |
| Charts | Recharts |
| AI | OpenRouter (multi-model gateway) |
| Email | Nodemailer (SMTP) |
| Testing | Vitest + Playwright + Testing Library |
| CI/CD | GitHub Actions |
| Deployment | Docker + Caddy (reverse proxy) |

## System Architecture

DentalERP uses a **multi-tenant SaaS architecture** with hospital-scoped data isolation. Each clinic (Hospital) is an independent entity with its own data, configuration, and users. All database queries are scoped by `hospitalId` to ensure complete data isolation between clinics.

**Key architectural patterns:**
- **App Router**: All pages use Next.js App Router (`app/` directory) for optimal rendering and data fetching
- **Auth**: Better Auth with hospital-scoped sessions and role-based access control
- **API Pattern**: Route handlers in `app/api/`, with `requireAuthAndRole()` for role gating
- **Database**: Prisma ORM with MySQL, all queries scoped by `hospitalId` for multi-tenancy
- **AI Skills**: Defined in `lib/ai/`, using OpenRouter for model access and cost optimization
- **UI**: shadcn/ui components in `components/ui/`, Tailwind CSS for styling
- **Storage**: Configurable local disk or S3-compatible storage for uploads
- **Queue**: BullMQ for background job processing (email, SMS, notifications)

## Project Structure

```
dental-erp/
├── app/                    # Next.js App Router
│   ├── (auth)/             # Authentication pages (login, signup, etc.)
│   ├── (dashboard)/        # Dashboard pages (all modules)
│   └── api/                # API routes (40+ endpoints)
├── components/             # Reusable React components
│   ├── layout/             # Dashboard shell, sidebar, header
│   ├── ui/                 # shadcn/ui components
│   └── imaging/            # Dental imaging components
├── lib/                    # Utilities, helpers, AI skills
│   ├── ai/                 # AI skill definitions (16 skills)
│   ├── api-helpers.ts      # Auth & API utilities
│   └── prisma.ts           # Prisma client singleton
├── prisma/
│   ├── schema.prisma       # Complete database schema (150+ models)
│   └── seed.ts             # Database seeder
├── tests/                  # Comprehensive test suite
│   ├── unit/               # 400+ unit tests (business logic, utilities, AI skills)
│   ├── integration/        # API route tests with mocked Prisma
│   ├── components/         # React component tests with Testing Library
│   └── e2e/                # Playwright E2E tests (50+ specs)
├── .github/
│   └── workflows/          # CI, E2E, CodeQL and release pipelines
└── public/                 # Static assets
```

## Key API Endpoints

**Authentication:**
- `POST /api/auth/[...nextauth]` — Login and session management
- `POST /api/public/signup` — Clinic signup (no auth required)
- `POST /api/public/verify-email` — Email verification

**Patients:**
- `GET /api/patients` — List patients with pagination and search
- `POST /api/patients` — Create new patient
- `GET /api/patients/[id]` — Fetch patient by ID with cross-hospital isolation

**Appointments:**
- `GET /api/appointments` — List appointments with filters
- `POST /api/appointments` — Create appointment with slot conflict detection
- `POST /api/appointments/[id]/check-in` — Check-in SCHEDULED/CONFIRMED

**Billing & Payments:**
- `GET /api/invoices` — List invoices with GST calculation
- `POST /api/payments/create-order` — Payment order creation (Razorpay/PhonePe/Paytm)
- `POST /api/payments/verify` — Payment signature verification

**AI Routes:**
- `POST /api/ai/chat` — Chat completion with skill selection
- `POST /api/ai/query` — NL→Prisma query with injection prevention
- `POST /api/ai/command` — Command execution with permission check

**Full API surface**: Over 100 endpoint routes covering all system modules.

## Database Structure

The database uses **Prisma ORM** with MySQL 8, featuring 150+ model definitions across these main entities:

- **Hospital** — Clinic entity with multi-tenancy support (plan, limits, settings)
- **User** — Staff users with roles (ADMIN, DOCTOR, RECEPTIONIST, LAB_TECH, ACCOUNTANT)
- **Patient** — Patient records with medical history and risk scoring
- **Appointment** — Appointment scheduling with virtual/physical status
- **Treatment** — Treatment records with procedures and diagnosis
- **Invoice** — Billing invoices with GST compliance and payment tracking
- **Payment** — Payment records with gateway integration (razorpay/phonepe/paytm)
- **Inventory** — Stock items with SKU, categories, supplier tracking
- **Staff** — Employee records with qualifications and shifts
- **AIConversation** — AI interaction history and context

**Key relationships:**
- Multi-tenant isolation via `hospitalId` on all relevant models
- Role-based access control through `Role` enum and `isHospitalAdmin` flags
- Procedural workflows through treatment plans, prescriptions, and invoices

## Authentication

**Better Auth (NextAuth v5 beta)** with credentials provider:

- **Hospital-scoped sessions**: Each user belongs to a specific clinic
- **Role-based access**: ADMIN > DOCTOR > RECEPTIONIST > LAB_TECH > ACCOUNTANT
- **Email verification**: Optional email verification flow
- **Onboarding**: First-time clinic onboarding flow with profile setup
- **Mobile/Bear tokens**: Support for mobile applications via Bearer tokens

**Public routes (no auth required):**
- `/login`, `/signup`, `/forgot-password`, `/verify-email`
- `/portal` — Patient portal access
- `/pay` — Payment page
- `/api/public/[slug]/doctors` — Public doctor listing
- `/api/public/[slug]/slots` — Public slot availability
- `/api/public/[slug]/book` — Public appointment booking

## Installation

### Prerequisites
- Node.js 20 or later
- npm 10 or later
- MySQL 8.0+
- Docker (optional, for backing services)

### Setup

```bash
# 1. Clone the repository
git clone https://github.com/nadeemmx/dental-erp.git
cd dental-erp

# 2. Install dependencies
npm install

# 3. Environment configuration
cp .env.example .env
# Edit .env with your configuration values
# - Generate NEXTAUTH_SECRET: node -e "console.log(require('crypto').randomBytes(32).toString('base64'))"
# - Generate ENCRYPTION_KEY: node -e "console.log(require('crypto').randomBytes(32).toString('hex'))"
# - Generate CRON_SECRET: node -e "console.log(require('crypto').randomBytes(16).toString('hex'))"
# - Set DATABASE_URL to your MySQL connection string

# 4. Database setup
npx prisma migrate deploy
npx prisma db seed

# 5. Start the development server
npm run dev
```

**The application runs on http://localhost:3000**

### Docker Development

```bash
# Start backing services (MySQL, Redis, MinIO, Mailpit)
docker compose -f docker-compose.dev.yml up -d

# Start the Next.js development server (on host, not in container)
npm run dev
```

### Production Deployment

For production deployment, refer to the full [SELF_HOSTING.md](SELF_HOSTING.md) guide which covers:
- TLS termination with Caddy and Let's Encrypt
- Database backup and restore procedures
- Multi-replica deployment patterns
- Storage driver configuration (local vs S3)
- Upgrade and rollback procedures

## Testing

The project has comprehensive test coverage:

- **Unit tests** — Business logic, utilities, AI skills (400+ tests)
- **Integration tests** — API route handlers with mocked Prisma
- **Component tests** — React components with Testing Library
- **E2E tests** — Full user flows with Playwright (50+ specs)

```bash
# Run all unit/integration tests
npm test

# Run with coverage report
npm run test:coverage

# Run E2E tests (requires running server)
npm run test:e2e

# Run all tests
npm run test:all
```

## Screenshots & Demo

*(Screenshots and demo GIFs would be added here for the public portfolio)*

## Public Edition vs Private Edition

This is the **public portfolio edition** — a carefully selected subset of the complete DentalERP system. The private full edition includes additional modules and features:

**Available in public edition:**
- Core patient management and appointment scheduling
- Basic billing and invoicing with GST
- Staff role-based access control
- 8 of 16 AI skills (treatment advisor, smart scheduler, billing agent, patient intake)
- Patient portal with OTP authentication
- Public appointment booking
- API ecosystem with 60+ endpoints
- Full test suite (unit + integration + component)
- Clean architecture and codebase

**Exclusive to private edition:**
- Advanced AI skills (8 additional skills)
- Full inventory management with batch tracking
- Lab integration and vendor management
- Complete insurance & claims processing
- CRM & loyalty programs
- IoT device integration
- Sterilization tracking and compliance
- Advanced reporting and analytics
- All payment gateway configurations
- Multi-branch hospital scaling features
- Full Prisma migration history
- Development and production configurations with real secrets

## License

This project is licensed under the **MIT License** — see the [LICENSE](LICENSE) file for details.

**Copyright (c) 2026 Nadeem Qureshi**

## Maintainer

**Project Owner / Maintainer**: Nadeem Qureshi (verified GitHub account: nadeemmx)

For questions, feedback, or business inquiries, please contact via the GitHub profile.