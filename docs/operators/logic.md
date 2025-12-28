# Logic Operators

Logical operations for boolean reasoning. All return int (0 or 1). Any non-zero input is treated as true.

## Logical NOT `!` / `not`

**Syntax:**
```c
!a
not(a)
```

**Examples:**
```c
out = !0;          // 1
out = !5;          // 0
out = !(in == 0);  // not equal to zero
```

---

## Logical AND `&&` / `and`

**Syntax:**
```c
a && b
and(a, b)
```

**Examples:**
```c
out = (in1 && in2);
out = (in > 0) && (in < 1);
```

---

## Logical OR `||` / `or`

**Syntax:**
```c
a || b
or(a, b)
```

**Examples:**
```c
out = (in1 || in2);
out = (in < 0) || (in > 1);
```

---

## Logical XOR `^^` / `xor`

**Syntax:**
```c
xor(a, b)
```

**Examples:**
```c
out = xor(in1, in2);
```

---

## Boolean Cast `bool`

**Syntax:**
```c
bool(x)
```

**Description:** Converts input to 0 or 1.

**Example:**
```c
out = bool(in1);
```
