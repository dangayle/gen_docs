# GenExpr Examples (C-style)

Short, ready-to-use snippets in C-style GenExpr/Codebox syntax.

## Basics

```c
out = in * 0.5;                 // scale input
sum = in1 + in2;                // two-inlet sum
clamped = clamp(in, -1, 1);     // prevent clipping
```

## Conditionals

```c
out = (in > threshold) ? in : 0;          // gate
soft_gate = smoothstep(t-0.1, t+0.1, in); // soften threshold
```

## Oscillators

```c
phase = phasor(freq);                 // 0..1 ramp
sine = sin(phase * twopi);            // sine osc
saw = phase * 2 - 1;                  // saw -1..1
square = (phase > 0.5) ? 1 : -1;      // square
triangle = 1 - abs(phase * 2 - 1);    // triangle
```

## Envelopes

```c
// AR envelope driven by gate
if (gate > 0) {
  env = mix(env, 1, attack);          // rise toward 1
} else {
  env = mix(env, 0, release);         // fall toward 0
}
out = in * env;
```

## Filters (one-pole)

```c
// Low-pass: y[n] = y[n-1]*(1-a) + x[n]*a
alpha = 0.05;
lp = history(lp);
lp = lp * (1 - alpha) + in * alpha;
out = lp;

// High-pass via differentiator + LP
hp = in - history(in);
hp = hp * (1 - alpha) + history(hp) * alpha;
out = hp;
```

## Noise

```c
white = noise();
// Simple pink-ish using 3-sample average
n0 = noise();
n1 = history(n0);
n2 = history(n1);
pink = (n0 + n1 + n2) * 0.33;
```

## Delays

```c
// One-sample delay
z1 = history(in);
out = z1;

// Simple comb feedback
fb = 0.7;
delay = history(in + delay * fb, 441); // 10ms @44.1k
out = delay;
```

## Waveshaping

```c
// Soft clip with tanh
a = in * drive;
out = tanh(a);

// Hard clip
out = clamp(in, -1, 1);

// Quadratic fold
out = 1 - abs(in * 2 - 1);
```

## Parameter Smoothing

```c
// Smooth param to avoid zipper noise
param target(0.5);
smooth = mix(history(smooth), target, 0.001);
out = mix(in1, in2, smooth);
```

## MIDI Note to Frequency

```c
// midi_note: 0-127
freq = 440 * exp2((midi_note - 69) / 12.0);
```

## Stereo Utilities

```c
// Mid/Side encode
t = 0.5;
mid = (left + right) * t;
side = (left - right) * t;

// Mid/Side decode
left = mid + side;
right = mid - side;
```

## Pan

```c
// Equal-power pan control 0..1
param pan(0.5);
angle = pan * (pi * 0.5);
cl = cos(angle);
cr = sin(angle);
left = in * cl;
right = in * cr;
```

## Waveset Reset (Sync)

```c
phase = history(phase);
phase += freq / samplerate;
if (phase >= 1) phase -= 1;          // wrap
if (sync > 0) phase = 0;             // hard sync
out = sin(phase * twopi);
```

## LFO to Modulate Filter

```c
lfo = sin(phasor(0.5) * twopi);      // 0.5 Hz LFO
cutoff = mix(200, 2000, (lfo + 1) * 0.5);
// use cutoff with your filter operator
```

## Peak and RMS

```c
// Peak hold with decay
peak = history(peak);
peak = (abs(in) > peak) ? abs(in) : peak * 0.99;

// RMS (running mean-square)
rms_sq = history(rms_sq);
rms_sq = rms_sq * 0.99 + in * in * 0.01;
rms = sqrt(rms_sq);
```

## Utility Macros

```c
// Map 0-1 knob to Hz (20-20k) with curve
hz = scale(knob, 0, 1, 20, 20000, 2);

// Exponential decay over time samples
decay = exp(-time * rate);
```
