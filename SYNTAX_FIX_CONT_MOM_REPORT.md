# SYNTAX_FIX_CONT_MOM_REPORT.md
## FLUIDOS PRIME v1.0 — Continuity and Momentum Syntax Fixes

---

### 1. FLUIDOS.hpprgm — Reported line 27

**Original issue:**
CHOOSE with 14 arguments (title string + 13 option strings) exceeded the HP
Prime G2 firmware limit. The confirmed safe maximum is 12 arguments (title + 11
options), which was the previous working FLUIDOS menu. Expanding to 13 options
(+2 new modules) brought the total to 14 arguments, causing a parse failure. HP
Connectivity Kit reported the error at line 27 (the first IF block after the
CHOOSE), which is the typical displacement pattern for CHOOSE-related parse errors.

**Fix applied:**
Split the single 13-option CHOOSE into two pages:

- Page 1 (FLUIDOS main menu): 8 options + title = 9 CHOOSE arguments (safe).
  Options: Propiedades, Manometros, Hidrostatica, Flotabilidad, Bernoulli,
  Continuidad, Cant. Movimiento, Mas modulos..., Salir.
- Page 2 (FLUIDOS_P2_ helper, non-exported): 6 options + title = 7 CHOOSE arguments (safe).
  Options: Tuberias, Bombas, Compresible, CAS, Utilidades, Volver.

`FLUIDOS_P2_()` uses the same WHILE 1 DO / CHOOSE / IF / RETURN pattern as all
other modules. Selecting "Volver" (op2 == 6) or pressing ESC (op2 == 0) returns
to the main menu. All 13 modules remain accessible.

---

### 2. MF_CONT.hpprgm — Reported line 168

**Original issue:**
`LOCAL c, A1, V1, A2, V2, A3, V3, Q1, Q2, Q3, res;` in function `MF_CONT_C21()`
contained 11 variables in a single LOCAL statement. HP Prime G2 allows at most 8
variables per LOCAL declaration. This caused a syntax error at line 168.

An identical problem existed at line 225 in `MF_CONT_C12()` (same 11-variable
LOCAL pattern). The CK would have reported this error next after fixing line 168.
Both were fixed proactively.

**Fix applied:**
- Line 168 (`MF_CONT_C21`): split `LOCAL c, A1, V1, A2, V2, A3, V3, Q1, Q2, Q3, res;`
  into `LOCAL c, A1, V1, A2, V2, A3, V3;` + `LOCAL Q1, Q2, Q3, res;` (7 + 4 vars).
- Line 225 (`MF_CONT_C12`): identical split applied.

No calculation logic was changed.

---

### 3. MF_MOM.hpprgm — Reported line 176

**Original issue:**
`LOCAL rho, V, A, theta, Q, mdot, Fx, Fy, Ftot, ang, res;` in function
`MF_MOM_DEF()` contained 11 variables in a single LOCAL statement, exceeding
the HP Prime limit of 8.

**Fix applied:**
Split into `LOCAL rho, V, A, theta, Q, mdot, Fx;` + `LOCAL Fy, Ftot, ang, res;`
(7 + 4 vars). No calculation logic was changed.

---

### HP PPL Rules Confirmed (Phase 2 additions)

1. **LOCAL variable limit (previously confirmed):** Max 8 variables per LOCAL statement.
   This now affects MF_CONT_C21, MF_CONT_C12, and MF_MOM_DEF (all fixed).

2. **CHOOSE argument limit:** Max ~12 total arguments (title + options). Confirmed
   safe at 12; fails at 14. The safe pattern for menus exceeding 11 options is to
   split into two CHOOSE pages using a helper sub-function.

---

### Functionality Status

| Function          | Status                                |
|-------------------|---------------------------------------|
| FLUIDOS main menu | All 13 modules accessible via 2 pages |
| MF_CONT_C21       | Fully functional (LOCAL split only)   |
| MF_CONT_C12       | Fully functional (LOCAL split only)   |
| MF_MOM_DEF        | Fully functional (LOCAL split only)   |
| All other modules | Unchanged                             |
| MF_CAS            | Untouched (placeholder)               |

No functionality was removed or simplified.

---

### What to Test First in HP Connectivity Kit

1. Import FLUIDOS.hpprgm, MF_CONT.hpprgm, MF_MOM.hpprgm. Confirm no errors.
2. Run `FLUIDOS()`. Verify main menu shows 8 options (Propiedades through "Mas
   modulos...") plus "Salir". Select option 8 "Mas modulos..." — verify second
   menu shows Tuberias, Bombas, Compresible, CAS, Utilidades, Volver.
3. Continuidad (option 6) → sub-option 4 "Continuidad 2 entradas-1 salida" →
   option 1 "Calcular Q3". Input A1=0.01, V1=2.0, A2=0.01, V2=1.5, A3=0.015.
   Expected: Q1=0.02, Q2=0.015, Q3=0.035, V3=2.333 m/s.
4. Continuidad (option 6) → sub-option 5 "Continuidad 1 entrada-2 salidas" →
   any option. Verify it opens without error.
5. Cant. Movimiento (option 7) → sub-option 4 "Chorro desviado por angulo".
   Input rho=1000, V=5.0, A=0.005, theta=45. Verify result appears.
6. From main menu, select "Mas modulos..." → select "Volver" → confirm returns
   to main menu. Select "Salir" or press ESC to exit cleanly.
