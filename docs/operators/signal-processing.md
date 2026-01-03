# Signal Processing Operators

Audio DSP-specific operators for oscillation, noise, filtering, and analysis.

## History and State

### History `history`

**Syntax (GenExpr & Codebox):**
```c
history(x)
history(x, delay_samples)
```

**Description:**
Returns a previous value of `x`. Fundamental for feedback and state.

**Examples:**
```c
// 1-sample delay
float delayed = history(in);

// Multi-sample delay
float y0 = in;
float y1 = history(y0, 1);
float y2 = history(y1, 1);

// Simple moving average
float avg = (in + history(in)) * 0.5;

// Difference (delta)
float delta = in - history(in);
```

**Notes:**
- Each call creates a state variable
- `delay_samples` must be constant (compile-time)
- Essential for filters and state machines

---

### DC Block `dcblock`

**Syntax (GenExpr & Codebox):**
```c
dcblock(x)
dcblock(x, cutoff)
```

**Description:**
High-pass filter removing DC and very low frequencies.

**Examples:**
```c
filtered = dcblock(in);          // default ~5 Hz cutoff
filtered = dcblock(in, 10);      // explicit cutoff
```

**Notes:**
- Include after operations that introduce DC
- Good before peak/RMS analysis

---

## Oscillators

### Phasor `phasor`

**Syntax (GenExpr & Codebox):**
```c
phasor(frequency)
```

**Description:**
Sawtooth oscillator returning 0..1; wraps automatically at 1.

**Examples:**
```c
phase = phasor(440);                     // basic phasor
square = (phasor(440) < 0.5) ? 1 : -1;   // square from phasor
triangle = 1 - abs(phasor(440) * 2 - 1); // triangle from phasor
```

**Notes:**
- Output range [0, 1)
- Frequency in Hz (sample-rate aware)

---

## Noise

### Noise `noise`

**Syntax (GenExpr & Codebox):**
```c
noise()
noise(seed)
```

**Description:**
Returns white noise in range [-1, 1].

**Examples:**
```c
float n0 = noise();
float n1 = history(n0);
float n2 = history(n1);
float pink = (n0 + n1 + n2) * 0.33;   // simple pink approximation
```

**Notes:**
- New random value each sample
- Use averaging/history for pink-ish noise

---

## Coordinate Conversion

### Cartesian to Polar `cartopol`

**Syntax (GenExpr & Codebox):**
```c
cartopol(x, y, &mag, &angle)
```

**Description:**
Converts Cartesian `(x, y)` to polar `(magnitude, angle)`; angle in radians.

**Examples:**
```c
float mag, angle;
cartopol(x, y, &mag, &angle);
angle += rotation;
poltocar(mag, angle, &x, &y);   // rotate and convert back
```

---

### Polar to Cartesian `poltocar`

**Syntax (GenExpr & Codebox):**
```c
poltocar(magnitude, angle, &x, &y)
```

**Description:**
Converts polar `(magnitude, angle)` to Cartesian `(x, y)`.

**Examples:**
```c
float angle = phasor(1) * twopi;   // 1 Hz rotation
float x, y;
poltocar(1.0, angle, &x, &y);
```

---

## Analysis Operators

### Hypot `hypot`

**Syntax (GenExpr & Codebox):**
```c
hypot(x, y)
```

**Description:**
Magnitude of a 2D vector; more stable than `sqrt(x*x + y*y)`.

**Examples:**
```c
mag = hypot(x, y);                     // vector magnitude
rms = hypot(left, right) / sqrt(2);    // stereo RMS
```

---

## Performance

| Function | Speed | Notes |
|----------|-------|-------|
| `history` | ~1 cycle | State variable |
| `dcblock` | ~5 cycles | IIR high-pass |
| `phasor` | ~10 cycles | Automatic phase wrap |
| `noise` | ~20 cycles | RNG with state |
| `cartopol` | ~30 cycles | Uses atan2 |
| `poltocar` | ~30 cycles | Uses sin/cos |
| `hypot` | ~15 cycles | 2D magnitude |

---

## Common Patterns

### Simple Low-Pass Filter (1-pole IIR)
```c
lpf_out = 0.9 * history(lpf_out) + 0.1 * in;
```

### Simple High-Pass Filter (Differentiator)
```c
hpf_out = in - history(in);
```

### Stereo to Mid-Side
```c
mid = (left + right) * 0.5;
side = (left - right) * 0.5;
```

### Mid-Side to Stereo
```c
left = mid + side;
right = mid - side;
```

### Peak Detector
```c
peak = history(peak);
peak = (abs(in) > peak) ? abs(in) : peak * 0.9;
```

### RMS Detector
```c
rms_sq = 0.99 * history(rms_sq) + 0.01 * (in * in);
rms = sqrt(rms_sq);
```

### Rotating Point (XY Pad)
```c
float angle = phasor(1) * twopi;   // 1 Hz rotation
poltocar(1.0, angle, &x, &y);
```

### Feedback Delay Network (3 taps)
```c
float d1 = history(d1, 441);
float d2 = history(d2, 441);
float d3 = history(d3, 441);

d1 = 0.8 * d1 + 0.2 * in;
d2 = 0.7 * d2 + 0.1 * d1;
d3 = 0.6 * d3 + 0.1 * d2;
```

---

## Related Topics

- [Math Functions](./math.md) - abs, clamp, scale, hypot
- [Trigonometry](./trigonometry.md) - sin, cos, atan2
- [Arithmetic](./arithmetic.md) - Basic signal mixing
- [Concepts: Memory & State](../concepts/memory-state.md) - Feedback patterns
