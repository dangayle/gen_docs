# Performance Guidelines

Practical tips to keep Gen patches efficient. All code is per-sample; small optimizations add up.

## General Rules
- Prefer multiplies over divides; precompute reciprocals.
- Avoid pow for simple exponents; use `exp2`, `sqrt`, squaring, or cubing directly.
- Keep `history` counts constant; dynamic counts aren’t allowed and hurt clarity.
- Eliminate unused branches; short-circuit with early returns where possible.
- Hoist constants out of sample-rate code when feasible.

## Cheap vs Costly Ops (rough order)
- Very fast: add, sub, mul, abs, min/max, clamp, bitwise.
- Fast: `exp2`, `wrap`, `fold`, `mix`, `smoothstep`, comparisons, `history`.
- Moderate: `div`, `mod`, `log2`, `log10`, `ln`, `sin`, `cos`, `tanh`, `hypot`.
- Slower: `pow`, `atan`, `atan2`, `exp`.
- Approximations: `fastsin`, `fastcos`, `fasttan`, `fastexp`, `fastpow` (~5–10 cycles, less accurate).

## Branching
- Prefer expressions over deep `if` chains when readable.
- Use ternaries for simple gates: `out = (in > t) ? in : 0;`.
- For switches, group by common setup and switch only the essentials.

## Memory and State
- Each `history` allocates state; keep the count minimal and reuse where sensible.
- Long delays are expensive; if you need many taps, consider external buffers or more efficient structures.
- DC-block feedback paths to prevent runaway drift: `out = dcblock(out);`.

## Denormals
- Extremely small values slow CPUs. Add a tiny dither if needed: `x += 1e-20;` or rely on `dcblock`.

## Tables and Lookup
- Use integer indices: `idx = int(phase * size); value = lookup(table, idx);`.
- Precompute tables offline when possible; keep sizes power-of-two for cheap wrapping.

## Vector-Friendly Patterns
- Use simple arithmetic in tight loops; avoid `pow`/`atan2` in inner audio-rate hot paths when an approximation suffices.
- Prefer `exp2` for pitch math: `freq = base * exp2(semitones / 12);`.

## Smoothing and Modulation
- Smooth parameters to avoid zipper noise: `smooth = mix(prev, target, 0.001);`.
- Keep LFO rates low; avoid recalculating expensive trig if a phasor-based oscillator works.

## Profiling Checklist
- Count divisions and `pow` calls; replace with multiplies/`exp2`/`sqrt`.
- Check for redundant `history` or duplicate computations.
- Ensure clamps/limits exist on feedback to avoid blowups.
- Verify no unintentional implicit int division (`int / int`).

## Reference Costs (approximate cycles)
- `+`, `-`, `*`: ~1
- `clamp`: ~2
- `wrap`, `fold`, `mix`, `smoothstep`: ~5
- `div`, `mod`: ~10–20
- `exp2`, `log2`, `log10`, `ln`, `sin`, `cos`, `tanh`, `hypot`: ~20–50
- `pow`, `atan`, `atan2`, `exp`: ~50–100
- `fastsin`, `fastcos`, `fasttan`, `fastexp`, `fastpow`: ~5–10 (lower accuracy)

## Code Patterns
```c
// Replace division by constant with multiply by reciprocal
out = in * 0.14285714;   // instead of in / 7

// Cheap envelope smoothing
smooth = mix(history(smooth), target, 0.001);

// Safe feedback with clamp and dcblock
fb = clamp(fb, -0.99, 0.99);
state = dcblock(in + fb * history(state));

// Power-of-two wrap using bitmask (when size is power-of-two)
idx = int(phase * size);
idx = idx & (size - 1);
value = table[idx];
```

## Related Topics
- [Execution Model](./execution-model.md)
- [Memory & State](./memory-state.md)
- [Operators](../operators/)
