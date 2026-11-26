# BlendShapeData

- **Namespace:** `Anodyne.Morphine`
- **Assembly:** `Anodyne.Morphine`
- **Implements:** `IEquatable<BlendShapeData>`

Serializable wrapper referencing a single blend shape index on a `SkinnedMeshRenderer`, providing safe validity checking, weight clamping, and value-based equality so it can serve reliably as a `Dictionary<BlendShapeData, float>` key.

---

## Properties

| Property | Type | Description |
|---|---|---|
| `IsValid` | `bool` | Returns `true` when `Skin != null`, `Skin.sharedMesh != null`, and `Index` is within `[0, blendShapeCount)`. |
| `Name` | `string` | Blend shape name from `sharedMesh.GetBlendShapeName(Index)`, or `"<Invalid BlendShape>"` when invalid. |
| `Skin` | `SkinnedMeshRenderer` | Associated `SkinnedMeshRenderer`. |
| `Index` | `int` | Zero-based blend shape index on `Skin.sharedMesh`. |
| `Weight` | `float` | Gets or sets `Skin.GetBlendShapeWeight(Index)` / `SetBlendShapeWeight(Index, Mathf.Clamp(value, 0f, 100f))`. Safe no-op if `!IsValid`. |

---

## Constructors & Methods

| Signature | Description |
|---|---|
| `BlendShapeData()` | Empty constructor for serialization. |
| `BlendShapeData(SkinnedMeshRenderer smr, int index)` | Initializes a reference to `smr`'s blend shape at `index`. |
| `void SetWeight(float weight)` | Sets `Weight` (clamped `0–100`). |
| `float GetWeight()` | Returns current `Weight`. |
| `bool Equals(BlendShapeData other)` / `==` / `!=` / `GetHashCode()` | Value equality comparing `Skin` and `Index`. |
