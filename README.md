# 🛒 Thai E-Commerce Platform

เว็บขายสินค้าออนไลน์สำหรับตลาดไทย — Next.js + PostgreSQL + Omise

## Tech Stack

| Layer | Technology |
|-------|-----------|
| Frontend | Next.js 14 (App Router), TypeScript, Tailwind CSS, shadcn/ui |
| Backend | Next.js API Routes, Prisma ORM |
| Database | PostgreSQL |
| Cache | Upstash Redis |
| Auth | NextAuth.js v5 (LINE, Google, OTP/SMS) |
| Payments | Omise (บัตรเครดิต + PromptPay QR) |
| Email | Resend |
| Search | Meilisearch |
| Hosting | Vercel + Supabase |
| CI/CD | GitHub Actions |

## Prerequisites

- Node.js >= 20.x
- pnpm >= 9.x (`npm install -g pnpm`)
- PostgreSQL 15+
- Redis (local หรือ Upstash)
- Meilisearch (Docker)

## Quick Start

### 1. Clone & Install

```bash
git clone https://github.com/snoopycaty8-cmyk/WebEc.git
cd WebEc
pnpm install
```

### 2. Environment Variables

```bash
cp .env.example .env.local
# แก้ไขค่าใน .env.local
```

### 3. Database Setup

```bash
# รัน PostgreSQL ด้วย Docker
docker run --name webec-db \
  -e POSTGRES_PASSWORD=password \
  -e POSTGRES_DB=webec \
  -p 5432:5432 -d postgres:15

# Run migrations
pnpm prisma migrate dev

# Seed ข้อมูลตัวอย่าง
pnpm prisma db seed
```

### 4. Meilisearch (ค้นหา)

```bash
docker run -d --name webec-search \
  -p 7700:7700 \
  getmeili/meilisearch:latest
```

### 5. Start Development

```bash
pnpm dev
```

เปิด http://localhost:3000

## Scripts

```bash
pnpm dev          # Development server
pnpm build        # Production build
pnpm start        # Production server
pnpm lint         # ESLint
pnpm test         # Unit + Integration tests (Jest)
pnpm test:e2e     # E2E tests (Playwright)
pnpm test:watch   # Watch mode

pnpm prisma studio          # Prisma GUI
pnpm prisma migrate dev     # Create migration
pnpm prisma migrate deploy  # Apply migrations (production)
pnpm prisma db seed         # Seed data
```

## Project Structure

```
WebEc/
├── prisma/
│   ├── schema.prisma        # Database schema
│   ├── migrations/          # Migration files
│   └── seed.ts              # Seed data
├── src/
│   ├── app/
│   │   ├── (shop)/          # หน้าร้าน (customer-facing)
│   │   │   ├── page.tsx     # หน้าแรก
│   │   │   ├── products/
│   │   │   ├── cart/
│   │   │   ├── checkout/
│   │   │   └── orders/
│   │   ├── (admin)/         # หลังบ้าน (admin)
│   │   │   ├── dashboard/
│   │   │   ├── products/
│   │   │   ├── orders/
│   │   │   └── reports/
│   │   └── api/             # API Routes
│   │       ├── auth/
│   │       ├── v1/
│   │       │   ├── products/
│   │       │   ├── cart/
│   │       │   ├── orders/
│   │       │   └── admin/
│   │       └── webhooks/
│   │           └── omise/
│   ├── components/
│   │   ├── ui/              # shadcn/ui base components
│   │   ├── shop/            # Shop-specific components
│   │   └── admin/           # Admin-specific components
│   ├── lib/
│   │   ├── prisma.ts        # Prisma client singleton
│   │   ├── auth.ts          # NextAuth config
│   │   ├── omise.ts         # Omise client
│   │   ├── redis.ts         # Redis client
│   │   ├── search.ts        # Meilisearch client
│   │   └── utils.ts         # Shared utilities
│   ├── hooks/               # React hooks
│   └── types/               # TypeScript types
├── docs/
│   ├── api-spec.yaml        # OpenAPI 3.1 spec
│   ├── branch-strategy.md   # Git workflow
│   └── env-vars.md          # Environment variables guide
├── tests/
│   ├── unit/
│   ├── integration/
│   └── e2e/                 # Playwright tests
├── .github/
│   ├── workflows/
│   │   ├── ci.yml
│   │   └── deploy.yml
│   └── pull_request_template.md
└── README.md
```

## Payment Integration

### Omise Setup
1. สร้างบัญชีที่ https://dashboard.omise.co
2. ไปที่ Settings → Keys
3. Copy Public Key และ Secret Key ใส่ใน `.env.local`
4. ตั้ง Webhook URL: `https://yourdomain.com/api/webhooks/omise`

### PromptPay QR
ระบบจะสร้าง QR โดยอัตโนมัติผ่าน Omise API เมื่อลูกค้าเลือก PromptPay

## Deployment

### Vercel (Recommended)

```bash
# Install Vercel CLI
npm i -g vercel

# Deploy
vercel --prod
```

ดู [Deployment Playbook](docs/deployment-playbook.md) สำหรับรายละเอียด

## Contributing

ดู [Branch Strategy](docs/branch-strategy.md) สำหรับ workflow

## License

Private — All rights reserved
