# PartialExpressionEvaluator

Early implementation of a partial evaluator of expression trees.

## History

Many expressions that land in Nuqleon query engines are the result of partial evaluation on the client, e.g. when eliminating references to closed over local variables. For example:

```csharp
xs.Where(x => x > a)
```

where `a` is a local variable. While the expression tree generated by the C# compiler will access the local using an `Expression.Field(Expression.Constant(closure), field_for_a)` expression, rewrites in client libraries eliminate this reference by performing partial evaluation, thus reducing the node to `Expression.Constant(value_of_a)`.

However, in many cases there's more reduction that can be done once constants have been inlined. For example, a query expression could look like:

```charp
xs.Where(x => x > s.Length)
```

where `s` is a local variable of type `string`. After partial evaluation of the closure field access into a string constant, there's a further opportunity to evaluate the `Length` property on the string literal, thus avoiding the allocation of the string altogether, as well as eliminating the runtime overhead to get the length.

The partial expression tree evaluator provides an early implementation of a mechanism that can partially evaluate subexpressions. It's been mostly superseded by the work in `Nuqleon.Linq.Expressions.Optimizers` which provides similar mechanisms but with more flexibility (through the concept of semantic providers and pluggable evaluators).