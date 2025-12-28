# Comparison Operators

Comparison operators return int (0 or 1). Any non-zero input is treated as true.

## Equal `==`

**Syntax:** `a == b`

**Example:**
```c
out = (in1 == in2);
```

---

## Not Equal `!=`

**Syntax:** `a != b`

**Example:**
```c
out = (in1 != in2);
```

---

## Greater Than `>`

**Syntax:** `a > b`

**Example:**
```c
out = (in1 > threshold);
```

---

## Less Than `<`

**Syntax:** `a < b`

**Example:**
```c
out = (in1 < threshold);
```

---

## Greater or Equal `>=`

**Syntax:** `a >= b`

**Example:**
```c
out = (in1 >= 0);
```

---

## Less or Equal `<=`

**Syntax:** `a <= b`

**Example:**
```c
out = (in1 <= 1);
```

---

## Step `step`

**Syntax:** `step(edge, x)`

**Description:** Returns 0 when `x < edge`, else 1.

**Example:**
```c
out = step(0.5, in1);
```
