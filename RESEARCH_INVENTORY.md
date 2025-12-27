# Gen Documentation Research Inventory

## Overview
This document catalogs all sources used to create comprehensive Gen documentation. Data collected from Max 9 installation and official Cycling '74 resources.

## Collected Resources

### 1. Max 9 Built-in Documentation Files
All files copied from `/Applications/Max.app/Contents/Resources/C74/`

**JSON Reference Documentation** (`docs/userguide/content/gen/`)
- `_gen_overview.json` - High-level Gen overview and introduction
- `gen_genexpr.json` - GenExpr language syntax and semantics reference
- `gen_common_operators.json` - Operators available in all Gen domains (arithmetic, logic, etc.)
- `gen~_operators.json` - Audio/signal processing operators for gen~ and gen.codebox~
- `gen_jitter_operators.json` - Jitter-specific operators for jit.gen and related objects

**Max Help Files** (`.maxhelp` format - JSON-serialized Max patchers)
- `gen~.maxhelp` - Comprehensive gen~ object help/tutorial (1.5MB)
- `gen.codebox~.maxhelp` - Codebox object help/tutorial (22KB)

### 2. Remote/Online Resources (Not yet collected, to review)

**Official Cycling '74 Documentation**
- URL: https://docs.cycling74.com/
- Contains: Complete Gen reference, GenExpr language documentation, all operators
- Status: Should cross-reference with local files for completeness

## Data Organization Structure

```
research/
├── JSON files (reference format)
│   ├── _gen_overview.json
│   ├── gen_genexpr.json
│   ├── gen_common_operators.json
│   ├── gen~_operators.json
│   └── gen_jitter_operators.json
└── Max help files (interactive documentation)
    ├── gen~.maxhelp
    └── gen.codebox~.maxhelp
```

## What These Resources Cover

### GenExpr Language
- Syntax fundamentals (operators, expressions, statements)
- Function definitions
- Control flow (if/else, for, while, break, continue)
- Multiple return values
- Parameters and attributes
- Variable scoping and type system
- Comments
- Abstractions and requires

### Operators (All Domains)
- **Common Operators**: Available in GenExpr and all Gen objects
  - Arithmetic: `+`, `-`, `*`, `/`, `%`, etc.
  - Comparison: `<`, `>`, `==`, `!=`, `<=`, `>=`
  - Logical: `&&`, `||`, `!`
  - Bitwise: `&`, `|`, `^`, `<<`, `>>`, `~`
  - Type conversions
  - Math functions: `sin`, `cos`, `tan`, `sqrt`, `abs`, `min`, `max`, etc.

- **Audio/Signal Operators** (gen~ and gen.codebox~)
  - Signal processing functions
  - Delay/history operations
  - Waveform generation
  - Filtering operations
  - Domain-specific signal operations

- **Jitter Operators** (jit.gen, jit.pix, jit.gl.pix)
  - Image/matrix sampling
  - Coordinate operations (norm, snorm, cell, dim)
  - 2D/3D operations
  - Texture operations
  - Color operations

### Type System
- Type coercion and conversion rules
- Supported types (int, float, signal types)
- Type inference
- Vector operations (if applicable)

### Execution Model
- Initialization vs. execution phases
- Inlet/outlet semantics
- Block-rate processing
- Sample-rate processing
- State management and variables

## Data Extraction Next Steps

1. **Parse JSON files** to extract:
   - Operator name and category
   - Function signatures
   - Parameter descriptions
   - Return value documentation
   - Usage examples (if included)
   - Cross-references

2. **Extract text from .maxhelp files** to get:
   - Interactive examples
   - Conceptual explanations
   - Usage patterns
   - Implementation notes

3. **Cross-reference with online docs** (docs.cycling74.com) to:
   - Fill any gaps
   - Verify accuracy
   - Get latest updates
   - Confirm complete operator coverage

4. **Organize by scope**:
   - GenExpr syntax and semantics
   - Common operators (all domains)
   - Audio-specific operators
   - Jitter-specific operators (scope limited but useful reference)

## Coverage Assessment

### Complete
- GenExpr language syntax ✓
- Common operators ✓
- Type system basics ✓
- Audio operators ✓

### To Verify
- Jitter operators (not primary focus, but may reference)
- Advanced concepts (optimization, constraints)
- Edge cases and gotchas

## File Sizes (for reference)
- gen~.maxhelp: 1.5 MB (comprehensive)
- gen.codebox~.maxhelp: 22 KB
- JSON files: ~65-150 KB each
- Total research material: ~2.5 MB

## Notes for Documentation Team

1. The JSON files are from Max 9's built-in help system and represent the authoritative reference for this version.

2. The .maxhelp files are interactive Max patchers showing real examples and usage patterns.

3. All operator documentation should cross-reference the source material to ensure accuracy for LLM training.

4. Pay special attention to:
   - Operator signatures and parameter types
   - Type coercion behavior (important for LLMs)
   - Execution context restrictions (init vs. runtime)
   - Performance notes where applicable
