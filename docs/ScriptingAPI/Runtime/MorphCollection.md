# MorphCollection

- **Namespace:** `Anodyne.Morphine`
- **Assembly:** `Anodyne.Morphine`
- **Implements:** `IEnumerable<Morph>`

A serializable, ordered collection of `Morph` objects—typically grouped either by `SkinnedMeshRenderer` (auto-synced `BASE` collections like `Plasto_Head`) or by semantic category (custom collections like `Expressions` or `Visemes`).

---

## Fields & Properties

| Member | Type | Description |
|---|---|---|
| `name` | `string` | Display name of the collection (also used as the layer name in the embedded Base `AnimatorController`). |
| `includeInController` | `bool` | Defaults to `true`. When `true`, `BaseAnimatorBuildService` generates an `Additive` Direct BlendTree layer for this collection. |
| `Count` | `int` | Number of morphs in the collection. |
| `this[int index]` | `Morph` | Gets or sets the morph at `index`. |

---

## Constructors

### `MorphCollection()`
Creates an empty `MorphCollection`.

### `MorphCollection(SkinnedMeshRenderer smr)`
Creates a collection named `smr.name` populated with one single-target `Morph` for each blend shape index on `smr.sharedMesh`.

---

## Methods

| Signature | Description |
|---|---|
| `void Add(Morph morph)` | Appends `morph` to the collection. |
| `void Remove(Morph morph)` | Removes the first occurrence of `morph`. |
| `int IndexOf(Morph morph)` | Returns the zero-based index of `morph`, or `-1` if absent. |
| `void Insert(int index, Morph morph)` | Inserts `morph` at `index`. |
| `void RemoveAt(int index)` | Removes the morph at `index`. |
| `void Clear()` | Removes all morphs from the collection. |
| `IEnumerator<Morph> GetEnumerator()` | Enables `foreach` iteration over all contained morphs. |

---

## Example

```csharp
using Anodyne.Morphine;
using UnityEngine;

public static class CollectionExample
{
    public static MorphCollection BuildPlastoExpressions(SkinnedMeshRenderer plastoSmr)
    {
        var expressions = new MorphCollection
        {
            name = "Expressions",
            includeInController = true
        };

        var happy = new Morph { Name = "Happy" };
        happy.AddMorphTarget(new BlendShapeData(plastoSmr, 3), 85f); // Smile R
        happy.AddMorphTarget(new BlendShapeData(plastoSmr, 4), 85f); // Smile L
        expressions.Add(happy);

        return expressions;
    }
}
```
