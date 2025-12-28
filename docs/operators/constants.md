# Mathematical Constants

Pre-defined constants available in GenExpr and Codebox.

## Pi `pi`

- **Value:** π ≈ 3.14159265359
- **Syntax:** `pi`
- **Examples:**

```c
out = sin(pi);               // 0
out = 2 * pi;                // ~6.283 (full circle)
circumference = 2 * pi * r;
```

---

## Tau `twopi`

- **Value:** 2π ≈ 6.28318530718
- **Syntax:** `twopi`
- **Examples:**

```c
out = sin(phase * twopi);          // phase in [0,1]
angle_radians = normalized * twopi;
```

---

## Euler's Number `e`

- **Value:** e ≈ 2.71828182846
- **Syntax:** `e`
- **Examples:**

```c
out = exp(1);        // e
one = ln(e);         // 1
pow_e2 = pow(e, 2);  // ~7.389
```

---

## Degrees ⇄ Radians

- **`DEGTORAD`**: Multiply degrees by this to get radians (π / 180).
- **`RADTODEG`**: Multiply radians by this to get degrees (180 / π).
- **Syntax:** `DEGTORAD`, `RADTODEG`
- **Examples:**

```c
radians = degrees * DEGTORAD;
degrees = radians * RADTODEG;
```

---

## Derived Circles

- **`halfpi`**: π/2
- **`quarterpi`**: π/4
- **`invpi`**: 1/π
- **`invtwopi`**: 1/(2π)
- **Syntax:** use the symbol directly.
- **Examples:**

```c
out = sin(x + halfpi);     // 90° phase shift
norm = x * invtwopi;       // normalize radians to 0..1
```

---

## Other Constants

- **`root2`**: √2 ≈ 1.41421356237
- **`halfrroot2`**: √2 / 2 ≈ 0.70710678118
- **`ln2`**: ln(2) ≈ 0.69314718056
- **`ln10`**: ln(10) ≈ 2.30258509299
- **Syntax:** use the symbol directly.
- **Examples:**

```c
out = value * root2;
out = db * ln10;            // convert dB if needed
```
