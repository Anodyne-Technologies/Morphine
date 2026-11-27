# MorphineDrip

- **Namespace:** `Anodyne.Morphine`
- **Assembly:** `Anodyne.Morphine`
- **Inherits:** `UnityEngine.ScriptableObject`

Central data container (**DRIP** = **Data Relationships, Identification, & Paths**) for a character's morph targets, collections, embedded `AnimationClip`s, embedded `AvatarMask`, and embedded Base `RuntimeAnimatorController`. All sub-asset references (`baseAnimatorController`, `morphClips`, `clipBindings`, `emptyFallbackClip`, `overrideMask`) are runtime-accessible in Player builds.

---

## Runtime Fields & Properties

| Member | Type | Description |
|---|---|---|
| `prefabObject` | `GameObject` | Primary character prefab associated with this Drip (e.g., `Plasto_Head.prefab`). |
| `baseAnimatorController` | `RuntimeAnimatorController` | Embedded morph-only Base `AnimatorController` (`[FormerlySerializedAs("animatorController")]`). |
| `UsableSkins` | `List<SkinnedMeshRenderer>` | Read-only accessor to the list of eligible `SkinnedMeshRenderer`s on `prefabObject`. |
| `morphCollections` | `List<MorphCollection>` | Ordered list of base and custom morph collections. |
| `morphClips` | `List<AnimationClip>` | Embedded `AnimationClip` sub-assets generated from the morphs in this Drip. |
| `clipBindings` | `List<MorphClipBinding>` | Stable `morphId -> (clip, contentHash)` table used for rename-safe incremental updates. |
| `emptyFallbackClip` | `AnimationClip` | Embedded zero-pose clip (`__Morphine_Empty`) used both as `additiveReferencePoseClip` and as fallback for unmatched `AnimatorOverrideController` slots. |
| `overrideMask` | `AvatarMask` | Embedded `AvatarMask` applied to every generated Additive layer. |

## Editor-Only Fields & Properties (`#if UNITY_EDITOR`)

| Member | Type | Description |
|---|---|---|
| `BaseAnimatorControllerEditor` | `AnimatorController` | Editor-typed getter/setter wrapping `baseAnimatorController`. |
| `animatorController` | `AnimatorController` | Legacy compatibility property forwarding to `BaseAnimatorControllerEditor`. |
| `isOpen` | `bool` | Legacy flag indicating inspector state. |
| `lastBuiltClipsHash` | `string` | SHA-256 hash recorded after the most recent clip sync. |
| `lastBuiltBaseControllerHash` | `string` | SHA-256 hash recorded after the most recent Base AC build. |
| `autoGenerateConstantsOnBuild` | `bool` | If `true`, rebuilding the Base AC automatically regenerates C# constants. |
| `constantsConfig` | `MorphineConstantsConfig` | Per-DRIP C# constants generation settings. |
| `linkedAggregationRecipe` | `ScriptableObject` | Optional `AnimatorAggregationRecipe` opened automatically by **Open Aggregator...**. |

---

## Methods

### `MorphClipBinding FindBindingByMorphId(string morphId)`
Returns the `MorphClipBinding` matching `morphId`, or `null` if not yet generated.

### `AnimationClip GetClipByMorphId(string morphId)`
Returns the embedded `AnimationClip` bound to `morphId`, or `null`.

### `AnimationClip GetClipForMorph(Morph morph)`
Convenience overload returning `GetClipByMorphId(morph.Id)`.

### `void ResetAllBlendShapes()`
Iterates through every `MorphCollection` and `Morph` in `morphCollections` and resets every constituent `BlendShapeData` weight to `0f`.

---

## Example

```csharp
using Anodyne.Morphine;
using UnityEngine;

public class DripClipInspector : MonoBehaviour
{
    public MorphineDrip plastoDrip;

    public AnimationClip FindMorphClip(string morphName)
    {
        foreach (MorphCollection collection in plastoDrip.morphCollections)
        {
            foreach (Morph morph in collection)
            {
                if (morph.Name == morphName)
                    return plastoDrip.GetClipForMorph(morph);
            }
        }
        return null;
    }
}
```
