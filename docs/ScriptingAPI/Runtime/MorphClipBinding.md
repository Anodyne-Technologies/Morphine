# MorphClipBinding

- **Namespace:** `Anodyne.Morphine`
- **Assembly:** `Anodyne.Morphine`

Serializable record stored in `MorphineDrip.clipBindings` that links a `Morph`'s stable `Id` to its embedded `AnimationClip` sub-asset and last baked content hash.

---

## Fields

| Field | Type | Description |
|---|---|---|
| `morphId` | `string` | Stable GUID matching `Morph.Id`. |
| `clip` | `AnimationClip` | Reference to the embedded `AnimationClip` sub-asset inside the `MorphineDrip`. |
| `contentHash` | `string` | SHA-256 hash of the clip name and sorted target weights recorded during the last sync. |

---

## Constructors

- `MorphClipBinding()`
- `MorphClipBinding(string morphId, AnimationClip clip, string contentHash)`
