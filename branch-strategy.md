# Git Branch Strategy & Workflow

## Branch Model: GitHub Flow (ปรับแต่งสำหรับทีม 3 คน)

```
main ──────────────────────────────────────────► production
  └─ develop ────────────────────────────────── staging
       ├─ feature/FE-101-product-listing
       ├─ feature/BE-201-cart-api
       ├─ fix/FE-305-mobile-checkout-layout
       └─ hotfix/PROD-001-payment-webhook
```

## Branch Types

| Branch | Pattern | Base | Merge To | Deploy |
|--------|---------|------|----------|--------|
| `main` | — | — | — | Production |
| `develop` | — | main | main (weekly) | Staging |
| Feature | `feature/{scope}-{id}-{desc}` | develop | develop | Preview |
| Fix | `fix/{scope}-{id}-{desc}` | develop | develop | Preview |
| Hotfix | `hotfix/PROD-{id}-{desc}` | main | main + develop | Production |
| Release | `release/v{x.y.z}` | develop | main | Staging → Prod |

**Scope codes:** `FE` = Frontend, `BE` = Backend, `DB` = Database, `CI` = DevOps

## Naming Examples
```
feature/FE-101-product-listing
feature/BE-201-cart-api
feature/BE-203-omise-payment
feature/FE-301-admin-dashboard
fix/FE-305-mobile-checkout-overflow
fix/BE-412-cart-quantity-race-condition
hotfix/PROD-001-payment-webhook-signature
release/v1.0.0
```

## Commit Message Convention (Conventional Commits)

```
<type>(<scope>): <description in Thai or English>

[optional body]
[optional footer: BREAKING CHANGE, Closes #123]
```

**Types:**
- `feat` — ฟีเจอร์ใหม่
- `fix` — แก้บัก
- `refactor` — เปลี่ยน code โดยไม่เพิ่มฟีเจอร์/แก้บัก
- `test` — เพิ่ม/แก้ tests
- `docs` — เอกสาร
- `style` — formatting, whitespace
- `chore` — build, deps, CI
- `perf` — performance improvements

**Examples:**
```
feat(cart): เพิ่มการใช้คูปองส่วนลด (#US-202)
fix(auth): แก้ OTP ไม่ส่ง SMS เมื่อเบอร์มี +66
test(checkout): เพิ่ม E2E test สำหรับ PromptPay flow
chore(deps): อัปเดต Prisma 5.x → 5.9
```

## Pull Request Rules

### PR Template (`.github/pull_request_template.md`)
```markdown
## ทำอะไร (What)
<!-- อธิบายสิ้นที่เปลี่ยนแปลง -->

## ทำไม (Why)
<!-- Story/Issue: #US-XXX -->

## วิธีทดสอบ (How to test)
1. 
2. 

## Checklist
- [ ] Tests ผ่านทั้งหมด
- [ ] ไม่มี console.log ค้างอยู่
- [ ] Schema migration ถูกต้อง (ถ้ามี)
- [ ] Environment variables อัปเดตใน `.env.example`
- [ ] Screenshot/Video (สำหรับ UI changes)
```

### PR Requirements
- **1 Approval** ขั้นต่ำ ก่อน merge
- CI ต้องผ่าน (lint + test + build)
- ห้าม squash commit บน `main`
- Review ภายใน **24 ชั่วโมง** (working hours)
- Merge strategy: **Squash and merge** สำหรับ feature, **Merge commit** สำหรับ release

## Protected Branch Rules

### `main`
- ❌ Direct push ไม่ได้
- ✅ ต้องผ่าน PR + 1 approval
- ✅ CI ต้องผ่าน
- ✅ Linear history (no merge commits จาก feature)

### `develop`
- ❌ Direct push ไม่ได้ (ยกเว้น minor fixes)
- ✅ ต้องผ่าน PR
- ✅ CI ต้องผ่าน

## Release Process

```bash
# 1. สร้าง release branch จาก develop
git checkout develop && git pull
git checkout -b release/v1.0.0

# 2. อัปเดต version
npm version minor  # หรือ major/patch
git push origin release/v1.0.0

# 3. Deploy to staging → รัน UAT

# 4. Merge to main
gh pr create --base main --title "Release v1.0.0"

# 5. Tag release
git tag -a v1.0.0 -m "Release v1.0.0"
git push --tags

# 6. Merge back to develop
git checkout develop && git merge main
```

## Hotfix Process (กรณีฉุกเฉิน Production)

```bash
# 1. Branch จาก main (ไม่ใช่ develop!)
git checkout main && git pull
git checkout -b hotfix/PROD-001-payment-webhook

# 2. แก้บัก + เขียน test

# 3. PR → main (fast track — review ภายใน 2 ชั่วโมง)

# 4. Deploy ทันที

# 5. Merge back to develop
git checkout develop
git merge hotfix/PROD-001-payment-webhook
```

## Environment Variables

ทุก env var ใหม่ต้อง:
1. เพิ่มใน `.env.example` (ค่า placeholder)
2. เพิ่มใน GitHub Secrets (staging + production)
3. ระบุใน `docs/env-vars.md`

```env
# .env.example
DATABASE_URL="postgresql://user:pass@localhost:5432/webec"
NEXTAUTH_SECRET="your-secret-here"
NEXTAUTH_URL="http://localhost:3000"
LINE_CLIENT_ID=""
LINE_CLIENT_SECRET=""
GOOGLE_CLIENT_ID=""
GOOGLE_CLIENT_SECRET=""
OMISE_PUBLIC_KEY=""
OMISE_SECRET_KEY=""
OMISE_WEBHOOK_SECRET=""
SMS_PROVIDER_API_KEY=""  # สำหรับ OTP
REDIS_URL="redis://localhost:6379"
NEXT_PUBLIC_APP_URL="http://localhost:3000"
```
