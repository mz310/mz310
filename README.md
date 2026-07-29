<div align="center">

# M. Zorigt

**I design, build, deploy and operate production systems — end to end, solo.**

<sub>Ulaanbaatar, Mongolia · UTC+8</sub>

<br>

<a href="https://crm.tee.education"><img alt="TEMS — live in production" src="https://img.shields.io/badge/TEMS-live_in_production-009E73?style=flat-square&labelColor=2f363d"></a>&nbsp;<a href="https://github.com/mz310/anytime"><img alt="ANYTIME — live" src="https://img.shields.io/badge/ANYTIME-live-009E73?style=flat-square&labelColor=2f363d"></a>&nbsp;<a href="https://www.linkedin.com/in/YOUR-HANDLE"><img alt="LinkedIn" src="https://img.shields.io/badge/LinkedIn-2f363d?style=flat-square&logo=linkedin&logoColor=white"></a>&nbsp;<a href="mailto:mzorigt6@gmail.com"><img alt="Email" src="https://img.shields.io/badge/email-2f363d?style=flat-square"></a>

</div>

Two systems below are in production today: a CRM running the full enrollment pipeline of
an eight-branch education group, and a geofence-verified membership platform for a 24/7
gym. Both built alone, end to end — schema, API, background jobs, frontend, deploy
pipeline. Currently operating and extending both; open to full-stack roles and contract
work.

---

## TEMS — enrollment CRM for TEE, an eight-branch education group

<a href="https://crm.tee.education"><img alt="Live at crm.tee.education" src="https://img.shields.io/badge/live-crm.tee.education-009E73?style=flat-square&labelColor=2f363d"></a> <img alt="Private repository" src="https://img.shields.io/badge/repo-private-59636e?style=flat-square&labelColor=2f363d"> <img alt="Built solo" src="https://img.shields.io/badge/built-solo-59636e?style=flat-square&labelColor=2f363d">

One system running the pipeline from ad click to signed contract — in daily use by
sales, HR, finance and branch teams.

Leads used to arrive through five unconnected channels: Meta Lead Ads, Google Forms,
walk-ins, events, and the Google Sheets the sales team already lived in. Nobody could
answer *"how many of last month's ad leads actually enrolled, and at which branch?"* —
duplicate parents were counted twice, and contracts and payments were tracked by hand.

**An LLM only where it earns its cost.** Mongolian names arrive in both Cyrillic and
Latin transliteration. A deterministic matcher — reduced transliteration + token sort +
trigram similarity — resolves the clear cases for free; only the ambiguous middle band
goes to Gemini, and its verdict is accepted only above a confidence threshold.
Otherwise a human decides. Siblings are structurally prevented from ever being merged.

**A merge engine that fails closed.** It refuses to run if a new table references leads
without being registered in its known-FK set — so a future migration cannot silently
orphan data. A loud failure beats silent data loss.

**Meet users where they already are.** Instead of forcing the sales team off
spreadsheets, a BullMQ worker makes the sheet a first-class synced surface: idempotent
import (content-hashed keys, so re-runs are no-ops) plus writeback of CRM state into
the sheet. The adoption problem was solved by architecture.

<img alt="TypeScript" src="https://img.shields.io/badge/TypeScript-2f363d?style=flat-square&logo=typescript&logoColor=white"> <img alt="Express" src="https://img.shields.io/badge/Express-2f363d?style=flat-square&logo=express&logoColor=white"> <img alt="PostgreSQL" src="https://img.shields.io/badge/PostgreSQL-2f363d?style=flat-square&logo=postgresql&logoColor=white"> <img alt="Redis" src="https://img.shields.io/badge/Redis-2f363d?style=flat-square&logo=redis&logoColor=white"> <img alt="BullMQ" src="https://img.shields.io/badge/BullMQ-2f363d?style=flat-square"> <img alt="React 19" src="https://img.shields.io/badge/React_19-2f363d?style=flat-square&logo=react&logoColor=white"> <img alt="Tailwind 4" src="https://img.shields.io/badge/Tailwind_4-2f363d?style=flat-square&logo=tailwindcss&logoColor=white"> <img alt="TanStack Query" src="https://img.shields.io/badge/TanStack_Query-2f363d?style=flat-square&logo=reactquery&logoColor=white"> <img alt="Zod" src="https://img.shields.io/badge/Zod-2f363d?style=flat-square&logo=zod&logoColor=white"> <img alt="Vitest" src="https://img.shields.io/badge/Vitest-2f363d?style=flat-square&logo=vitest&logoColor=white"> <img alt="Gemini" src="https://img.shields.io/badge/Gemini-2f363d?style=flat-square&logo=googlegemini&logoColor=white">

<sub>71 versioned SQL migrations, applied automatically on every deploy · 8 background
workers · 28 backend services · 67 test files · 44 operational scripts, all
preview-by-default — nothing writes to production without an explicit `--apply` flag</sub>

> [!NOTE]
> The repository is private — it holds a client's production data model. I'm happy to
> walk through the architecture and code live.

<details>
<summary>More decisions, and how a lead becomes exactly one clean record</summary>

- **8-state lead lifecycle** enforced by a server-side transition state machine —
  `ENROLLED` is reachable *only* through verified contract ingestion; a status can't be
  faked from the UI.
- **RBAC across 8 roles** on a page × action matrix. Branch-scoped roles are
  hard-filtered at the SQL layer on every read and ownership-checked on every write —
  not merely hidden in the UI.
- **Finance:** grade-derived tuition, discount snapshots, installment schedules, and an
  audit ledger.
- Also in the stack: Neon Postgres · Vite · React Router 7 · Recharts · PM2 · nginx ·
  Meta Lead Ads webhooks · Google Sheets/Forms API · Moodle.

```mermaid
flowchart LR
    FB[Meta Lead Ads] --> IN[Ingest]
    GF[Google Forms] --> IN
    GS[Google Sheets] --> IN
    WK[Walk-ins / events] --> IN
    IN --> DM{Deterministic matcher}
    DM -- clear --> CRM[(CRM)]
    DM -- ambiguous band --> LLM[Gemini]
    LLM -- above threshold --> CRM
    LLM -- below threshold --> HR[Human review]
    HR --> CRM
    CRM -- writeback --> GS
```

</details>

---

## ANYTIME — geofenced membership for a 24/7 gym

<a href="https://api.zorigt.live"><img alt="API live" src="https://img.shields.io/badge/API-api.zorigt.live-009E73?style=flat-square&labelColor=2f363d"></a> <a href="https://dashboard.zorigt.live"><img alt="Dashboard live" src="https://img.shields.io/badge/dashboard-dashboard.zorigt.live-009E73?style=flat-square&labelColor=2f363d"></a> <a href="https://github.com/mz310/anytime"><img alt="Repository" src="https://img.shields.io/badge/repo-mz310%2Fanytime-59636e?style=flat-square&labelColor=2f363d"></a>

Mobile app, REST API and manager dashboard for ANYTIME 24/7 fitness in Ulaanbaatar —
deployed and running on ~$5/month of infrastructure.

A 24/7 gym has no staff at the door for most of the day. Membership check-in has to be
self-service, trustworthy, and impossible to fake from a couch across town.

**Anti-spoofing as layered defence, not a checkbox.** The client checks `coords.mocked`
to catch mock-location apps; the server independently re-runs the Haversine distance
calculation rather than trusting a client-reported "I'm inside". Readings with accuracy
worse than 100 m are rejected outright, and the geofence tolerance scales with reported
accuracy (accuracy / 2, capped at 25 m) instead of one fixed radius.

**Sessions that survive the phone being pocketed.** Background tracking via
`expo-task-manager` with an Android foreground-service notification keeps a workout
session accruing while the app is off-screen. GPS acquisition is wrapped in a 10-second
`Promise.race`, so the UI degrades to an explicit timeout state instead of hanging.

**One backend, three very different clients.** A NestJS + Prisma API serves a React
Native app, a Vite/React manager dashboard, and CI — JWT auth (bcrypt cost 12,
rate-limited to 5 requests/min on auth routes), with the Prisma schema as the single
source of truth across all three.

<img alt="NestJS" src="https://img.shields.io/badge/NestJS-2f363d?style=flat-square&logo=nestjs&logoColor=white"> <img alt="Prisma" src="https://img.shields.io/badge/Prisma-2f363d?style=flat-square&logo=prisma&logoColor=white"> <img alt="PostgreSQL 17" src="https://img.shields.io/badge/PostgreSQL_17-2f363d?style=flat-square&logo=postgresql&logoColor=white"> <img alt="Expo" src="https://img.shields.io/badge/Expo-2f363d?style=flat-square&logo=expo&logoColor=white"> <img alt="React Native" src="https://img.shields.io/badge/React_Native-2f363d?style=flat-square&logo=react&logoColor=white"> <img alt="TypeScript" src="https://img.shields.io/badge/TypeScript-2f363d?style=flat-square&logo=typescript&logoColor=white"> <img alt="Docker" src="https://img.shields.io/badge/Docker-2f363d?style=flat-square&logo=docker&logoColor=white"> <img alt="GitHub Actions" src="https://img.shields.io/badge/GitHub_Actions-2f363d?style=flat-square&logo=githubactions&logoColor=white">

<sub>15 Prisma models · 17 migrations · 13 backend modules · 17 backend test suites ·
CI on backend and mobile · Android APK via EAS Build · total infrastructure ≈ $5/month</sub>

<details>
<summary>Feature surface, and what a check-in actually verifies</summary>

Email-verified JWT auth · geofenced check-in in under 2 seconds · session and streak
points · workout plans · exercise and equipment library · trainer↔member messaging ·
leaderboard · push notifications · manager dashboard.

Built as a B.Sc. thesis project (MUST, 2026) — but deployed against a real gym, not a
demo.

```mermaid
sequenceDiagram
    participant P as Phone
    participant S as Server
    P->>P: reject if coords.mocked
    P->>S: check-in (lat, lng, accuracy)
    S->>S: reject if accuracy > 100 m
    S->>S: re-run Haversine independently
    S->>S: tolerance = accuracy / 2, capped at 25 m
    S-->>P: session started / rejected
```

</details>

---

## Stack

| | |
|---|---|
| **Backend** | <img alt="TypeScript" src="https://img.shields.io/badge/TypeScript-2f363d?style=flat-square&logo=typescript&logoColor=white"> <img alt="Node.js" src="https://img.shields.io/badge/Node.js-2f363d?style=flat-square&logo=nodedotjs&logoColor=white"> <img alt="NestJS" src="https://img.shields.io/badge/NestJS-2f363d?style=flat-square&logo=nestjs&logoColor=white"> <img alt="Express" src="https://img.shields.io/badge/Express-2f363d?style=flat-square&logo=express&logoColor=white"> <img alt="Prisma" src="https://img.shields.io/badge/Prisma-2f363d?style=flat-square&logo=prisma&logoColor=white"> <img alt="Zod" src="https://img.shields.io/badge/Zod-2f363d?style=flat-square&logo=zod&logoColor=white"> <img alt="BullMQ" src="https://img.shields.io/badge/BullMQ-2f363d?style=flat-square"> <img alt="Vitest" src="https://img.shields.io/badge/Vitest-2f363d?style=flat-square&logo=vitest&logoColor=white"> |
| **Web & mobile** | <img alt="React" src="https://img.shields.io/badge/React-2f363d?style=flat-square&logo=react&logoColor=white"> <img alt="React Native" src="https://img.shields.io/badge/React_Native-2f363d?style=flat-square&logo=react&logoColor=white"> <img alt="Expo" src="https://img.shields.io/badge/Expo-2f363d?style=flat-square&logo=expo&logoColor=white"> <img alt="Vite" src="https://img.shields.io/badge/Vite-2f363d?style=flat-square&logo=vite&logoColor=white"> <img alt="TanStack Query" src="https://img.shields.io/badge/TanStack_Query-2f363d?style=flat-square&logo=reactquery&logoColor=white"> <img alt="Tailwind CSS" src="https://img.shields.io/badge/Tailwind-2f363d?style=flat-square&logo=tailwindcss&logoColor=white"> <img alt="zustand" src="https://img.shields.io/badge/zustand-2f363d?style=flat-square"> <img alt="Recharts" src="https://img.shields.io/badge/Recharts-2f363d?style=flat-square"> |
| **Data** | <img alt="PostgreSQL" src="https://img.shields.io/badge/PostgreSQL-2f363d?style=flat-square&logo=postgresql&logoColor=white"> <img alt="Neon" src="https://img.shields.io/badge/Neon-2f363d?style=flat-square&logo=neon&logoColor=white"> <img alt="Redis" src="https://img.shields.io/badge/Redis-2f363d?style=flat-square&logo=redis&logoColor=white"> |
| **Ops & delivery** | <img alt="Docker" src="https://img.shields.io/badge/Docker-2f363d?style=flat-square&logo=docker&logoColor=white"> <img alt="GitHub Actions" src="https://img.shields.io/badge/GitHub_Actions-2f363d?style=flat-square&logo=githubactions&logoColor=white"> <img alt="EAS Build" src="https://img.shields.io/badge/EAS_Build-2f363d?style=flat-square&logo=expo&logoColor=white"> <img alt="PM2" src="https://img.shields.io/badge/PM2-2f363d?style=flat-square&logo=pm2&logoColor=white"> <img alt="nginx" src="https://img.shields.io/badge/nginx-2f363d?style=flat-square&logo=nginx&logoColor=white"> <img alt="DigitalOcean" src="https://img.shields.io/badge/DigitalOcean-2f363d?style=flat-square&logo=digitalocean&logoColor=white"> <img alt="Vercel" src="https://img.shields.io/badge/Vercel-2f363d?style=flat-square&logo=vercel&logoColor=white"> <img alt="Cloudflare" src="https://img.shields.io/badge/Cloudflare-2f363d?style=flat-square&logo=cloudflare&logoColor=white"> |

---

<details>
<summary>🇲🇳 Монголоор</summary>

### TEMS

Найман салбартай хувийн сургалтын байгууллагын элсэлтийн бүх урсгалыг — зарын
даралтаас гэрээ байгуулах хүртэл — хөтөлдөг CRM. Борлуулалт, хүний нөөц, санхүү,
салбарын багууд өдөр бүр ашигладаг.

**Асуудал.** Хүсэлтүүд Meta Lead Ads, Google Forms, биечлэн ирсэн, арга хэмжээ, Google
Sheets гэсэн хоорондоо холбоогүй таван сувгаар ирдэг байв. «Өнгөрсөн сарын зарын
хүсэлтүүдээс хэд нь үнэхээр элссэн бэ, аль салбарт?» гэдэгт хэн ч хариулж чадахгүй,
давхардсан эцэг эх хоёр удаа тоологдож, гэрээ болон төлбөрийг гараар хөтөлж байлаа.

1. **LLM-ийг зөвхөн үнэ цэнээ өгөх газарт нь.** Монгол нэрс кирилл, латин галигаар
   холилдон ирдэг. Тодорхой тохиолдлыг детерминист тааруулагч (галиг хялбарчлал +
   токен эрэмбэ + триграм ижилслэл) үнэгүй шийдэж, зөвхөн эргэлзээтэй дунд хэсгийг
   Gemini-д илгээдэг; итгэлцлийн босго давсан үед л хариуг хүлээн авч, бусад үед хүн
   шийддэг. Ах дүүс хоорондоо нэгтгэгдэхээс бүтцийн түвшинд хамгаалагдсан.
2. **Алдаа гарвал зогсдог нэгтгэлийн хөдөлгүүр.** Шинэ хүснэгт lead-рүү холбогдсон
   мөртлөө бүртгэлтэй FK багцад нь ороогүй бол ажиллахаас татгалздаг — ирээдүйн
   миграци өгөгдлийг чимээгүй орхигдуулж чадахгүй.
3. **Хэрэглэгчдийг байгаа газарт нь угтах.** Борлуулалтын багийг хүснэгтээс нь
   салгахын оронд BullMQ worker Google Sheets-ийг бүрэн синктэй гадаргуу болгосон:
   idempotent импорт (контент-хэшлэсэн түлхүүр — давтан ажиллуулахад өөрчлөлт гарахгүй)
   ба CRM төлөвийг хүснэгт рүү буцааж бичдэг.

### ANYTIME

Улаанбаатар дахь ANYTIME 24/7 фитнесийн гишүүнчлэлийн платформ: мобайл апп, REST API,
менежерийн самбар. Сарын ~5 ам.долларын дэд бүтэц дээр ажиллаж байна.

**Асуудал.** 24/7 фитнес өдрийн ихэнх цагт үүдэндээ ажилтангүй. Гишүүнчлэлийн бүртгэл
өөрөө үйлчилгээтэй, найдвартай, гэрээсээ хуурамчаар хийх боломжгүй байх ёстой.

1. **Хуурамч байршлын эсрэг давхарласан хамгаалалт.** Клиент `coords.mocked`-г шалгаж
   mock-location аппыг барьдаг; сервер клиентэд итгэхийн оронд Haversine зайг өөрөө
   дахин тооцдог. 100 м-ээс муу нарийвчлалтай хэмжилтийг шууд хааж, geofence-ийн
   зөвшөөрөл нарийвчлалтай уялдан (нарийвчлал / 2, дээд тал нь 25 м) өөрчлөгддөг.
2. **Утас халаасанд байхад ч тасрахгүй сесс.** `expo-task-manager` + Android
   foreground-service мэдэгдлээр дасгалын сесс арын горимд үргэлжилдэг. GPS авалт
   10 секундын `Promise.race`-д ороосон тул UI гацахын оронд "timeout" төлөвт ил
   шилждэг.
3. **Нэг backend, гурван өөр клиент.** NestJS + Prisma API нь React Native апп,
   Vite/React самбар, CI гурвыг зэрэг үйлчилдэг; JWT auth (bcrypt cost 12, auth зам
   дээр минутад 5 хүсэлт), Prisma схем нь гурвуулангийн цорын ганц үнэний эх сурвалж.

</details>

---

<div align="center">

<sub>Open to full-stack roles and contract work · <a href="https://www.linkedin.com/in/YOUR-HANDLE">LinkedIn</a> · <a href="mailto:YOU@EXAMPLE.COM">email</a> · Ulaanbaatar, UTC+8</sub>

</div>
