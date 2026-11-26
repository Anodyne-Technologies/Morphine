# Animator Overrides API

- **Namespace:** `Anodyne.Morphine.Editor.Services`
- **Assembly:** `Anodyne.Morphine.Editor`

Auto-populates `AnimatorOverrideController` assets by matching clip slots from a base `RuntimeAnimatorController` to a target `MorphineDrip`'s embedded morph clips.

---

## `AnimatorOverrideService`

### Methods

#### `static bool IsMorphAnimationClip(AnimationClip clip)`
Returns `true` if `clip` is `__Morphine_Empty`, is embedded inside a `MorphineDrip` asset, or has >= 1 curve where every curve targets `SkinnedMeshRenderer` with `propertyName.StartsWith("blendShape.")`. Used to distinguish morph slots from skeletal/locomotion clips.

#### `static OverrideMappingPlan BuildPlan(RuntimeAnimatorController baseController, MorphineDrip targetDrip, ICollection<string> includedCollections = null, bool ensureTargetClipsBuilt = true)`
Analyzes all overridable slots on `baseController` (unwrapping nested `AnimatorOverrideController`s if needed) and classifies each slot against `targetDrip`:
1. Matches by exact clip name, `<Collection>_<Morph>`, `<Morph>`, or stripped collection prefix → `OverrideSlotKind.MatchedMorph`.
2. Unmatched morph clips → `OverrideSlotKind.UnmatchedMorphFallback` mapped to `targetDrip.emptyFallbackClip`.
3. Non-morph clips (e.g., locomotion animations) → `OverrideSlotKind.NonMorphPreserved` mapped to `null` (preserving the base clip).

#### `static AnimatorOverrideController ApplyPlan(OverrideMappingPlan plan, AnimatorOverrideController targetOverride = null, string createAtAssetPath = null)`
Applies all slot overrides in `plan` to `targetOverride` (or creates a new `.overrideController` at `createAtAssetPath`) and saves.

---

## Supporting Types

- **`OverrideSlotKind`**: `MatchedMorph`, `UnmatchedMorphFallback`, `NonMorphPreserved`, `ManualOverride`.
- **`OverrideSlotEntry`**: `OriginalClip`, `TargetClip`, `Kind`, `MatchedCollectionName`, `MatchedMorphName`, `UserModified`.
- **`OverrideMappingPlan`**: `BaseController`, `TargetDrip`, `Entries`, `MatchedCount`, `FallbackCount`, `PreservedCount`, `ManualCount`, `RecalculateCounts()`.

---

## Example

```csharp
using Anodyne.Morphine;
using Anodyne.Morphine.Editor.Services;
using UnityEngine;

public static class OverrideAutomationExample
{
    public static AnimatorOverrideController CreateOverrideForCharacter(
        RuntimeAnimatorController masterController,
        MorphineDrip targetCharacterDrip,
        string outputPath)
    {
        OverrideMappingPlan plan = AnimatorOverrideService.BuildPlan(
            masterController,
            targetCharacterDrip,
            includedCollections: new[] { "Expressions" });

        return AnimatorOverrideService.ApplyPlan(plan, targetOverride: null, createAtAssetPath: outputPath);
    }
}
```
