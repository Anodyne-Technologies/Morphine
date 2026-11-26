# Morphine Scripting API Reference

Programmatic reference for Morphine's Runtime (`Anodyne.Morphine`) and Editor (`Anodyne.Morphine.Editor.*`) assemblies.

---

## Runtime Assembly (`Anodyne.Morphine`)

Safe for use in both Player builds and Editor scripts.

| Type | Page | Summary |
|---|---|---|
| `MorphineDrip` | [MorphineDrip](Runtime/MorphineDrip.md) | Central `ScriptableObject` holding usable skins, morph collections, embedded clips, zero-pose fallback clip, `AvatarMask`, and embedded Base `RuntimeAnimatorController`. |
| `MorphCollection` | [MorphCollection](Runtime/MorphCollection.md) | Ordered `IEnumerable<Morph>` container with `includeInController` toggle. |
| `Morph` | [Morph](Runtime/Morph.md) | Named multi-blendshape recipe with stable `Id`, scaling `Weight`, `Clone()`, and additive `+` operator. |
| `BlendShapeData` | [BlendShapeData](Runtime/BlendShapeData.md) | `IEquatable<BlendShapeData>` wrapper for a `(SkinnedMeshRenderer, index)` pair with `IsValid` and clamped `Weight`. |
| `MorphClipBinding` | [MorphClipBinding](Runtime/MorphClipBinding.md) | Serializable `(morphId, clip, contentHash)` binding for rename-safe incremental sync. |
| `MorphineConstantsConfig` / `ConstantsNamingStyle` | [MorphineConstantsConfig](Runtime/MorphineConstantsConfig.md) | Serializable configuration for C# Constants generation (`PascalCase` / `ScreamingSnakeCase`, section toggles). |
| `MorphController` | *(Reserved)* | Empty `MonoBehaviour` placeholder reserved for future runtime helper components. |

---

## Editor Assembly (`Anodyne.Morphine.Editor`)

Compiled with `"includePlatforms": ["Editor"]`.

| Namespace | Page | Key Types |
|---|---|---|
| `Anodyne.Morphine.Editor.Services` | [Build Services](Editor/BuildServices.md) | `MorphClipBuildService`, `MorphClipBuildResult`, `BaseAnimatorBuildService`, `BaseAnimatorBuildResult`, `MorphineBuildHashUtility` |
| `Anodyne.Morphine.Editor.Aggregation` | [Animator Aggregation](Editor/AnimatorAggregation.md) | `AnimatorAggregationRecipe`, `AnimatorAggregationSource`, `AnimatorLayerOverride`, `AnimatorAggregationPlanner`, `AnimatorAggregationPlan`, `PlannedLayer`, `PlannedParameter`, `AggregationDiagnostic`, `AnimatorAggregatorService`, `AggregationBakeResult` |
| `Anodyne.Morphine.Editor.Services` | [Animator Overrides](Editor/AnimatorOverrides.md) | `AnimatorOverrideService`, `OverrideMappingPlan`, `OverrideSlotEntry`, `OverrideSlotKind` |
| `Anodyne.Morphine.Editor.Constants` | [Constants Generation](Editor/ConstantsGeneration.md) | `AnimatorConstantsGenerator`, `CSharpIdentifierSanitizer`, `MorphineConstantsSettings` |
| `Anodyne.Morphine.Editor.UI` / `Anodyne.Morphine.Editor` | [Authoring & Preview](Editor/AuthoringAndPreview.md) | `MorphineDripService`, `DripValidationIssue`, `MorphinePreviewSession`, `MorphineAuthoringWindow`, `AnimatorAggregatorWindow`, `MorphineDripEditor`, `EditorMenus` |
| `Anodyne.Morphine.Editor` / Global | [Utilities & Extensions](Editor/UtilitiesAndExtensions.md) | `AnimationUtilities`, `MorphExtensions`, `MorphCollectionExtensions`, `AnimatorControllerExtensions`, `GameObjectExtensions`, `SkinnedMeshRendererUtilities` |
