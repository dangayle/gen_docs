# Range Operators

Operations for constraining, wrapping, and transforming values within ranges.

## Clamping

### Clamp `clamp`

**Syntax (GenExpr & Codebox):**
```c
clamp(x, min, max)
```

**Description:**
Constrains a value to stay within a specified range. Values below min become min, above max become max.

**Parameters:**
- `x` - Input value
- `min` - Minimum boundary
- `max` - Maximum boundary

**Returns:**
- Clamped value (always between min and max)

**Examples:**
```c
out = clamp(in, 0, 1);          // Clamp to 0-1 range
out = clamp(in, -1, 1);         // Prevent clipping
out = clamp(gain, 0, 0.5);      // Limit gain
```

**Notes:**
- Hard boundaries (discontinuous at edges)
- Values don't wrap or oscillate
- Efficient prevention of out-of-range values

---

## Wrapping

### Wrap `wrap`

**Syntax (GenExpr & Codebox):**
```c
wrap(x, min, max)
```

**Description:**
Wraps a value to stay within range. Value exceeding max wraps to min, value below min wraps to max.

**Parameters:**
- `x` - Input value
- `min` - Minimum boundary
- `max` - Maximum boundary

**Returns:**
- Wrapped value

**Examples:**
```c
phase = wrap(phase, 0, 1);            // Wrap phase 0..1
angle = wrap(angle, 0, twopi);        // Wrap angle 0..2pi
index = wrap(index, 0, table_size);   // Wrap table index
```

**Notes:**
- Continuous (no discontinuity at boundaries)
- Equivalent to modulo for positive values
- Range = max - min

---

## Folding

### Fold `fold`

**Syntax (GenExpr & Codebox):**
```c
fold(x, min, max)
```

**Description:**
Folds a value within range. Value exceeding max bounces back toward min, like a reflection.

**Parameters:**
- `x` - Input value
- `min` - Minimum boundary
- `max` - Maximum boundary

**Returns:**
- Folded value

**Examples:**
```c
out = fold(phase, 0, 1);                 // triangle from saw
out = fold(center + modulation, 0, 1);   // fold modulated value
```

**Notes:**
- Continuous and smooth at boundaries
- Creates reflection effect at limits
- Useful for oscillators and modulation

---

## Scaling and Mapping

### Scale `scale`

**Syntax (GenExpr & Codebox):**
```c
scale(x, in_min, in_max, out_min, out_max)
scale(x, in_min, in_max, out_min, out_max, curve)
```

**Description:**
Maps a value from one range to another. Optionally applies exponential curve.

**Parameters:**
- `x` - Input value
- `in-min` - Input minimum
- `in-max` - Input maximum
- `out-min` - Output minimum
- `out-max` - Output maximum
- `curve` - Exponent for curve (optional, default 1.0)

**Returns:**
- Scaled value

**Examples:**
```c
out = scale(in, -1, 1, 0, 1);                 // normalize to 0-1
freq = scale(param, 0, 1, 100, 10000, 2);     // exponential mapping
gain = scale(velocity, 0, 127, 0, 1);         // MIDI velocity to gain
```

**Notes:**
- Does NOT clamp (values outside range extend beyond output range)
- Curve > 1 = faster rise at end (exponential feel)
- Curve < 1 = slower rise at end
- Curve = 1 = linear

---

## Thresholding

### Step `step`

**Syntax (GenExpr & Codebox):**
```c
step(threshold, x)
```

**Description:**
Returns 0 if x < threshold, 1 if x >= threshold.

**Parameters:**
- `threshold` - Threshold value
- `x` - Input value

**Returns:**
- 0 or 1 (boolean)

**Examples:**
```c
out = step(0.1, in);                 // gate at threshold
square = step(0, sin(phase * twopi));
```

**Notes:**
- Hard threshold (discontinuous)
- Same as comparison (>= threshold)
- Returns 0 or 1 (not the value itself)

---

## Comparison of Range Operators

| Function | Input | Output | Behavior |
|----------|-------|--------|----------|
| `clamp` | Any | [min, max] | Hard boundaries, no change inside |
| `wrap` | Any | [min, max] | Wraps around (like modulo) |
| `fold` | Any | [min, max] | Bounces at boundaries |
| `scale` | Any | [out-min, out-max] | Maps range, no clamping |
| `step` | Any | 0 or 1 | Threshold comparison |

---

## Common Patterns

### Constrain Audio to Prevent Clipping
```c
out = clamp(in + feedback, -1, 1);
```

### Normalize to 0-1 Range
```c
normalized = scale(input, min_input, max_input, 0, 1);
```

### Exponential Frequency Mapping
```c
freq = scale(param, 0, 1, 20, 20000, 2);   // 0-1 to 20Hz-20kHz
```

### Triangle Oscillator via Fold
```c
out = fold(phasor(freq), 0, 1);    // saw -> triangle
```

### Soft Saturation (without clamp)
```c
out = tanh(in * drive);
```

### Gate with Threshold
```c
out = in * step(threshold, abs(in));
```

### Logarithmic Control Scaling
```c
freq = scale(pow(param, 2), 0, 1, 100, 20000);
```

### MIDI Velocity to Amplitude
```c
amp = scale(velocity, 0, 127, 0, 1);                 // linear
amp = scale(pow(velocity / 127.0, 2), 0, 1, 0, 1);   // curved
```

### Clamp with Soft Knees
```c
out = smoothstep(0, 0.1, clamp(x, 0, 1));
```

---

## Performance

| Function | Speed | Notes |
|----------|-------|-------|
| `clamp` | ~2 cycles | Fast boundary check |
| `wrap` | ~5 cycles | Modulo operation |
| `fold` | ~5 cycles | Reflection calculation |
| `scale` | ~10 cycles | Multiplication and addition |
| `step` | ~1 cycle | Simple comparison |

---

## Related Topics

- [Math Functions](./math.md) - clamp, wrap, fold, scale
- [Comparison](./comparison.md) - step, threshold detection
- [Control Flow](./control-flow.md) - smoothstep (smooth clamping)
- [Trigonometry](./trigonometry.md) - tanh (soft saturation)
