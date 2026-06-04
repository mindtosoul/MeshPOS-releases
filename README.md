# MeshPOS Releases

MeshPOS is an offline-first ERP and point-of-sale platform built for apparel retail across multiple outlets. Every till runs as a standalone Windows app, so billing, inventory, customers and returns all keep working on the local machine even when the internet drops. In the background, a sync engine quietly keeps each outlet lined up with a shared cloud backbone. Owners run the whole network from one secure web admin, where they manage master data, staff, pricing and reporting for every location in a single place.

This repo holds the packaged Windows installers and the auto-update files for the MeshPOS desktop client. The application source code lives in a separate private repository.

## Tech Stack

- Electron for the desktop shell
- React and TypeScript for the interface
- SQLite with Prisma as the local database, one per till
- Supabase (Postgres with RLS) for cloud auth and cross-outlet sync
- Next.js on Vercel for the web admin

## Architecture Highlights

- **Local first:** reads and writes go straight to local SQLite. The cloud is there to sync, never something the billing screen has to wait on.
- **Multi-outlet sync:** the sync engine is idempotent and non-blocking. It reconciles each till with the cloud and across outlets, tracks sync state per record, and can safely replay without creating duplicates.
- **Ledger-based stock:** stock is never a column you overwrite. Quantities are worked out from an immutable movement ledger and sped up with periodic snapshots, so every outlet keeps a clean audit trail.
- **Audit-safe books:** financial records are soft-deleted, not removed, and every change leaves an audit log entry. Invoice corrections go through a single controlled cloud procedure.
- **Roles that actually gate:** admin, manager and cashier roles are enforced by RLS in the cloud and checked on every till.

## Key Features

- Offline-first billing, product lookup and customer creation that need no internet
- Multiple outlets, each with its own local database, reconciled to a shared cloud
- Background cloud sync that is idempotent and never blocks the counter
- GST billing with the apparel rate worked out per piece automatically
- Returns and exchanges with a full audit trail
- Customer loyalty points earned and redeemed at the counter
- Barcode scanning and label printing
- Thermal-printer-ready receipts
- An admin-only web dashboard for master data, staff and settings across outlets

## Releases

Each release here ships the Windows installer (`MeshPOS-Setup-x.y.z.exe`) together with the `latest.yml` file the app reads to check for updates.

Every till checks this repo for new releases on its own, both at startup and every so often while it runs. When there is a newer version it downloads quietly in the background and installs on the next restart, so outlets stay up to date without anyone reinstalling anything and without interrupting a shift.

For a first install, or to update a till that came before auto-update support, grab the latest `MeshPOS-Setup-x.y.z.exe` from the [Releases page](../../releases) and run it.
