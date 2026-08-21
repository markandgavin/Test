# Vending Machine QC Form

A web app for running QC on vending machines. Forms are stored **in this GitHub repo**, so
anyone with the link sees the same shared list of machines and can open any report — while people
with a GitHub token can create and edit them.

## How it works

- **Reading is public:** the app lists machines from `index.json` and loads each form from
  `forms/<id>.json` (read straight from the repo). Anyone with the app link can view and download
  reports — no login.
- **Writing needs a token:** creating/editing a form commits those JSON files to the repo via the
  GitHub API, using a token you paste into the app's ⚙ Settings. The token is stored **only in your
  browser** (localStorage) and is never written into the repo.
- Photos are downscaled (~1600px) and embedded in the form JSON, so each form is self-contained.

## One-time setup

1. **Host the app (GitHub Pages).** Make the repo public, then Settings → Pages → Deploy from a
   branch → `claude/vending-machine-qc-form-hkzq5d` → `/root`. Your link is
   `https://markandgavin.github.io/Test/`.
2. **Create a GitHub token** (fine-grained): GitHub → Settings → Developer settings → Personal
   access tokens → Fine-grained tokens → Generate new token → Repository access: **Only select
   repositories → markandgavin/Test** → Permissions → Repository → **Contents: Read and write** →
   Generate.
3. In the app, tap **⚙ → paste the token → Save**. Now you can create and edit machines.

## Using it

- **Home** lists every machine (site number, last-6 serial, photo progress, date). Tap one to open.
- **＋ New machine** → Site number → Serial (photo + last 6) → the photo checklist → **Review**.
- On Review: **Save PDF** (Share sheet / Print on iPhone, direct download on desktop) and
  **Copy share link** (a `?id=…` link straight to that one form).
- Share your Pages link with anyone — they'll see all machines and can download reports.

## Notes

- **Public data:** anyone with the link can view/download forms, and (per this setup) anyone with a
  token can add/edit/delete. Fine for internal equipment records; not for anything sensitive.
- New forms appear for other viewers within a minute or two (GitHub's raw content cache).
- Editing the checklist: change the `CHECKLIST` array near the top of the `<script>` in
  `index.html`.
