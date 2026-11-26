# Utilities & Extensions API

- **Namespace:** `Anodyne.Morphine.Editor` (and global `SkinnedMeshRendererUtilities`)
- **Assembly:** `Anodyne.Morphine.Editor`

Low-level helpers and extension methods backing Morphine's asset generation pipeline.

---

## `AnimationUtilities`

| Member | Description |
|---|---|
| `const string EmptyFallbackClipName = "__Morphine_Empty"` | Name of the shared embedded zero-pose clip. |
| `static AvatarMask CreateEmptyAvatarMaskAsset(string name, GameObject prefabObject = null)` | Creates an `AvatarMask` with all Humanoid body parts disabled and an empty transform list. |
| `static AvatarMask EnsureOverrideMask(MorphineDrip drip)` | Ensures `drip.overrideMask` exists as an embedded sub-asset of `drip`. |
| `static AnimationClip EnsureEmptyFallbackClip(MorphineDrip drip)` | Ensures `drip.emptyFallbackClip` exists as a hidden embedded sub-asset and synchronizes its zero-weight reference curves. |
| `static void SyncZeroReferenceCurves(MorphineDrip drip, AnimationClip zeroClip)` | Writes `0f` curves on `zeroClip` for every blend shape across all `SkinnedMeshRenderer`s on `drip.prefabObject` and binds self-reference pose settings. |

---

## `MorphExtensions`

| Method | Description |
|---|---|
| `static bool BakeIntoClip(this Morph morph, GameObject root, AnimationClip targetClip, string desiredClipName = null, List<string> warnings = null, AnimationClip additiveReferencePoseClip = null)` | Bakes deterministic 1-frame constant pose curves at each target's authored weight into `targetClip` in-place (preserving `fileID`) and optionally applies `additiveReferencePoseClip`. |
| `static void ApplyAdditiveReferencePose(AnimationClip clip, AnimationClip referencePoseClip)` | Configures `AnimationClipSettings.hasAdditiveReferencePose = true` and binds `referencePoseClip` at `t = 0`. |
| `static AnimationClip ToAnimationClip(this Morph morph, GameObject root, string desiredClipName = null)` | Allocates a new `AnimationClip` and bakes `morph` into it via `BakeIntoClip`. |

---

## `MorphCollectionExtensions`

- `static MorphClipBuildResult CreateMorphClips(this MorphineDrip drip)` — Delegates to `MorphClipBuildService.BuildOrSyncClips(drip)`.
- `static void ClearChildAssets(this MorphineDrip drip)` — Logs all sub-assets stored at `drip`'s asset path for diagnostics.

---

## `AnimatorControllerExtensions`

- `static void InjectMorphCollection(this AnimatorController anim, MorphCollection morphCollection, GameObject root)` — Directly appends one `Additive` Direct BlendTree layer and embedded clips into `anim`.
- `static string CreateConstantsFromAnimatorController(AnimatorController animator, MorphineConstantsConfig config = null)` — Delegates to `AnimatorConstantsGenerator.GenerateSource(...)`.
- `[MenuItem("Assets/Create/Animation/Animator Controller C# Constants")]` — Generates constants for the selected `AnimatorController` using `MorphineConstantsSettings.LoadProjectDefaults()`.

---

## `GameObjectExtensions` & `SkinnedMeshRendererUtilities`

- `GameObjectExtensions.HasSkinnedMeshRenderersWithBlendShapes(this GameObject)`
- `GameObjectExtensions.GetSkinnedMeshRendererWithBlendShapes(this GameObject)`
- `SkinnedMeshRendererUtilities.HasBlendShapes(this SkinnedMeshRenderer)`
- `SkinnedMeshRendererUtilities.BlendShapeCount(this SkinnedMeshRenderer)`
- `SkinnedMeshRendererUtilities.ClearBlendShapeWeights(this SkinnedMeshRenderer)`
- `SkinnedMeshRendererUtilities.ClearBlendShapes(this SkinnedMeshRenderer)`
- `SkinnedMeshRendererUtilities.AddBlendShapeFrame(this SkinnedMeshRenderer, ...)`
- `SkinnedMeshRendererUtilities.GenerateMorphCollection(this SkinnedMeshRenderer)`
