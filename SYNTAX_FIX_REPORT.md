# SYNTAX FIX REPORT — FLUIDOS PRIME v1.0
Date: 2026-05-22

---

## 1. Files Inspected

| File | Lines | Status |
|---|---|---|
| FLUIDOS.hpprgm | 95 | FIXED |
| MF_BERN.hpprgm | 247 | OK |
| MF_BUOY.hpprgm | 148 | OK |
| MF_CAS.hpprgm | 433 | OK |
| MF_COMP.hpprgm | 276 | OK |
| MF_HYDRO.hpprgm | 210 | OK |
| MF_MANO.hpprgm | 292 | OK |
| MF_PIPES.hpprgm | 338 | OK |
| MF_PROPS.hpprgm | 289 | OK |
| MF_PUMPS.hpprgm | 313 | FIXED |
| MF_UTILS.hpprgm | 415 | OK |

---

## 2. Syntax Errors Found and Fixed

### ERROR 1 — FLUIDOS.hpprgm: Non-canonical IFERR syntax (HIGH RISK)

**Problem:** All 11 IFERR blocks used the compact inline form:
```
IFERR MF_PROPS();
THEN MSGBOX("...");
END;
```
The HP Connectivity Kit (especially older versions) strictly requires the body of IFERR on its own line, separated from the IFERR keyword. Placing a statement directly on the IFERR line followed by a semicolon can cause the parser to see `THEN` as an unexpected orphan keyword.

**Fix applied:** All 11 IFERR blocks were reformatted to the canonical HP PPL multi-line form:
```
IFERR
  MF_PROPS();
THEN
  MSGBOX("...");
END;
```

**Files changed:** FLUIDOS.hpprgm

---

### ERROR 2 — MF_PUMPS.hpprgm: BREAK inside FOR loops (HIGH RISK)

**Problem:** Two `BREAK` statements were used inside `FOR` loops in `MF_PUMPS_OP()`:
- Line ~251: `BREAK;` inside the bracket-search loop
- Line ~271: `IF ABS(Qhi-Qlo) < 1E-8 THEN BREAK; END;` inside the bisection loop

`BREAK` is not universally supported across HP Connectivity Kit versions and may produce a "syntax error" or "unknown identifier" error on some HP Prime G2 units depending on firmware/kit version.

**Fix applied:** Both `FOR` loops replaced with `WHILE` loops using flag/counter variables:

Loop 1 (bracket search):
```
i := 1;
WHILE i <= 20 AND ok_range == 0 DO
  ...
  IF Flo < 0 THEN ok_range := 1; ELSE Qhi := Qhi * 2; END;
  i := i + 1;
END;
```

Loop 2 (bisection convergence):
```
i := 1;
WHILE i <= 60 DO
  ...
  IF ABS(Qhi - Qlo) < 1E-8 THEN i := 61; ELSE i := i + 1; END;
END;
```

**Files changed:** MF_PUMPS.hpprgm

---

## 3. Potential Risks Investigated — No Fix Required

### fmt_ function defined in multiple files

Every module defines a local `fmt_` helper with identical logic (except MF_UTILS uses 6 decimal places vs 4 elsewhere). This is a naming risk at runtime if HP Prime puts non-exported functions in a shared namespace. However:
- It does NOT cause a syntax error during Connectivity Kit import
- Each module only ever calls its own `fmt_`
- The worst runtime outcome is MF_UTILS's 6-decimal version replacing another module's 4-decimal version — a minor precision difference with no functional impact

**Decision:** No change. Fixing this would require touching 40+ call sites across 10 files.

### Unicode characters in string literals

Box-drawing chars (─), arrows (→), and accented Spanish letters (ó, á, é, etc.) appear in MSGBOX/CHOOSE strings. These are inside string literals, not in code identifiers. HP Prime G2 uses UTF-8 encoding and the Connectivity Kit supports Unicode in strings. This is not a syntax error.

### π constant in PPL code

Used directly as a mathematical constant in MF_CAS, MF_HYDRO, MF_MANO, MF_PIPES (`π / 2 * R ^ 2`, etc.). HP Prime PPL recognizes `π` as a built-in constant (same as in CAS). Valid — no fix needed.

### IFERR in MF_CAS.hpprgm

MF_CAS already uses the canonical multi-line IFERR form throughout. No fix needed.

### CHOOSE index values

All CHOOSE return values (c == N for "Volver" or ESC) were verified against actual item counts. All indices are correct.

### BEGIN/END balance

Verified manually for all 11 files. All BEGIN/END, IF/THEN/END, WHILE/DO/END, FOR/DO/END, and IFERR/THEN/END pairs are balanced.

### ASCII identifiers

All variable and function names use ASCII-safe characters. No subscript Unicode identifiers found in code. The `π` constant is a valid HP Prime built-in, not a user-defined identifier.

### Calls to non-existent functions

FLUIDOS.hpprgm calls: MF_PROPS, MF_MANO, MF_HYDRO, MF_BUOY, MF_BERN, MF_PIPES, MF_PUMPS, MF_COMP, MF_CAS, MF_UNITS, MF_HELP — all are defined and exported in their respective modules. All wrapped in IFERR for graceful missing-module handling.

---

## 4. CAS Module Status

MF_CAS.hpprgm is **NOT disabled**. It was reviewed and found syntactically valid:
- All `CAS("...")` calls use correct string-argument form
- All IFERR blocks already in canonical multi-line form
- `solve()`, `simplify()`, `diff()`, `int()` are standard HP Prime CAS functions
- No invalid CAS syntax detected

The CAS module will work if the HP Prime CAS engine is available. All CAS calls are wrapped in IFERR, so CAS failures show a user-friendly error message rather than crashing.

---

## 5. Navigation Safety

- Main menu: ESC → c=0 triggers exit confirmation dialog. Safe.
- All sub-module menus: ESC → c=0, "Volver" → c=N both trigger RETURN. No infinite trap.
- Sub-menus without matching c value fall through and loop back to CHOOSE. Safe.

---

## 6. Remaining Risks

| Risk | Severity | Notes |
|---|---|---|
| `fmt_` naming collision at runtime | LOW | Same behavior, only precision differs |
| CHOOSE returns different value on ESC in edge firmware | LOW | Standard behavior, unlikely |
| CAS calls may fail on older HP Prime firmware | LOW | All wrapped in IFERR |
| Unicode box-drawing chars (─) in strings on some encodings | VERY LOW | Cosmetic only |

---

## 7. Recommended First Tests on HP Prime / Connectivity Kit

1. **Import all 11 .hpprgm files** via Connectivity Kit — verify no syntax errors on import.
2. **Run FLUIDOS()** — verify main menu appears with 11 options + SALIR.
3. **Press ESC** from main menu — verify exit confirmation appears.
4. **Select "0. SALIR" → "Si, salir"** — verify clean exit.
5. **Open "1. Propiedades y Num. Adim."** → test Reynolds with rho/mu input.
6. **Open "7. Bombas y Cavitacion"** → test "5. Punto de Operacion" (this is the fixed BREAK function).
7. **Open "9. Herramientas CAS"** → test "1. Resolver Ecuacion" to confirm CAS bridge works.
8. If a module is not installed, verify the IFERR fallback message appears cleanly.

---

## 8. Files Changed

- `FLUIDOS.hpprgm` — IFERR syntax canonicalized (11 blocks)
- `MF_PUMPS.hpprgm` — BREAK replaced with WHILE loops (2 loops)
- `SYNTAX_FIX_REPORT.md` — this file (new)
