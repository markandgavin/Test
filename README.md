# Vending Machine QC Form

A single self-contained web page for running QC on vending machines in the field. No server,
no login, no internet required — everything runs locally in your phone's browser.

## How to use

1. Open `index.html` on your phone (email it to yourself, put it in a cloud drive, or host it
   anywhere static — then bookmark it / add to Home Screen).
2. Walk the steps:
   - **Site** — enter the site name/number (e.g. "Site 82").
   - **Serial number** — type the full serial, then photograph the device / SN label.
   - **Confirm last 4** — re-enter the last 4 characters; it blocks you if they don't match
     what you typed (catches transcription errors).
   - **Photo checklist** — one photo each: Peplink, OptConnect, Nuc, Badge, Inside, Outside,
     Keys, Crate with label.
   - **Review** — check all photos (tap any tile to redo one), then **Generate report**.
3. On the print dialog, choose **Save as PDF** (or AirPrint / Print). You get one PDF per
   machine: a header with site, serial, and date, then one labeled photo per page.

Everything is held in memory for the current machine only. "Start a new machine" clears it and
begins a fresh QC.

## Editing the checklist

The checklist is a plain array near the top of the `<script>` in `index.html`:

```js
var CHECKLIST = [
  { id: "peplink",   label: "Peplink" },
  { id: "optconnect", label: "OptConnect" },
  ...
];
```

Add, remove, or reorder items there. Each `id` must be unique; `label` is what's shown in the
wizard and the report.
