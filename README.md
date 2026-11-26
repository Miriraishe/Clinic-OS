# Clinic OS (COS) — Front-end Prototype

A physiotherapy clinic management prototype with four role-based portals in one app:

| Portal | Demo sign-in email | What it covers |
|---|---|---|
| Reception | `rudo.chikwanha@clinicos.demo` | Dashboard, calendar (drag, resize, conflict checks), patient registration and directory, inventory, suppliers, purchase requests, reports |
| Physiotherapist | `tendai.moyo@clinicos.demo` | Clinical dashboard, schedule, patient records, assessments, SOAP notes, treatment plans, progress reports |
| Clinic Owner | `nyasha.chuma@clinicos.demo` | Overview, purchase approvals, staff and roles, read-only audit log, reception desk |
| IT Manager | `kuda.mutasa@clinicos.demo` | Service desk simulator, team, assets, system health, user accounts, audit log |

Any password works. **The email decides which portal opens.** This is a demo, not real authentication.

> **All patient, staff, supplier and contact data is fictional.** Names, IDs, phone numbers (`+263 77 000 01xx`), emails (`example.com` / `clinicos.demo`) and insurers are made up. Do not enter real patient information: this build has no backend, no encryption and no real access control.

## Files

```
clinic-os/
├── index.html                       Page shell (links the CSS and JS)
├── css/styles.css                   Design tokens and all styles
├── js/app.js                        All application code (one script)
├── standalone/clinic-os-standalone.html   Same app as ONE file (CSS + JS inlined)
└── README.md
```

`index.html` + `css/` + `js/` is the project to edit. The standalone file is identical, handy for quick sharing or pasting into a host that accepts a single file.

## Run locally

No build step or dependencies. Either:

- Double-click `standalone/clinic-os-standalone.html`, or
- Serve the folder (recommended for the split version):

```bash
cd clinic-os
python3 -m http.server 8080
# open http://localhost:8080
```

An internet connection is only needed for the Google Fonts (Fraunces, IBM Plex Sans). Without it the app falls back to system fonts.

## Publish

It is a static site, so any static host works. Upload the folder contents (or just the standalone file).

- **GitHub Pages:** push the folder to a repository, then Settings → Pages → deploy from the branch root.
- **Netlify:** drag the `clinic-os` folder onto app.netlify.com/drop.
- **Vercel / Cloudflare Pages:** import the repository, framework preset "Other", no build command, output directory `.`.
- **Any web server:** copy the files to the public web root.

Before sharing publicly, keep the fictional-data notice in place and don't connect it to real patient records.

## Where things live in `js/app.js`

| Section | What it does |
|---|---|
| `seedState()` | Fictional receptionist data: patients, appointments, inventory, suppliers, purchase requests |
| `seedClinical()` | Fictional clinical data: assessments, SOAP notes, treatment plans, report (3 full patient journeys) |
| `seedAdmin()` | Fictional users, IT agents, assets, services, service desk tickets |
| `renderDashboard` … `renderInventory` | Reception screens |
| `tDashboard` … `tReportPanel` | Physiotherapist screens |
| `aOwner`, `aApprovals`, `aUsers`, `aAudit` | Owner screens |
| `aServiceDesk`, `aTeam`, `aAssets`, `aHealth` | IT screens and the ticket simulation (`simStep`) |
| `ADMIN_NAV`, `VIEWS_FOR`, `T_NAV`, `applyRole` | Per-role menus and which views each role can open |

Seed dates are relative to today, and are shifted forward automatically on later visits so the demo always has appointments for the current day.

## Data and reset

All data is kept in the browser's `localStorage` (key `cos_demo_state_v2`), per browser. To reset the demo, clear site data for the page or run `localStorage.removeItem("cos_demo_state_v2")` in the console and reload.

## What this prototype does not have

- No backend, database or real authentication. Role permissions are enforced in the UI only, which is not security.
- No PDF generation. "Export PDF" uses the browser print dialog (choose Save as PDF).
- Not included: custom assessment templates, reassessment comparison, therapist notifications, session timeout, billing.

## Turning it into a real system

The brief targets React, TypeScript, Tailwind, shadcn/ui and Supabase (PostgreSQL, Auth, Row Level Security). A sensible path:

1. Create tables for the entities the code already models (`patients`, `appointments`, `inventory_items`, `suppliers`, `purchase_requests`, `assessments`, `soap_notes`, `treatment_plans`, `progress_reports`, `audit_logs`, and so on) with foreign keys, timestamps and audit columns.
2. Replace `loadState` / `saveState` with data-access functions against your API.
3. Enforce roles with Row Level Security so clinical tables are readable only by authorised roles. Reception, IT and Owner must not read clinical notes.
4. Replace the demo sign-in with real authentication and MFA.
5. Port the screens to components, one portal at a time.
6. Have the data protection and clinical safety requirements for your jurisdiction reviewed before storing real patient data.
