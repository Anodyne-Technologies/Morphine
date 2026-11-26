# Morph

- **Namespace:** `Anodyne.Morphine`
- **Assembly:** `Anodyne.Morphine`

Represents a named combination of `BlendShapeData` targets mapped to authored base weights (`0–100`), identified by a stable internal GUID (`Id`) so embedded clips update in-place across renames.

---

## Fields & Properties

| Member | Type | Description |
|---|---|---|
| `Id` | `string` | Stable 32-character GUID (`EnsureId()`). Used by `MorphClipBinding` to track the morph's generated `AnimationClip` across renames. |
| `Name` | `string` | Human-readable morph name (e.g., `"Happy"` or `"Smile L"`). |
| `blendShapes` | `Dictionary<BlendShapeData, float>` | Maps each `BlendShapeData` target to its authored base weight (`0–100`). Natively serialized in Unity 6. |
| `Weight` | `float` | Overall morph weight (`0–100`, default `100f`). Setting `Weight` proportionally applies `(weight / 100f) * targetWeight` to every target in `blendShapes`. |

---

## Constructors

### `Morph()`
Creates an empty `Morph` with a freshly generated `Id`.

### `Morph(SkinnedMeshRenderer smr, int index)`
Creates a `Morph` with a fresh `Id`, sets `Name` to `smr.sharedMesh.GetBlendShapeName(index)`, and adds `(smr, index)` at weight `100f`.

---

## Methods & Operators

### `string EnsureId()`
Lazily initializes `id` to `Guid.NewGuid().ToString("N")` if null or empty, and returns it.

### `void AddMorphTarget(BlendShapeData target, float weight)`
Adds or updates `target` in `blendShapes`, clamping `weight` to `[0f, 100f]`.

### `bool RemoveMorphTarget(BlendShapeData target)`
Removes `target` from `blendShapes`. Returns `true` if the target existed.

### `Morph Clone(string newName = null)`
Creates a copy of the morph with cloned `BlendShapeData` entries and a **new unique `Id`** (preventing clip-binding collisions with the source morph).

### `static Morph operator +(Morph a, Morph b)`
Combines two morphs into a new `Morph` (named `"New Morph"` with a fresh `Id`), scaling each morph's targets by `Weight / 100f` and summing weights for overlapping `BlendShapeData` keys.

---

## Example

```csharp
using Anodyne.Morphine;
using UnityEngine;

public static class MorphExample
{
    public static Morph CreateSmirk(SkinnedMeshRenderer plastoSmr)
    {
        var smileR = new Morph(plastoSmr, 3) { Weight = 80f };
        var browRUp = new Morph(plastoSmr, 12) { Weight = 50f };

        Morph smirk = smileR + browRUp;
        smirk.Name = "Smirk";
        return smirk;
    }
}
```
