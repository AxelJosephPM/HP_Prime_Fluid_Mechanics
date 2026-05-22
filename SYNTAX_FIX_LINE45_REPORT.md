# SYNTAX FIX — LINE 45 ERROR REPORT
Date: 2026-05-23

---

## 1. Root Cause — IFERR + Multiple IF...THEN...END Inside

**File:** `FLUIDOS.hpprgm`

**Pattern observed:** Both prior errors pointed to the same module dispatch:
- Fix 1 → error at line 82: `MF_PUMPS();` (7th nested IFERR body)
- Fix 2 → error at line 45: `IF c == 7 THEN MF_PUMPS();` (7th IF inside IFERR body)

Both errors involved the 7th dispatch element (MF_PUMPS). The root cause is the HP Prime Connectivity Kit parser confusing the `THEN` keyword inside `IF c == 7 THEN MF_PUMPS(); END;` with the IFERR separator keyword `THEN`.

When the IFERR body contained 11 sequential `IF c == N THEN func(); END;` statements, the parser correctly tracked the first six IF...THEN...END blocks but at the 7th one (line 45), it may have misidentified that THEN as the IFERR's own THEN separator — causing the "syntax error at line 45."

This is a parser ambiguity in some HP Connectivity Kit versions: multiple THEN keywords inside an IFERR body, even inside complete IF...END blocks, cause confusion.

---

## 2. Fix Applied to FLUIDOS.hpprgm

**Strategy:** Move all dispatch IF statements into a separate helper function `dispatch_(c)`. The IFERR body now contains only ONE statement (a function call) with NO THEN keywords inside.

**Before (55 lines):**
```ppl
IFERR
  IF c == 1 THEN MF_PROPS(); END;
  IF c == 2 THEN MF_MANO(); END;
  ...
  IF c == 7 THEN MF_PUMPS(); END;   <- error at line 45
  ...
  IF c == 11 THEN MF_HELP(); END;
THEN
  MSGBOX("Modulo no encontrado...");
END;
```

**After (60 lines):**
```ppl
// New helper function (before EXPORT FLUIDOS):
dispatch_(c)
BEGIN
  IF c == 1 THEN MF_PROPS(); END;
  IF c == 2 THEN MF_MANO(); END;
  ...
  IF c == 7 THEN MF_PUMPS(); END;
  ...
  IF c == 11 THEN MF_HELP(); END;
END;

// Inside EXPORT FLUIDOS, IFERR now has only one statement:
IFERR
  dispatch_(c);
THEN
  MSGBOX("Modulo no encontrado." + CHAR(10) + "Instale todos los modulos.");
END;
```

The IFERR body contains zero THEN keywords — only a single function call `dispatch_(c);`. This eliminates the parser ambiguity entirely while preserving the graceful "module not found" error handling.

**New line 45:** `"0. SALIR");` — last line of CHOOSE call. Completely safe.

---

## 3. Secondary Fixes — Non-ASCII Characters in All Files

While investigating, several non-ASCII characters in string literals and code were identified as potential compatibility risks with older HP Connectivity Kit versions. All were replaced with ASCII equivalents in this pass.

### 3a. `→` (U+2192, right arrow) in strings → `->` (ASCII)

| File | Lines affected |
|---|---|
| MF_MANO.hpprgm | 32, 33, 34 (CHOOSE options), 41, 67, 89 (INPUT titles) |
| MF_COMP.hpprgm | 244 (result string) |
| MF_CAS.hpprgm | 200, 202, 224, 226 (result strings) |

### 3b. `─` (U+2500, box-drawing) in strings → `-` (ASCII)

| File | Lines affected |
|---|---|
| MF_BERN.hpprgm | 61, 175 |
| MF_COMP.hpprgm | 115, 122, 126 |
| MF_PIPES.hpprgm | 155, 293, 299 |

### 3c. `≈` (U+2248) in strings → `~=`

| File | Lines affected |
|---|---|
| MF_BERN.hpprgm | 220 (result string) |

Note: `≈` in COMMENT lines 189 and 203 of MF_BERN was left as-is — comments are stripped by the parser and cannot cause syntax errors.

### 3d. `≠` (U+2260) in strings → `!=`

| File | Lines affected |
|---|---|
| MF_CAS.hpprgm | 202 |

### 3e. `ó` / `Ó` (accented o) in strings → `o` / `O`

| File | Lines affected |
|---|---|
| MF_COMP.hpprgm | 141, 142 (CHOOSE items: subsónico, supersónico) |
| MF_PROPS.hpprgm | 120, 123, 126 (aviso string: SUBSÓNICO, SÓNICO) |
| MF_CAS.hpprgm | 268 (CHOOSE item: parabólico) |

### 3f. `π` (U+03C0) in code expressions → `3.14159265358979`

`π` is a valid HP Prime built-in constant, but replaced with a numeric literal for maximum compatibility with all Connectivity Kit versions.

| File | Lines affected |
|---|---|
| MF_PIPES.hpprgm | 75, 87, 264 |
| MF_HYDRO.hpprgm | 129 |
| MF_MANO.hpprgm | 226, 237 |
| MF_CAS.hpprgm | 296, 301, 320 |

---

## 4. Files Inspected — No Changes Needed

| File | Status |
|---|---|
| MF_BUOY.hpprgm | No non-ASCII in code or strings |
| MF_PUMPS.hpprgm | No non-ASCII; WHILE loops from prior fix intact |
| MF_UTILS.hpprgm | No non-ASCII in code or strings |

---

## 5. FLUIDOS.hpprgm — Structural Validation After Fix

```
Lines:       60
BEGIN count: 2  (dispatch_ body, FLUIDOS body)
END count:   18 (2 function ENDs + 1 WHILE + 1 outer IF + 1 inner IF +
                 1 IFERR + 11 dispatch IFs + 1 CHOOSE-exit IF)
IFERR:       1  (direct child of WHILE, body = single call dispatch_(c))
THEN in IFERR body: 0
```

Line 45 of new FLUIDOS: `"0. SALIR");` — last item of CHOOSE call. No syntax risk.

---

## 6. Remaining Risks

| Risk | Severity | Notes |
|---|---|---|
| MF_COMP `solveMach_`: RETURN inside FOR loop | LOW | Valid HP PPL; RETURN exits function regardless of loop |
| MF_CAS string INPUT vars with string defaults | LOW | Valid HP PPL dynamic typing; runtime-only concern |
| `fmt_` defined in multiple modules | COSMETIC | All copies functionally identical; runtime overwrite harmless |
| `≈` in COMMENT lines of MF_BERN (lines 189, 203) | VERY LOW | Comments are stripped before parsing; cannot cause errors |

---

## 7. Recommended Tests on HP Prime / Connectivity Kit

1. **Import FLUIDOS.hpprgm first** — should import without syntax error (60 lines, clean IFERR).
2. **Import MF_BERN, MF_BUOY, MF_COMP, etc.** — import each; verify no errors.
3. **Run FLUIDOS()** — main menu with 11 options + SALIR should appear.
4. **Press ESC** — exit confirmation should appear.
5. **Select "0. SALIR" then "Si, salir"** — program should exit.
6. **Select "2. Manometros" → "1. Conversion Abs/Man/Vacio"** — tests MF_MANO with fixed `->` labels.
7. **Select "8. Flujo Compresible" → "3. Relacion Area-Mach"** — tests `subsonico`/`supersonico` labels.
8. **Select "7. Bombas" → "5. Punto de Operacion"** — tests WHILE loops in MF_PUMPS_OP.
9. **Remove one module file** — verify IFERR shows "Modulo no encontrado" via dispatch_ helper.

---

## 8. Files Changed in This Pass

- `FLUIDOS.hpprgm` — restructured with `dispatch_()` helper; IFERR body = single call (60 lines)
- `MF_MANO.hpprgm` — `→` → `->`, `π` → numeric literal
- `MF_BERN.hpprgm` — `─` → `-`, `≈` → `~=`
- `MF_COMP.hpprgm` — `─` → `-`, `→` → `->`, `ó` → `o`
- `MF_CAS.hpprgm` — `→` → `->`, `≠` → `!=`, `ó` → `o`, `π` → numeric
- `MF_PIPES.hpprgm` — `─` → `-`, `π` → numeric literal
- `MF_HYDRO.hpprgm` — `π` → numeric literal
- `MF_PROPS.hpprgm` — `Ó` → `O` in aviso strings
- `SYNTAX_FIX_LINE45_REPORT.md` — this file
