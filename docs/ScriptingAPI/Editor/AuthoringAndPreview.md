# Authoring, Preview & Editor Windows API

- **Namespaces:** `Anodyne.Morphine.Editor.UI`, `Anodyne.Morphine.Editor`
- **Assembly:** `Anodyne.Morphine.Editor`

Undo-safe authoring services, Prefab Stage preview management, and Editor Window entry points.

---

## `MorphineDripService` (`Anodyne.Morphine.Editor.UI`)

Centralized, Undo-safe authoring operations on `MorphineDrip`, `MorphCollection`, and `Morph`.

| Category | Methods |
|---|---|
| **Skin Discovery & Sync** | `GetPrefabSkinsWithBlendShapes(GameObject)`, `GetEffectiveSkins(MorphineDrip)`, `EnsureUsableSkinsInitialized(MorphineDrip)`, `SetSkinUsable(MorphineDrip, SkinnedMeshRenderer, bool)`, `IsBaseCollection(MorphineDrip, MorphCollection)`, `SyncBaseCollections(MorphineDrip)`, `EnsureAllMorphIds(MorphineDrip)` |
| **Collections** | `AddCollection(MorphineDrip, string)`, `RenameCollection(MorphineDrip, MorphCollection, string)`, `RemoveCollection(MorphineDrip, MorphCollection)`, `MoveCollection(MorphineDrip, int, int)`, `SetCollectionIncludedInController(MorphineDrip, MorphCollection, bool)`, `FindCollectionContaining(MorphineDrip, Morph)` |
| **Morphs & Targets** | `CaptureMorph(MorphineDrip, MorphCollection, string, IReadOnlyDictionary<BlendShapeData, float>)`, `OverwriteMorph(MorphineDrip, Morph, IReadOnlyDictionary<BlendShapeData, float>)`, `CreateEmptyMorph(MorphineDrip, MorphCollection, string)`, `DuplicateMorph(MorphineDrip, MorphCollection, Morph)`, `RenameMorph(MorphineDrip, Morph, string)`, `RemoveMorph(MorphineDrip, MorphCollection, Morph)`, `SetMorphTargetWeight(MorphineDrip, Morph, BlendShapeData, float)`, `AddMorphTarget(MorphineDrip, Morph, SkinnedMeshRenderer, int, float)`, `RemoveMorphTarget(MorphineDrip, Morph, BlendShapeData)` |
| **Validation** | `ValidateDrip(MorphineDrip)` → `List<DripValidationIssue>` (`Severity`, `Message`) |

---

## `MorphinePreviewSession` (`Anodyne.Morphine.Editor.UI`)

`sealed class MorphinePreviewSession : IDisposable` managing non-destructive Prefab Stage live preview. Automatically snapshots resting blend shape weights on bind and restores them on `Dispose()`, Drip switch, or Prefab Stage close.

- **Events**: `PreviewChanged`, `StageStateChanged`
- **Properties**: `Drip`, `SoloMorph`, `IsStageBound`, `LastEvaluatedWeights`, `ActiveEvaluatedBlendShapeCount`, `HasAnyActivePreview`
- **Methods**: `AttachDrip(MorphineDrip, bool openStageIfNeeded)`, `OpenPrefabStage()`, `TryBindToCurrentStage()`, `GetPreviewWeight(Morph)`, `GetActiveMorphCount(MorphCollection)`, `SetPreviewWeight(Morph, float)`, `ToggleSolo(Morph)`, `ClearSolo()`, `ResetMorph(Morph)`, `ResetCollection(MorphCollection)`, `ResetAll()`, `LoadMorphIntoMixer(Morph)`, `PruneDeletedMorphs()`, `Evaluate()`, `RestoreRestingWeights()`, `Dispose()`

---

## Editor Windows & Inspectors

### `MorphineAuthoringWindow` (`Anodyne.Morphine.Editor.UI`)
- **Menu:** `Tools > Morphine > Authoring Window` (also opened automatically on double-clicking a `MorphineDrip` asset via `MorphineDripEditor.OnOpenMorphineDrip`).
- **Static API:** `MorphineAuthoringWindow.Open(MorphineDrip drip, bool openPrefabStage = true)`
- **Instance API:** `SetActiveDrip(MorphineDrip drip, bool openPrefabStage = false)`, `SelectWorkspaceTabByName(string tabName)` (`"Morphs"`, `"Build"`, `"Overrides"`).

### `AnimatorAggregatorWindow` (`Anodyne.Morphine.Editor.UI`)
- **Menu:** `Tools > Morphine > Animator Aggregator` (also opened automatically on double-clicking an `AnimatorAggregationRecipe` asset).
- **Static API:** `AnimatorAggregatorWindow.Open(AnimatorAggregationRecipe recipe = null)`, `AnimatorAggregatorWindow.OpenWithDrip(MorphineDrip drip)`
- **Instance API:** `SetRecipe(AnimatorAggregationRecipe recipe)`, `SelectPreviewTabByName(string tabName)` (`"Layers"`, `"Parameters"`, `"Diagnostics"`).

### `MorphineDripEditor` (`Anodyne.Morphine.Editor`)
Custom UI Toolkit Inspector for `MorphineDrip`. Exposes helper methods `BuildBaseCollections()` and `MorphineCollectionInjection()` (delegating to `MorphineDripService.SyncBaseCollections` and `BaseAnimatorBuildService.BuildOrSyncBaseController`).

### `EditorMenus` (`Anodyne.Morphine.Editor`)
Provides `Assets > Create > Animation > Morphine DRIP` for prefabs containing `SkinnedMeshRenderer`s with blend shapes.
