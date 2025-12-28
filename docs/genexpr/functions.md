# GenExpr Function Definitions

Functions in GenExpr encapsulate reusable computations. They use C-like syntax and are inlined by the compiler.

## Basic Function Definition

```c
add(a, b) {
  return a + b;
}

result = add(5, 3);  // result = 8
```

## Parameters

```c
square(x) {
  return x * x;
}

y = square(5);       // y = 25
```

GenExpr does not support default parameter values inside functions; use `param` bindings defined outside if defaults are needed.

## Return Values

### Single Return

The last expression (or an explicit `return`) is returned:

```c
add_one(x) {
  return x + 1;
}

y = add_one(5);  // y = 6
```

### Multiple Returns

Use comma-separated returns and assignments:

```c
cartesian_to_polar(x, y) {
  r = sqrt(x * x + y * y);
  theta = atan2(y, x);
  return r, theta;
}

magnitude, angle = cartesian_to_polar(3, 4);
```

## Conditional Logic

GenExpr offers expression-level selection. Use ternary or the `if` operator inside functions:

```c
absolute_value(x) {
  return (x >= 0) ? x : -x;
  // or: return if(x >= 0, x, -x);
}
```

Imperative loops (`for`, `while`, `break`, `continue`) are not available in GenExpr; use Codebox if you need them.

## Scope and State

- Variables are global to the Gen patcher; function-local names are not isolated. Prefix to avoid collisions.
- Persistent state must use `history` (or related delay/state operators); plain variables do not persist across samples.

```c
accumulating(input) {
  acc = history(0);
  acc = acc + input;
  return acc;
}

out = accumulating(in1);
```

## Composition

Functions can call other functions; calls are inlined:

```c
double(x) { return x * 2; }
square(x) { return x * x; }

result = square(double(5));  // 100
```

## Unsupported Patterns

- Recursion is not supported.
- Unbounded or imperative looping is not supported.
- Prefix/Lisp-style `defun` or `set!` forms are not part of GenExpr.

## Debugging Tips

- Test with literals first, then connect inlets.
- Return intermediates temporarily (e.g., `return temp;`) to inspect values via extra outlets if needed.

## Related Topics

- [GenExpr Syntax](./syntax.md)
- [Operators](../operators/index.md)
- [Type System](../concepts/types.md)
