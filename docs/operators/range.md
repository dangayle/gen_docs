# Range Operators

Operations for constraining, wrapping, and transforming values within ranges.

## Clamping

### Clamp `clamp`

**Syntax:**
```scheme
; GenExpr
(clamp x min max)

; Codebox
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

GenExpr:
```scheme
; Clamp to 0-1 range
(clamp x 0 1)

; Clamp audio to prevent clipping
(clamp in -1 1)

; Gate with soft limits
(clamp gain 0 0.5)
```

Codebox:
```c
// Clamp to 0-1
y = clamp(x, 0, 1);

// Prevent clipping
y = clamp(x, -1, 1);

// Limit gain
y = clamp(gain, 0, 0.5);
```

**Notes:**
- Hard boundaries (discontinuous at edges)
- Values don't wrap or oscillate
- Efficient prevention of out-of-range values

---

## Wrapping

### Wrap `wrap`

**Syntax:**
```scheme
; GenExpr
(wrap x min max)

; Codebox
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

GenExpr:
```scheme
; Wrap phase (0 to 1)
(wrap phase 0 1)

; Wrap angle (0 to 2π)
(wrap angle 0 twopi)

; Wrap table index
(wrap index 0 table-size)
```

Codebox:
```c
// Wrap phase
phase = wrap(phase, 0, 1);

// Wrap angle
angle = wrap(angle, 0, twopi);
```

**Notes:**
- Continuous (no discontinuity at boundaries)
- Equivalent to modulo for positive values
- Range = max - min

---

## Folding

### Fold `fold`

**Syntax:**
```scheme
; GenExpr
(fold x min max)

; Codebox
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

GenExpr:
```scheme
; Fold oscillator (creates triangle from saw)
(fold phase 0 1)

; Fold modulated value
(fold (+ center modulation) 0 1)
```

Codebox:
```c
// Fold oscillator
y = fold(phase, 0, 1);

// Fold modulated signal
y = fold(center + modulation, 0, 1);
```

**Notes:**
- Continuous and smooth at boundaries
- Creates reflection effect at limits
- Useful for oscillators and modulation

---

## Scaling and Mapping

### Scale `scale`

**Syntax:**
```scheme
; GenExpr
(scale x in-min in-max out-min out-max [curve])

; Codebox
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

GenExpr:
```scheme
; Normalize input to 0-1
(scale in -1 1 0 1)

; Map parameter to frequency (exponential)
(scale param 0 1 100 10000 2)

; Convert MIDI velocity to gain
(scale velocity 0 127 0 1)
```

Codebox:
```c
// Normalize
y = scale(x, -1, 1, 0, 1);

// Exponential mapping
freq = scale(param, 0, 1, 100, 10000, 2);

// Linear MIDI to gain
gain = scale(velocity, 0, 127, 0, 1);
```

**Notes:**
- Does NOT clamp (values outside range extend beyond output range)
- Curve > 1 = faster rise at end (exponential feel)
- Curve < 1 = slower rise at end
- Curve = 1 = linear

---

## Thresholding

### Step `step`

**Syntax:**
```scheme
; GenExpr
(step threshold x)

; Codebox
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

GenExpr:
```scheme
; Simple gate
(step 0.1 in)

; Create square wave from sine
(step 0 (sin (* phase twopi)))
```

Codebox:
```c
// Gate at threshold
y = step(0.1, x);

// Square wave from sine
y = step(0, sin(phase * twopi));
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
```scheme
; GenExpr
(clamp (+ in feedback) -1 1)

; Codebox
y = clamp(x + feedback, -1, 1);
```

### Normalize to 0-1 Range
```scheme
; GenExpr
normalized = (scale input min-input max-input 0 1)

; Codebox
normalized = scale(input, min_input, max_input, 0, 1);
```

### Exponential Frequency Mapping
```scheme
; GenExpr
; Map 0-1 parameter to 20Hz-20kHz with exponential curve
freq = (scale param 0 1 20 20000 2)

; Codebox
freq = scale(param, 0, 1, 20, 20000, 2);
```

### Triangle Oscillator via Fold
```scheme
; GenExpr
; Sawtooth wrapped/folded becomes triangle
(fold (phasor freq) 0 1)

; Codebox
y = fold(phasor(freq), 0, 1);
```

### Soft Saturation (without clamp)
```scheme
; GenExpr
; Smooth curve without hard clipping
(tanh (* in drive))

; Codebox
y = tanh(x * drive);
```

### Gate with Threshold
```scheme
; GenExpr
; Gate if above threshold
(* in (step threshold (abs in)))

; Codebox
y = x * step(threshold, abs(x));
```

### Logarithmic Control Scaling
```scheme
; GenExpr
; Exponential curve for frequency knob
(scale (pow param-0-1 2) 0 1 100 20000)

; Codebox
freq = scale(pow(param, 2), 0, 1, 100, 20000);
```

### MIDI Velocity to Amplitude
```scheme
; GenExpr
; Map 0-127 to 0-1 linearly
amp = (scale velocity 0 127 0 1)

; Exponential velocity curve
amp = (scale (pow (/ velocity 127) 2) 0 1 0 1)

; Codebox
amp = scale(velocity, 0, 127, 0, 1);
amp = scale(pow(velocity / 127.0, 2), 0, 1, 0, 1);
```

### Clamp with Soft Knees
```scheme
; GenExpr
; Smooth clamping using smoothstep
(smoothstep 0 0.1 (clamp x 0 1))

; Codebox
y = smoothstep(0, 0.1, clamp(x, 0, 1));
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
