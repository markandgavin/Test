# Vending Machine QC Form

A single self-contained web page for running QC on vending machines in the field. No server,
no login, no internet required — everything runs locally in your phone's browser. It keeps a
list of machines on your device and generates a PDF report per machine.

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

Machines and photos are stored **on your device** using the browser's IndexedDB, tied to how you
opened the page. Open it the same way each time (ideally the Home-Screen app) to keep your list.
Nothing is uploaded anywhere.

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
