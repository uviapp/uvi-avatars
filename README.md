# uvi-avatars

Avatar catalog - Avatars are VRM models (VRoid Studio / VRoid Hub and compatible)

## Layout

```
index.json              # catalog manifest the app reads (raw.githubusercontent, branch main)
avatars/<id>/
├── model.vrm           # the VRM model, referenced by the manifest
└── preview.png         # card thumbnail, referenced by the manifest
```

## Manifest format

```json
{
  "schemaVersion": 2,
  "avatars": [
    {
      "id": "0cbce7b9-5ebb-463e-9a2a-8eb819512e39",
      "name": "Aria",
      "vrm": "avatars/0cbce7b9-5ebb-463e-9a2a-8eb819512e39/model.vrm",
      "thumbnail": "avatars/0cbce7b9-5ebb-463e-9a2a-8eb819512e39/preview.png",
      "authorUrl": "https://hub.vroid.com/en/users/110934782",
      "source": "https://hub.vroid.com/en/characters/1068688385771703078/models/6133742810208915407",
      "checkedAt": "2026-09-02",
      "license": {
        "specVersion": "0.x",
        "title": "FREE 2",
        "author": "AnimeFreak",
        "contact": null,
        "licenseUrl": "https://hub.vroid.com/license?...",
        "redistribution": "allow",
        "modification": "allow",
        "credit": "unnecessary",
        "commercial": "allow"
      }
    }
  ]
}
```

### Identity and display

- `id` — the avatar's permanent identity: a random UUID v4, assigned once and
  **never renamed or reused for another model**. On download the app stores the
  avatar under this id, so re-downloading overwrites the same files, and an
  already-installed avatar is detected by id (the card shows "Downloaded").
  Renaming an id would break every installed copy: the old download turns into an
  orphan, and the new id gets downloaded again as a duplicate. The id carries no
  meaning on purpose (not a position, not a name) — that is what keeps it stable.
- `name` — the card title shown in the app. Display only, free to change at any
  time (already-downloaded copies keep the name they were downloaded with). It
  does not have to match the character name inside the model: every avatar in this
  catalog carries `characterization_allowed_user: everyone`, which permits giving
  the model its own identity. Several entries here are named by us because the
  model file carries only a slot label (`AvatarSample_A`, `FREE 2`).
- `vrm` / `thumbnail` — paths relative to `index.json` (absolute URLs also work).

### Attribution (curated, survives download)

- `author` — **optional override**, set only when the model file gives no usable
  author: the field is empty, or it is written in a non-Latin script
  (`VRoidプロジェクト` → `VRoid Project`). When the file already names its author in
  Latin, leave this out and let the file speak — do not normalize casing or handles
  even where the same person is spelled differently across files. The app persists
  the override alongside the downloaded avatar, so it survives the download; a
  value extracted from the file would not, because after downloading the app
  re-reads the license from the bytes it received.
- `authorUrl` — link to the author's page. Rendered as a clickable author name,
  never as a bare URL. This is what discharges a `credit: required` obligation in
  a way a plain string cannot, so it is **mandatory** for those entries. Cannot be
  derived from the model file, hence curated here.

### License (generated, never hand-written)

- `license` — a normalized block **extracted from the .vrm file**, identical in
  shape to what the app reads back after download. Never edit it by hand: the app
  re-extracts it from the downloaded bytes, so a hand-tuned value here would only
  affect the Explore card and then silently disappear. Regenerate it if the model
  file is replaced.
  - `specVersion` — `"0.x"` or `"1.0"`, which VRM generation the file uses.
  - `title` / `author` / `contact` — as recorded inside the file, `null` when absent.
    `title` is kept as a provenance record; the app does not display it.
  - `licenseUrl` — the human-readable terms (VRoid Hub license page, or
    `vrm.dev/licenses/1.0/`). Shown in the app as "full terms".
  - `redistribution` / `modification` — `allow` | `disallow` | `unknown`.
  - `credit` — `required` | `unnecessary` | `unknown`.
  - `commercial` — `allow` | `personalOnly` | `disallow` | `unknown`.

For VRM 0.x these come from `licenseName` when it is not `Other`, and from the
query parameters of the license URL otherwise — in that order, because a file can
carry `licenseName: Redistribution_Prohibited` with no URL at all.

### Provenance (record of due diligence)

- `source` — the model's own page on VRoid Hub (or wherever it was obtained).
- `checkedAt` — the date its terms were last verified, `YYYY-MM-DD`.

The app reads neither field. They exist so that a licensing question a year from
now can be answered with what the terms said when the model was accepted.

## Ordering

The Explore tab shows avatars **in manifest order** — the order of entries in the
`avatars` array, nothing else. To put a new avatar first, insert it first; the
other entries stay exactly as they are. Folder names and ids play no role in
ordering.

## Adding an avatar

**`redistribution: allow` inside the file is not enough.** Authors regularly add
their real terms as free text on their VRoid Hub profile, and that text can
contradict the structured flags in both directions — one model in this catalog was
rejected for exactly that: its metadata said redistribution was allowed while the
author's profile forbade re-uploading the model anywhere without obvious changes,
"even if re-uploaded for free". Always read the profile before accepting a model.

1. Open the model's page on VRoid Hub and confirm the structured license matches
   the file's own metadata.
2. Open the **author's profile page** and read the description in full. A written
   term overrides the checkbox it contradicts.
3. Generate an id: `uuidgen | tr A-Z a-z`.
4. Create `avatars/<id>/` with `model.vrm` and a `preview.png` thumbnail
   (the folder name matching the id is a convention for tidiness, not a
   requirement — the manifest paths are explicit).
5. Extract the `license` block from the .vrm rather than typing it, and fill in
   `source`, `checkedAt`, `authorUrl` (mandatory when `credit` is `required`) and
   `author` (only if the file names none).
6. Insert the entry into `index.json` at the position where the card should appear.
7. Commit and push to `main`.

Notes: keep models within ~200 MB (the app's own upload limit); if a thumbnail is
missing or fails to download, the app generates a preview locally from the model.
Thumbnails here are 640x800 PNG, matching what the app renders offscreen on a
retina display.

The app's bundled default avatar is **not** part of this catalog (it ships inside
the app under its own id and cannot be deleted there), so nothing here mirrors it.
