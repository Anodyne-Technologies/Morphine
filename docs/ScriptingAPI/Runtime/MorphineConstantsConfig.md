# MorphineConstantsConfig

- **Namespace:** `Anodyne.Morphine`
- **Assembly:** `Anodyne.Morphine`

Pure-C# serializable configuration controlling runtime-queryable C# Constants generation. Used by `MorphineDrip.constantsConfig`, `AnimatorAggregationRecipe.constantsConfig`, and project-level defaults (`MorphineConstantsSettings`).

---

## Enum: `ConstantsNamingStyle`

| Value | Example (`"Cheek R Puff "`) |
|---|---|
| `PascalCase = 0` *(Default)* | `CheekRPuff` |
| `ScreamingSnakeCase = 1` | `CHEEK_R_PUFF` |

---

## Fields

| Field | Type | Default | Description |
|---|---|---|---|
| `outputFolder` | `string` | `"Assets/Generated/Constants"` | Target project folder for generated `.cs` files. |
| `namespaceName` | `string` | `"Morphine.Constants"` | C# namespace wrapping the generated static class. Leave blank for global namespace. |
| `classNameOverride` | `string` | `""` | Optional explicit class name; when blank, derived from the `AnimatorController`'s sanitized name. |
| `namingStyle` | `ConstantsNamingStyle` | `PascalCase` | Identifier casing style for generated members and nested classes. |
| `includeLayers` | `bool` | `true` | Emit `Layers` (`Count`, indices, and `Names`). |
| `includeParameters` | `bool` | `true` | Emit `Parameters` (`Count` and typed `Bool`/`Float`/`Int`/`Trigger` hash classes + `Names`). |
| `includeParameterDefaults` | `bool` | `true` | Emit `Defaults` subclasses inside `Bool`/`Float`/`Int` parameter groups. |
| `includeStates` | `bool` | `true` | Emit recursive `States` nested per Layer and SubStateMachine (`ShortNameHash`, `FullPathHash`, `LayerIndex`, `Name`, `FullPath`). |
| `includeTags` | `bool` | `true` | Emit `Tags` (distinct non-empty state tag hashes + `Names`). |
| `includeTransitions` | `bool` | `true` | Emit `Transitions` for user-named transitions (`UserNameHash`, `NameHash`, `UserName`, `Name`). |
| `includeClips` | `bool` | `true` | Emit `Clips` (`const string` names of referenced `AnimationClip`s). |

---

## Methods

### `MorphineConstantsConfig Clone()`
Returns a deep copy of the configuration instance.
