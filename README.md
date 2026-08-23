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
  "schemaVersion": 1,
  "avatars": [
    {
      "id": "0cbce7b9-5ebb-463e-9a2a-8eb819512e39",
      "name": "Aria",
      "vrm": "avatars/0cbce7b9-5ebb-463e-9a2a-8eb819512e39/model.vrm",
      "thumbnail": "avatars/0cbce7b9-5ebb-463e-9a2a-8eb819512e39/preview.png"
    }
  ]
}
```

- `id` — the avatar's permanent identity: a random UUID v4, assigned once and
  **never renamed or reused for another model**. On download the app stores the
  avatar under this id, so re-downloading overwrites the same files, and an
  already-installed avatar is detected by id (the card shows "Downloaded").
  Renaming an id would break every installed copy: the old download turns into an
  orphan, and the new id gets downloaded again as a duplicate. The id carries no
  meaning on purpose (not a position, not a name) — that is what keeps it stable.
- `name` — the card title shown in the app. Display only, free to change at any
  time (already-downloaded copies keep the name they were downloaded with).
- `vrm` / `thumbnail` — paths relative to `index.json` (absolute URLs also work).

## Ordering

The Explore tab shows avatars **in manifest order** — the order of entries in the
`avatars` array, nothing else. To put a new avatar first, insert it first; the
other entries stay exactly as they are. Folder names and ids play no role in
ordering.

## Adding an avatar

1. Generate an id: `uuidgen | tr A-Z a-z`.
2. Create `avatars/<id>/` with `model.vrm` and a `preview.png` thumbnail
   (the folder name matching the id is a convention for tidiness, not a
   requirement — the manifest paths are explicit).
3. Insert an entry into `index.json` at the position where the card should appear.
4. Commit and push to `main`.

Notes: keep models within ~200 MB (the app's own upload limit); if a thumbnail is
missing or fails to download, the app generates a preview locally from the model.

The app's bundled default avatar is **not** part of this catalog (it ships inside
the app under its own id and cannot be deleted there), so nothing here mirrors it.
