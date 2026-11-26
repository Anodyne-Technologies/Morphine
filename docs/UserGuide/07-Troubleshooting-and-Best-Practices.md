# Troubleshooting & Best Practices

Key Unity 6 Mecanim behaviors and Morphine design patterns to keep your character animation pipeline predictable.

---

## 1. Additive Morph Layers Evaluate to `0` Without a Zero-Pose Reference Clip

**Symptom:** Custom or manually created 1-frame blend shape clips produce `0` deformation when placed on an `Additive` Animator layer.

**Cause:** Unity Mecanim evaluates Additive layers as `clip(t) - refPose(0)`. If `hasAdditiveReferencePose` is false (or `additiveReferencePoseClip` has no curves for those blend shapes), Unity subtracts frame 0 of the clip from itself (`weight - weight = 0`).

**Solution:** Always build morph clips through Morphine (**Build / Sync Clips** or `MorphClipBuildService`). Morphine automatically maintains the embedded zero-pose clip (`__Morphine_Empty`) with explicit `0f` curves for every blend shape on the prefab and binds it as every morph clip's `additiveReferencePoseClip`.

---

## 2. `AvatarMask` Transform List Blocks BlendShapes If Populated

**Symptom:** Blend shapes stop animating after editing `drip.overrideMask` in the Inspector and importing a transform skeleton into the mask.

**Cause:** On a `MorphineDrip`'s embedded `AvatarMask`, all Humanoid body parts are disabled and the transform mask is intentionally left **empty** (`transformCount == 0`). That masks out Humanoid skeletal bones while allowing `SkinnedMeshRenderer.blendShape.*` curves to pass through. If you populate the transform hierarchy on the mask and leave the renderer's GameObject unchecked, Unity blocks blend shape curves on that renderer too.

**Solution:** Keep `overrideMask`'s transform list empty (the default created by `AnimationUtilities.EnsureOverrideMask`), or if you must use a transform mask, ensure every `SkinnedMeshRenderer` GameObject path in `UsableSkins` is checked active.

---

## 3. Source Controller Layer 0 Weight Quirk During Aggregation

**Symptom:** A manually merged controller's second layer has weight `0` and never plays.

**Cause:** Unity often serializes Layer 0 of a standalone `AnimatorController` with `defaultWeight = 0` even though Layer 0 always evaluates at weight `1`.

**Solution:** Use `Tools > Morphine > Animator Aggregator`—it automatically detects Layer 0 `defaultWeight == 0` when placing a source's Layer 0 at target index `> 0` and promotes it to `1.0` (indicated by the **`wt:1 (0->1)`** badge in the Dry-Run Preview).

---

## 4. Off-Screen / Test `Animator.Update()` Returns `0`

**Symptom:** Driving an `Animator` in an EditMode test or on an off-screen character via `animator.Update(dt)` leaves blend shape weights at `0`.

**Cause:** By default, `Animator.cullingMode` is `CullUpdateTransforms` or `CullCompletely`, so Unity skips evaluating unrendered meshes.

**Solution:** Set `animator.cullingMode = AnimatorCullingMode.AlwaysAnimate` when testing off-screen or driving facial rigs that must update regardless of camera frustum visibility.

---

## 5. `StateMachineBehaviour` Subclasses Must Live in Runtime Assemblies

**Symptom:** `AddStateMachineBehaviour<T>()` logs `Can't find monoscript for class '...'` when authoring or testing controllers.

**Cause:** Unity only permits `StateMachineBehaviour` scripts compiled in Runtime (non-Editor) assemblies.

**Solution:** Always place custom `StateMachineBehaviour` classes in a Runtime `.asmdef` (never in an `"includePlatforms": ["Editor"]` assembly). `AnimatorAggregatorService` will automatically deep-clone those behaviours and preserve all serialized field values during aggregation.

---

## 6. Build Size & Asset Hygiene

- **Keep Aggregation Recipes Editor-Only**: `AnimatorAggregationRecipe` lives in `Anodyne.Morphine.Editor`, so your source controllers (`Locomotion.controller`, `UpperBody.controller`, etc.) are never pulled into Player builds—only the final baked output `.controller` is referenced by your prefabs.
- **Exclude Raw Base Skin Layers When Unused**: If your character only plays authored custom expressions (like `Expressions` or `Visemes`), uncheck **Include in Controller** on the raw `BASE` skin collection (`Plasto_Head`) in the **Morphs** or **Build & Constants** tab to keep your runtime `AnimatorController` layer and parameter counts lean.
- **Documentation Folder (`Documentation~/`)**: Morphine's documentation and screenshots live in `Assets/Morphine/Documentation~/` (trailing tilde) so Unity skips importing documentation images as project textures.
