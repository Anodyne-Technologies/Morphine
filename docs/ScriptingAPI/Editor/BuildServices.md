# Build Services API

- **Namespace:** `Anodyne.Morphine.Editor.Services`
- **Assembly:** `Anodyne.Morphine.Editor`

Programmatic APIs for incremental embedded morph `AnimationClip` syncing, embedded Base `AnimatorController` building, and deterministic dirty-state hashing.

---

## `MorphClipBuildService`

Incrementally builds and synchronizes embedded `AnimationClip` sub-assets inside a `MorphineDrip`, preserving `fileID` references across renames/edits, binding the shared zero-pose `additiveReferencePoseClip` (`__Morphine_Empty`), and pruning orphaned clip sub-assets.

### Methods

#### `static MorphClipBuildResult BuildOrSyncClips(MorphineDrip drip)`
Synchronously builds or incrementally updates all embedded morph clips on `drip` inside a batched `AssetDatabase.StartAssetEditing()` / `StopAssetEditing()` commit.

#### `static Task<MorphClipBuildResult> BuildOrSyncClipsAsync(MorphineDrip drip, CancellationToken cancellationToken = default, Action<float, string> onProgress = null, double frameBudgetMs = 8.0)`
Non-blocking, frame-budgeted async sync running on Unity's main thread (yielding via `EditorApplication.update` whenever `frameBudgetMs` is exceeded). Automatically integrates with `UnityEditor.Progress` (cancellable in Unity's Background Tasks bar).

### `MorphClipBuildResult`

| Property | Type | Description |
|---|---|---|
| `Created` | `int` | Newly created embedded clips. |
| `Updated` | `int` | Existing clips whose curves were re-baked in-place. |
| `Renamed` | `int` | Existing clips renamed in-place with unchanged curves. |
| `Unchanged` | `int` | Existing clips skipped because hash and name matched. |
| `Removed` | `int` | Orphaned embedded clip sub-assets pruned from the Drip. |
| `TotalProcessed` | `int` | `Created + Updated + Renamed + Unchanged`. |
| `Cancelled` | `bool` | `true` if cancelled before commit. |
| `Warnings` | `List<string>` | Non-fatal warnings (e.g., invalid blend shape targets skipped). |

---

## `BaseAnimatorBuildService`

Creates and in-place updates `drip.baseAnimatorController` as an embedded `AnimatorController` sub-asset of `MorphineDrip`. Each `MorphCollection` with `includeInController == true` becomes an `Additive` layer (`defaultWeight = 1f`, `avatarMask = drip.overrideMask`) containing a single state with a `Direct` BlendTree.

### Methods

#### `static BaseAnimatorBuildResult BuildOrSyncBaseController(MorphineDrip drip, bool syncClipsFirst = true)`
Synchronously rebuilds the embedded Base `AnimatorController` in-place (preserving its `GlobalObjectId`), optionally syncing dirty/missing clips first.

#### `static Task<BaseAnimatorBuildResult> BuildOrSyncBaseControllerAsync(MorphineDrip drip, CancellationToken cancellationToken = default, Action<float, string> onProgress = null)`
Runs `MorphClipBuildService.BuildOrSyncClipsAsync` if clips are dirty/missing, then rebuilds the embedded Base AC in-place.

### `BaseAnimatorBuildResult`

| Property | Type | Description |
|---|---|---|
| `Controller` | `AnimatorController` | The embedded Base `AnimatorController` instance. |
| `LayerCount` | `int` | Number of generated Additive layers. |
| `ParameterCount` | `int` | Number of generated Float parameters. |
| `Cancelled` | `bool` | `true` if async clip sync was cancelled. |
| `Warnings` | `List<string>` | Non-fatal build warnings. |

---

## `MorphineBuildHashUtility`

| Method | Returns | Description |
|---|---|---|
| `ComputeDesiredClipNames(MorphineDrip drip)` | `Dictionary<string, string>` | Maps `morphId -> clipName` (`<Morph>` if unique, else `<Collection>_<Morph>`). |
| `ComputeMorphContentHash(Morph morph, GameObject root, string desiredClipName)` | `string` | Deterministic SHA-256 hex digest of clip name and sorted target weights. |
| `ComputeDripClipsHash(MorphineDrip drip)` | `string` | Aggregate SHA-256 digest of all morph clips in the Drip. |
| `ComputeDripBaseControllerHash(MorphineDrip drip)` | `string` | Aggregate SHA-256 digest of included collections, morphs, and mask state. |
| `IsClipsDirty(MorphineDrip drip)` | `bool` | Returns `true` when clips are missing or out of sync with authored morph data. |
| `IsBaseControllerDirty(MorphineDrip drip)` | `bool` | Returns `true` when Base AC is missing, clips are dirty, or included collections changed. |
| `CountTotalMorphs(MorphineDrip drip)` | `int` | Total number of morphs across all collections. |

---

## Example: Automated Editor Build Pipeline

```csharp
using Anodyne.Morphine;
using Anodyne.Morphine.Editor.Services;
using UnityEditor;
using UnityEngine;

public static class MorphineBatchBuildExample
{
    public static void RebuildPlastoDrip()
    {
        var drip = AssetDatabase.LoadAssetAtPath<MorphineDrip>("Assets/Plasto_Head_DRIP.asset");
        if (drip == null) return;

        BaseAnimatorBuildResult res = BaseAnimatorBuildService.BuildOrSyncBaseController(drip, syncClipsFirst: true);
        Debug.Log($"Built {res.Controller.name}: {res.LayerCount} layer(s), {res.ParameterCount} param(s).");
    }
}
```
