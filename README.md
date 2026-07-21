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
      "id": "avatar-1",
      "name": "Aria",
      "vrm": "avatars/avatar-1/model.vrm",
      "thumbnail": "avatars/avatar-1/preview.png"
    }
  ]
}
```

- `id` — stable slug; on download the app stores the avatar under this id, so
  re-downloading overwrites the same files, and an already-installed avatar is
  detected by id (the card shows "Downloaded").
- `name` — the card title shown in the app.
- `vrm` / `thumbnail` — paths relative to `index.json` (absolute URLs also work).

## Adding an avatar

1. Create `avatars/<id>/` with `model.vrm` and a `preview.png` thumbnail.
2. Add an entry to `index.json` with the paths above.
3. Commit and push to `main`.

Notes: keep models within ~200 MB (the app's own upload limit); if a thumbnail is
missing or fails to download, the app generates a preview locally from the model.

`avatar-1` (Aria) mirrors the app's single bundled default avatar (same id as
the seeded default) — it is listed so a user can re-download the default after
deleting it. Keep it first in the manifest.
