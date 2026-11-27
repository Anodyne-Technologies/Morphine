# Animator Aggregation API

- **Namespace:** `Anodyne.Morphine.Editor.Aggregation`
- **Assembly:** `Anodyne.Morphine.Editor`

Programmatic API for non-destructive deep-cloning `AnimatorController` aggregation, usable with or without a `MorphineDrip`.

---

## `AnimatorAggregationRecipe`

Editor-only `ScriptableObject` (`[CreateAssetMenu(menuName = "Animation/Animator Aggregation Recipe")]`) storing a reusable merge configuration.

| Field | Type | Description |
|---|---|---|
| `outputController` | `AnimatorController` | Existing output `.controller` to update in-place (preserving GUID). |
| `outputAssetPath` | `string` | Target path when creating a new `.controller` on first bake. |
| `sources` | `List<AnimatorAggregationSource>` | Ordered list of `AnimatorController` or `MorphineDrip` sources. |
| `autoGenerateConstants` | `bool` | Automatically regenerate C# constants after a successful bake. |
| `constantsConfig` | `MorphineConstantsConfig` | Settings passed to `AnimatorConstantsGenerator`. |

### `AnimatorAggregationSource`

| Member | Type | Description |
|---|---|---|
| `sourceAsset` | `UnityEngine.Object` | Accepts either an `AnimatorController` or a `MorphineDrip`. |
| `enabled` | `bool` | Whether this source participates in planning and baking. |
| `parameterPrefix` | `string` | Optional prefix prepended to all parameters from this source (all internal references remapped). |
| `layerNamePrefix` | `string` | Optional prefix prepended to all layer names from this source. |
| `autoSyncDripBeforeBake` | `bool` | For `MorphineDrip` sources, rebuilds clips/Base AC if missing or dirty before baking. |
| `layerOverrides` | `List<AnimatorLayerOverride>` | Per-layer include/weight/blending-mode/mask overrides. |
| `ResolveController()` | `AnimatorController` | Resolves `sourceAsset` directly or via `MorphineDrip.BaseAnimatorControllerEditor`. |
| `SyncLayerOverridesFromController()` | `void` | Reconciles `layerOverrides` with the resolved controller's current layers. |

### `AnimatorLayerOverride`
Fields: `sourceLayerName`, `sourceLayerIndex`, `include`, `overrideWeight`, `weight`, `overrideBlendingMode`, `blendingMode`, `overrideAvatarMask`, `avatarMask`.

---

## `AnimatorAggregationPlanner` & `AnimatorAggregationPlan`

### `static AnimatorAggregationPlan BuildPlan(IReadOnlyList<AnimatorAggregationSource> sources)`
Performs a pure read-only dry run without mutating any assets.

- **`plan.Layers`** (`List<PlannedLayer>`): Final layer index (`TargetIndex`), `FinalName`, `EffectiveWeight`, `PromotedLayer0Weight`, `EffectiveBlendingMode`, `EffectiveMask`, `RemappedSyncedLayerIndex`.
- **`plan.Parameters`** (`List<PlannedParameter>`): Final `Name`, `Type`, defaults, `Status` (`Added`, `Shared`, `Prefixed`, `RenamedOnTypeMismatch`), and `SourceDisplayNames`.
- **`plan.Diagnostics`** (`List<AggregationDiagnostic>`): `Severity` (`Info`, `Warning`, `Error`) and `Message`; check `plan.HasErrors` and `plan.WarningCount`.
- **`plan.ParameterRemapsBySourceIndex`**: Per-source `Dictionary<string, string>` mapping original parameter names to final names.

---

## `AnimatorAggregatorService`

### Methods

#### `static AggregationBakeResult Bake(AnimatorAggregationRecipe recipe)`
Bakes `recipe`, updates `recipe.outputController` and `recipe.outputAssetPath` if newly created, and saves.

#### `static AggregationBakeResult Bake(IReadOnlyList<AnimatorAggregationSource> sources, AnimatorController existingOutput, string outputAssetPath, bool generateConstants = false, MorphineConstantsConfig constantsConfig = null)`
Direct ad-hoc overload. Updates `existingOutput` in-place (preserving its asset GUID), deep-cloning StateMachines, nested SubStateMachines, States, nested BlendTrees, Transitions (with remapped conditions), `StateMachineBehaviour`s, and Synced Layer overrides.

### `AggregationBakeResult`

| Property | Type | Description |
|---|---|---|
| `Success` | `bool` | `true` when baking completed without errors. |
| `OutputController` | `AnimatorController` | The baked output `.controller` asset. |
| `Plan` | `AnimatorAggregationPlan` | The dry-run plan executed during baking. |
| `GeneratedConstantsPath` | `string` | Asset path of the generated `.cs` file when `generateConstants == true`. |

---

## Example: Programmatic Aggregation + Constants

```csharp
using System.Collections.Generic;
using Anodyne.Morphine;
using Anodyne.Morphine.Editor.Aggregation;
using UnityEditor;
using UnityEditor.Animations;
using UnityEngine;

public static class ProgrammaticAggregationExample
{
    public static void BakeCombinedCharacter(AnimatorController locomotion, MorphineDrip plastoDrip)
    {
        var sources = new List<AnimatorAggregationSource>
        {
            new AnimatorAggregationSource { sourceAsset = locomotion, enabled = true },
            new AnimatorAggregationSource { sourceAsset = plastoDrip, enabled = true, autoSyncDripBeforeBake = true }
        };

        var constantsConfig = new MorphineConstantsConfig
        {
            outputFolder = "Assets/Generated/Constants",
            namespaceName = "Game.Animation"
        };

        AggregationBakeResult result = AnimatorAggregatorService.Bake(
            sources,
            existingOutput: null,
            outputAssetPath: "Assets/Generated/Plasto_Combined.controller",
            generateConstants: true,
            constantsConfig: constantsConfig);

        if (result.Success)
            Debug.Log($"Baked {result.OutputController.name} -> {result.GeneratedConstantsPath}");
    }
}
```
