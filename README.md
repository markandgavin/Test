# Vending Machine QC Form

A single self-contained web page for running the **final QC** on vending machines in the field.
It walks you through a photo checklist per machine and generates a PDF report. QCs are saved to
the shared backend (the same Supabase project as the build system) and show up in the review
dashboard under the **Final checklist** filter, so a connection is required.

## How to use

1. Open `index.html` on your phone. Best experience: open it once in Safari/Chrome and use
   **Add to Home Screen** so it launches like an app and keeps your saved machines.
2. **Home screen** — your saved machines, newest first, each showing the site number, last-6
   serial, photo progress (e.g. `6/9`), and date.
   - **＋ New machine** starts a new QC.
   - **Tap a machine** to open it and keep working (or to save its PDF).
   - **🗑** deletes a machine (asks first).
3. **Per machine**, walk the steps:
   - **Site number** — the key identifier (e.g. "82").
   - **Serial** — photograph the full serial number, then type the **last 6** as a quick
     reference.
   - **Photo checklist** — one photo each: Peplink, OptConnect, Nuc, Badge, Inside, Outside,
     Keys, Crate with label.
   - **Review** — check all photos (tap a tile to redo/add one), then **Save PDF**.
   - Use **‹ Machines** (top-left) any time to jump back to the list. Your progress is saved
     automatically as you go.

## Saving the PDF (esp. on iPhone)

Tapping **Save PDF** uses the best method your phone supports:

- **Share sheet** (iPhone/Android) → choose **Save to Files**, Mail, AirDrop, etc.
- If sharing isn't available, it falls back to your browser's **Print → Save as PDF**
  (reliable on iPhone).
- On desktop/Android Chrome it downloads the `.pdf` directly.

The report is one PDF per machine: a cover page headlined with the **Site number** (plus the
last-6 serial and date), then one labeled photo per page — full serial number first, then each
component. Saved as `QC_Site-<site>_<last6>.pdf`.

## Where your data lives

Machines and photos are stored in the **shared Supabase backend** as `builds` with `kind='final'`
(plus `build_steps`, photos in the `photos` bucket under `final/<id>/…`). Everyone who opens the
page sees the same list, and each machine appears in the review dashboard's **Final checklist**
filter. There's a **Submit to review** button on the review screen to flag a QC as submitted.

## Editing the checklist

The checklist is a plain array near the top of the `<script>` in `index.html`:

```js
var CHECKLIST = [
  { id: "peplink",    label: "Peplink" },
  { id: "optconnect", label: "OptConnect" },
  ...
];
```

Add, remove, or reorder items there. Each `id` must be unique; `label` is what's shown in the
wizard and the PDF.

## How the PDF works

The PDF is generated entirely in the browser with no external libraries. Photos are downscaled on
capture (~1600px max edge) and re-encoded as JPEG, which embeds directly into the PDF — so the
report is self-contained, works offline, and stays reasonably small.

---

# Machine Build system (employee + admin, shared via Supabase)

A separate, cloud-backed system for guided machine assembly and live review. Unlike the on-device
`index.html` above, these share data through a dedicated Supabase project so everyone sees the same
builds.

- **`build.html`** — the employee app. Enter name + site, then for each step (serial plaque,
  Tray 1–5, **Rail positions**, side connectors, outside) view the reference photo and upload a
  photo of your build. Each photo step offers **📷 Take photo** (camera) or **🖼️ From library**.
  On the review screen, any **crate labels** the reviewer attached to this machine appear as
  download links. Everything uploads to Supabase.
- **`review.html`** — the admin dashboard. Watch builds come in live, open one to compare each
  step's photo against the reference, and mark **Pass / Needs-fix** with notes. A **Reference
  guide** tab lets the admin upload the reference image for each step once (all employees then see
  them). In a build's detail view, **Crate labels** lets the reviewer attach label files (PDF or
  image) to that machine; the builder can then download them from their app. A **Site ID** tag
  can be set per machine from the build detail (just above Crate labels); it shows in the machine
  title in both apps. **Time to complete** is tracked in the backend: a database trigger stamps
  `submitted_at` when a build is submitted (and `completed_at` when it's passed), so the dashboard
  shows how long each build took (or elapsed time while still in progress). A **Builder / Final
  checklist** filter at the top of the reviews list switches between the builder's checklist (from
  `build.html`) and the final QC (from `index.html`). Projects can be **deleted with the password
  `123`** via the trash button on each row.

**AI check (wired, off by default).** An `ai-check` Supabase Edge Function compares an employee's
photo to the reference and writes a verdict. It stays dormant until `ANTHROPIC_API_KEY` is set as a
function secret and `AI_ENABLED` is flipped to `true` at the top of both HTML files. Model defaults
to `claude-opus-5` (override with the `AI_MODEL` function secret).

Config (Supabase URL + anon key) lives at the top of `build.html` / `review.html`. Access is open
(no login) for this internal tool; the anon key is public by design and fenced by row-level
security.
