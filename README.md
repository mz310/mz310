<div align="center">

<picture>
  <source media="(prefers-color-scheme: dark)" srcset="assets/header-dark.svg">
  <img alt="M. Zorigt — production systems, end to end, solo" src="assets/header.svg">
</picture>

<a href="https://crm.tee.education"><img alt="TEMS — live" src="https://img.shields.io/badge/LIVE-crm.tee.education-ffffff?style=flat-square&labelColor=000000"></a>&nbsp;<a href="https://github.com/mz310/anytime"><img alt="ANYTIME — live" src="https://img.shields.io/badge/LIVE-mz310%2Fanytime-ffffff?style=flat-square&labelColor=000000"></a>&nbsp;<a href="https://www.linkedin.com/in/YOUR-HANDLE"><img alt="LinkedIn" src="https://img.shields.io/badge/LINKEDIN-ffffff?style=flat-square&logo=linkedin&logoColor=000000"></a>&nbsp;<a href="mailto:YOU@EXAMPLE.COM"><img alt="Email" src="https://img.shields.io/badge/EMAIL-ffffff?style=flat-square"></a>

</div>

<picture>
  <source media="(prefers-color-scheme: dark)" srcset="assets/s01-dark.svg">
  <img alt="01 — whoami" src="assets/s01.svg">
</picture>

Two systems below are in production today: a CRM running the full enrollment pipeline of
an eight-branch education group, and a geofence-verified membership platform for a 24/7
gym. Both built alone, end to end — schema, API, background jobs, frontend, deploy
pipeline. Currently operating and extending both; open to full-stack roles and contract
work.

<picture>
  <source media="(prefers-color-scheme: dark)" srcset="assets/s02-dark.svg">
  <img alt="02 — TEMS, production CRM" src="assets/s02.svg">
</picture>

<a href="https://crm.tee.education"><img alt="Live at crm.tee.education" src="https://img.shields.io/badge/LIVE-crm.tee.education-ffffff?style=flat-square&labelColor=000000"></a> <img alt="Private repository" src="https://img.shields.io/badge/REPO-PRIVATE-ffffff?style=flat-square&labelColor=000000"> <img alt="Built solo" src="https://img.shields.io/badge/BUILT-SOLO-ffffff?style=flat-square&labelColor=000000">

One system running the pipeline from ad click to signed contract — in daily use by
sales, HR, finance and branch teams at TEE, a private education group.

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

<picture>
  <source media="(prefers-color-scheme: dark)" srcset="assets/tems-telemetry-dark.svg">
  <img alt="71 SQL migrations · 8 background workers · 28 services · 67 test files · 44 ops scripts · 8 RBAC roles" src="assets/tems-telemetry.svg">
</picture>

<sub>Migrations apply automatically on every deploy. All 44 operational scripts are
preview-by-default — nothing writes to production without an explicit `--apply` flag.</sub>

<img alt="TypeScript" src="https://img.shields.io/badge/TypeScript-ffffff?style=flat-square&logo=typescript&logoColor=000000"> <img alt="Express" src="https://img.shields.io/badge/Express-ffffff?style=flat-square&logo=express&logoColor=000000"> <img alt="PostgreSQL" src="https://img.shields.io/badge/PostgreSQL-ffffff?style=flat-square&logo=postgresql&logoColor=000000"> <img alt="Redis" src="https://img.shields.io/badge/Redis-ffffff?style=flat-square&logo=redis&logoColor=000000"> <img alt="BullMQ" src="https://img.shields.io/badge/BullMQ-ffffff?style=flat-square"> <img alt="React 19" src="https://img.shields.io/badge/React_19-ffffff?style=flat-square&logo=react&logoColor=000000"> <img alt="Tailwind 4" src="https://img.shields.io/badge/Tailwind_4-ffffff?style=flat-square&logo=tailwindcss&logoColor=000000"> <img alt="TanStack Query" src="https://img.shields.io/badge/TanStack_Query-ffffff?style=flat-square&logo=reactquery&logoColor=000000"> <img alt="Zod" src="https://img.shields.io/badge/Zod-ffffff?style=flat-square&logo=zod&logoColor=000000"> <img alt="Vitest" src="https://img.shields.io/badge/Vitest-ffffff?style=flat-square&logo=vitest&logoColor=000000"> <img alt="Gemini" src="https://img.shields.io/badge/Gemini-ffffff?style=flat-square&logo=googlegemini&logoColor=000000">

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

<picture>
  <source media="(prefers-color-scheme: dark)" srcset="assets/s03-dark.svg">
  <img alt="03 — ANYTIME, geofenced fitness" src="assets/s03.svg">
</picture>

<a href="https://api.zorigt.live"><img alt="API live" src="https://img.shields.io/badge/API-api.zorigt.live-ffffff?style=flat-square&labelColor=000000"></a> <a href="https://dashboard.zorigt.live"><img alt="Dashboard live" src="https://img.shields.io/badge/DASHBOARD-dashboard.zorigt.live-ffffff?style=flat-square&labelColor=000000"></a> <a href="https://github.com/mz310/anytime"><img alt="Repository" src="https://img.shields.io/badge/REPO-mz310%2Fanytime-ffffff?style=flat-square&labelColor=000000"></a>

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

<picture>
  <source media="(prefers-color-scheme: dark)" srcset="assets/anytime-telemetry-dark.svg">
  <img alt="15 Prisma models · 17 migrations · 13 backend modules · 17 test suites · about five dollars per month of infrastructure" src="assets/anytime-telemetry.svg">
</picture>

<sub>CI on backend and mobile · Android APK distributed through EAS Build.</sub>

<img alt="NestJS" src="https://img.shields.io/badge/NestJS-ffffff?style=flat-square&logo=nestjs&logoColor=000000"> <img alt="Prisma" src="https://img.shields.io/badge/Prisma-ffffff?style=flat-square&logo=prisma&logoColor=000000"> <img alt="PostgreSQL 17" src="https://img.shields.io/badge/PostgreSQL_17-ffffff?style=flat-square&logo=postgresql&logoColor=000000"> <img alt="Expo" src="https://img.shields.io/badge/Expo-ffffff?style=flat-square&logo=expo&logoColor=000000"> <img alt="React Native" src="https://img.shields.io/badge/React_Native-ffffff?style=flat-square&logo=react&logoColor=000000"> <img alt="TypeScript" src="https://img.shields.io/badge/TypeScript-ffffff?style=flat-square&logo=typescript&logoColor=000000"> <img alt="Docker" src="https://img.shields.io/badge/Docker-ffffff?style=flat-square&logo=docker&logoColor=000000"> <img alt="GitHub Actions" src="https://img.shields.io/badge/GitHub_Actions-ffffff?style=flat-square&logo=githubactions&logoColor=000000">

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

<picture>
  <source media="(prefers-color-scheme: dark)" srcset="assets/s04-dark.svg">
  <img alt="04 — stack" src="assets/s04.svg">
</picture>

<picture>
  <source media="(prefers-color-scheme: dark)" srcset="assets/stack-dark.svg">
  <img alt="Backend: TypeScript, Node.js, NestJS, Express, Prisma, Zod, BullMQ, Vitest. Web and mobile: React 19, React Native, Expo, Vite, TanStack Query, Tailwind 4, zustand, Recharts. Data: PostgreSQL, Neon, Redis. Ops: Docker, GitHub Actions, EAS Build, PM2, nginx, DigitalOcean, Vercel, Cloudflare." src="assets/stack.svg">
</picture>

<picture>
  <source media="(prefers-color-scheme: dark)" srcset="assets/s05-dark.svg">
  <img alt="05 — contact" src="assets/s05.svg">
</picture>

<div align="center">

<picture>
  <source media="(prefers-color-scheme: dark)" srcset="assets/footer-dark.svg">
  <img alt="Open to full-stack roles and contract work" src="assets/footer.svg">
</picture>

<a href="https://www.linkedin.com/in/YOUR-HANDLE"><img alt="LinkedIn" src="https://img.shields.io/badge/LINKEDIN-ffffff?style=flat-square&logo=linkedin&logoColor=000000"></a>&nbsp;<a href="mailto:YOU@EXAMPLE.COM"><img alt="Email" src="https://img.shields.io/badge/EMAIL-ffffff?style=flat-square"></a>&nbsp;<a href="https://github.com/mz310"><img alt="GitHub" src="https://img.shields.io/badge/GITHUB-mz310-ffffff?style=flat-square&labelColor=000000"></a>

</div>

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
