# SYNTAX_FIX_SPECIFIC_LINES_REPORT.md
## FLUIDOS PRIME v1.0 - HP Prime G2 Specific Line Error Fixes

### Summary of Reported Errors and Fixes

---

### 1. FLUIDOS.hpprgm — Reported line 24

**Root cause:**
`REPEAT...UNTIL op == 11 OR op == 0` — HP Prime G2 has issues with compound
conditions in `UNTIL`. The parser encounters trouble evaluating `op == 11 OR op == 0`
as the UNTIL predicate, and reports the error somewhere in the middle of the
preceding REPEAT body (at stripped "program line 24").

**Fix applied:**
Replaced `REPEAT...UNTIL` with `WHILE 1 DO...END`. Added explicit exit check
`IF op == 0 OR op == 11 THEN RETURN; END;` as the first statement inside the
loop after CHOOSE. This is the same safe pattern used in all other modules
(MF_COMP, MF_UTILS, etc.).

**Functionality preserved:** All 11 menu options and module calls intact.

---

### 2. MF_PROPS.hpprgm — Reported line 27

**Root cause:**
`regimenRe(Re)` — The parameter name `Re` conflicts with HP Prime's built-in
function `RE(z)` (real part of complex number). HP Prime's tokenizer is
case-insensitive for built-in function names, so `Re` is parsed as the built-in
`RE`, causing a syntax error at the function definition line.

**Fix applied:**
- Renamed parameter `Re` → `reyn` in function `regimenRe`.
- Renamed LOCAL variable `Re` → `reyn` in `MF_RE()`.
- Updated all code usages of the variable throughout both functions.
- String literals (display text like `"Re = "`, `"LAMINAR (Re < 2300)"`) preserved unchanged.

**Functionality preserved:** Reynolds number calculation, regime classification unchanged.

---

### 3. MF_HYDRO.hpprgm — Reported line 67

**Root cause:**
`LOCAL rho, g, h1, h2, b, hc, A, F, IG, ycp, ecp, res;` — 12 variables in one
LOCAL statement. HP Prime PPL allows at most 8 variables per LOCAL declaration.
A second 15-variable LOCAL was also present in `MF_HYDRO_FI()` (line 114), not
yet reported because the parser stopped at line 67 first.

**Fix applied:**
- Line 67: Split 12-var LOCAL into two statements of 6 vars each.
- Line 114: Split 15-var LOCAL into three statements of 6, 6, and 3 vars.

**Functionality preserved:** All hydrostatic force calculations intact.

---

### 4. MF_BUOY.hpprgm — Reported line 53

**Root cause:**
`LOCAL rho_f, rho_b, g, Vb, W, E, Wap, estado, res;` — 9 variables in one LOCAL
statement, exceeding the HP Prime limit of 8.

**Fix applied:**
Split 9-var LOCAL into two statements: 5 vars + 4 vars.

**Functionality preserved:** All buoyancy calculations intact.

---

### 5. MF_BERN.hpprgm — Reported line 32

**Root cause:**
`LOCAL P1, V1, z1, V2, z2, hL, hbomba, hturb, rho, g, a1, a2;` — 12 variables
in one LOCAL statement. Three additional oversized LOCAL declarations existed in
`MF_BERN_HL()` (12 vars), `MF_BERN_HB()` (12 vars), and `MF_BERN_V2()` (10 vars),
not yet reported.

**Fix applied:**
All four oversized LOCALs split into pairs of ≤6 vars each:
- `MF_BERN_P2` line 32: 12 → 6+6
- `MF_BERN_HL` line 83: 12 → 6+6
- `MF_BERN_HB` line 112: 12 → 6+6
- `MF_BERN_V2` line 192: 10 → 6+4

**Functionality preserved:** All Bernoulli equation variants intact.

---

### 6. MF_PIPES.hpprgm — Reported line 26

**Root cause:**
`regimenStr(Re)` — Same as MF_PROPS issue: parameter name `Re` conflicts with the
built-in `RE(z)` function. The function `calcFF(Re, eps, D)` at line 36 had the same
problem. Multiple LOCAL variables named `Re` also appeared in `MF_PIPES_RE()`,
`MF_PIPES_FF()`, and `MF_PIPES_TOT()`.
Additionally, `MF_PIPES_TOT` had an 11-variable LOCAL declaration (line 246),
unreported because the parser stopped at line 26.

**Fix applied:**
- Renamed parameter and body `Re` → `reyn` in `regimenStr`, `calcFF`.
- Renamed LOCAL `Re` → `reyn` in `MF_PIPES_RE`, `MF_PIPES_FF`, `MF_PIPES_TOT`.
- Updated all code usages (assignments, conditions, formula expressions).
- String literals preserved: `"Re (-)="`, `"Re = "`, `"LAMINAR (Re < 2300)"`, etc.
- Split `LOCAL Re, A, Q, f, hf, hm, hL, DP, nu, reg, argF;` (11 vars) into 5+5 vars.

**Functionality preserved:** All pipe flow calculations intact.

---

### 7. MF_PUMPS.hpprgm — Reported line 61

**Root cause:**
`LOCAL Ph, Peje, eta_hid, rho, g, Q, H, res, res2;` — 9 variables in one LOCAL
statement, exceeding the HP Prime limit of 8.

**Fix applied:**
Split 9-var LOCAL into two statements: 5 vars + 4 vars (merged with existing
`LOCAL c;` line to keep structure clean).

**Functionality preserved:** Pump efficiency calculations intact.

---

### HP PPL Rules Confirmed

1. **LOCAL variable limit:** Maximum 8 variables per LOCAL declaration.
   Use multiple LOCAL statements for functions with more variables.

2. **Reserved identifier conflict:** `Re` (any case) is treated as the built-in
   `RE(z)` complex-part function. Do not use `Re`, `re`, `RE` as variable or
   parameter names. Use `reyn` instead.

3. **Loop structure:** `REPEAT...UNTIL condition` with complex boolean conditions
   (using OR) may be unreliable. Prefer `WHILE 1 DO...END` with an explicit
   `IF condition THEN RETURN; END;` guard inside the loop.

4. **ASCII safety:** All files confirmed 0 non-ASCII bytes after fixes.

---

### Remaining Risks

- `MF_MANO.hpprgm` and `MF_COMP.hpprgm` were not in the reported error list and
  were not modified. If errors appear in those files, apply the same LOCAL-split
  rule (max 8 vars per statement).
- The INPUT function with 10 fields (used in several Bernoulli functions) has not
  been tested on the calculator. If input screens do not render correctly, split
  into two sequential INPUT calls.
- MF_CAS remains a placeholder (CAS safety rule).

---

### What to Test First in HP Connectivity Kit

1. Import all 11 .hpprgm files. Confirm: no "PROGRAM LINE N" error for any file.
2. Send all programs to calculator.
3. Run `FLUIDOS()` — verify main menu appears with all 11 options.
4. Test each module individually (options 1-10).
5. For Bernoulli (option 5): test `MF_BERN_P2` (uses 10-field INPUT), verify
   the input screen renders.
6. For Pipes (option 6): test `MF_PIPES_FF` (uses `reyn` variable, formerly `Re`).
7. Option 9 (CAS) shows placeholder message — expected behavior.
8. Option 11 (Salir) or Cancel should exit the main menu cleanly.
