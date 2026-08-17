# Vending Machine QC Form

A single self-contained web page for running QC on vending machines in the field. No server,
no login, no internet required — everything runs locally in your phone's browser, and it
generates a real PDF report with one tap.

## How to use

1. Open `index.html` on your phone (email it to yourself, put it in a cloud drive, or host it
   anywhere static — then bookmark it / add to Home Screen).
2. Walk the steps:
   - **Site number** — the key identifier (e.g. "82").
   - **Serial** — photograph the full serial number, then type the **last 6** as a quick
     reference.
   - **Photo checklist** — one photo each: Peplink, OptConnect, Nuc, Badge, Inside, Outside,
     Keys, Crate with label.
   - **Review** — check all photos (tap any tile to redo one), then **Download PDF**.
3. You get one PDF per machine: a cover page headlined with the **Site number** (plus the
   last-6 serial and date), then one labeled photo per page — full serial number first, then
   each component. The file saves as `QC_Site-<site>_<last6>.pdf`.

Everything is held in memory for the current machine only. "Start a new machine" clears it and
begins a fresh QC.

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

The PDF is generated entirely in the browser with no external libraries. Photos are downscaled
on capture (~1600px max edge) and re-encoded as JPEG, which embeds directly into the PDF — so
the report is self-contained, works offline, and stays reasonably small.
