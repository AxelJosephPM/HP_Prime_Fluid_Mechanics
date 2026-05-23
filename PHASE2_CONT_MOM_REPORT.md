# PHASE2_CONT_MOM_REPORT.md
## FLUIDOS PRIME v1.0 — Phase 2: Continuity and Momentum Modules

---

### Summary

Phase 2 adds two new high-impact fluid mechanics modules to FLUIDOS PRIME:

| Module | File | Functions |
|--------|------|-----------|
| Continuidad | `MF_CONT.hpprgm` | 9 sub-functions + menu |
| Cantidad de Movimiento | `MF_MOM.hpprgm` | 7 sub-functions + menu |

The main menu (`FLUIDOS.hpprgm`) was expanded from 11 to 13 options.
Documentation updated: `README.md`, `TEST_PLAN.md`, `STRUCTURE.md`.

---

### Files Created

#### MF_CONT.hpprgm — Continuity / Mass Conservation

Non-exported helper:
- `fmt_(x)` — number formatter (same pattern as all other modules)

Sub-functions (not exported):
- `MF_CONT_Q()` — Q = A*V with unit conversions
- `MF_CONT_MDOT()` — mdot = rho*Q
- `MF_CONT_C11()` — 1-in, 1-out: solve for any of V1, V2, A1, A2
- `MF_CONT_C21()` — 2-in, 1-out: Q1 + Q2 = Q3
- `MF_CONT_C12()` — 1-in, 2-out: Q1 = Q2 + Q3
- `MF_CONT_DHT()` — dh/dt = (Qin - Qout)/As with level state message
- `MF_CONT_TFILL()` — t = As * dh / |Qin - Qout|
- `MF_CONT_CONV()` — caudal unit converter (m3/s, L/s, m3/h, L/h, GPM, ft3/s)
- `MF_CONT_HELP()` — inline help text

Exported:
- `MF_CONT()` — 9-option WHILE 1 DO menu, exit on c==0 OR c==10

#### MF_MOM.hpprgm — Linear Momentum

Non-exported helpers:
- `fmt_(x)` — number formatter
- `angulo_(Fx, Fy)` — angle in degrees from components, handles Fx==0 (ATAN2 workaround)

Sub-functions (not exported):
- `MF_MOM_NOZ()` — axial force in nozzle/diffuser: Rx = P1*A1 - P2*A2 + mdot*(V1-V2)
- `MF_MOM_BEND()` — 2D elbow reaction: Rx, Ry, |R|, angle
- `MF_MOM_PLATE()` — jet on fixed plate: F = mdot*V
- `MF_MOM_DEF()` — deflected jet at angle theta: Fx, Fy, |F|
- `MF_MOM_THRUST()` — motor/rocket thrust: T = mdot*(Ve-Vi) + (Pe-Pi)*Ae, Isp
- `MF_MOM_2D()` — resultant 2D force from Fx, Fy input: magnitude, angle, anchor force
- `MF_MOM_HELP()` — inline help with momentum equation conventions

Exported:
- `MF_MOM()` — 7-option WHILE 1 DO menu, exit on c==0 OR c==8

---

### Files Modified

#### FLUIDOS.hpprgm

Previous: 11 options (Propiedades through Salir = op 1–11)
New: 13 options with Continuidad (op 6) and Cant. Movimiento (op 7) inserted.

Shifted options:
- op 6: Continuidad → MF_CONT()
- op 7: Cant. Movimiento → MF_MOM()
- op 8: Tuberias → MF_PIPES()
- op 9: Bombas → MF_PUMPS()
- op 10: Compresible → MF_COMP()
- op 11: CAS → MF_CAS()
- op 12: Utilidades → MF_UTILS()
- op 13: Salir (exit condition: op == 0 OR op == 13)

#### README.md
- Added MF_CONT and MF_MOM to file table
- Added module descriptions as sections 6 and 7
- Renumbered existing sections 6–11 to 8–13
- Removed "Continuidad" and "Cantidad de movimiento" from pending list

#### STRUCTURE.md
- Added MF_CONT.hpprgm and MF_MOM.hpprgm to file tree
- Added full function hierarchy for both modules in architecture diagram

#### TEST_PLAN.md
- Added TEST 12: Continuidad 1-1, calcular V2
- Added TEST 13: Caudal volumetrico + conversor
- Added TEST 14: Tasa de cambio de nivel en deposito
- Added TEST 15: Fuerza axial en tobera
- Added TEST 16: Chorro contra placa fija
- Added TEST 17: Reaccion en codo 90 grados

---

### HP PPL Constraints Applied

All Phase 2 code follows the rules confirmed in Phase 1:

1. **LOCAL limit**: All LOCAL declarations have ≤ 8 variables per statement.
   MF_MOM_BEND has the most locals; split across two LOCAL lines (A1..P2 and V1..Fanc).

2. **No `Re` identifier**: Not used anywhere. `reyn` pattern not needed in these modules.

3. **WHILE 1 DO loops**: Both MF_CONT() and MF_MOM() use WHILE 1 DO...END.
   No REPEAT...UNTIL used anywhere.

4. **ATAN2 workaround**: `angulo_(Fx, Fy)` handles Fx==0 case explicitly before calling ATAN.

5. **Trig conversion**: All SIN/COS calls receive radians (deg * 3.14159265358979 / 180).
   All ATAN results multiplied by 180 / 3.14159265358979.

6. **ASCII safety**: No accented characters, Greek letters, or Unicode in any identifiers
   or string literals. Spanish text in MSGBOX uses ASCII-safe words only.

7. **CHOOSE limit concern**: FLUIDOS.hpprgm now has 13 options + title = 15 CHOOSE
   arguments. HP Prime G2 has been tested up to 11 options; 13 is unconfirmed.
   If the calculator rejects the 13-option CHOOSE, split the menu into two pages:
   e.g., a first CHOOSE with "Continuar..." as last option leads to a second CHOOSE.

---

### CAS Status

MF_CAS.hpprgm was NOT modified. It remains a placeholder:
```
EXPORT MF_CAS()
BEGIN
  MSGBOX("Herramientas CAS pendientes de validacion en HP Prime.");
END;
```

---

### Engineering Assumptions

1. **Incompressible flow**: All continuity and momentum calculations assume constant
   density. For compressible flow, use MF_COMP.

2. **Steady state**: Momentum equation applied to steady control volumes only.

3. **1D (uniform) velocity**: Momentum flux is ṁ*V (no kinetic energy correction factor).
   Beta (momentum correction factor) = 1 assumed throughout.

4. **Gauge pressures in momentum**: INPUT prompts specify "presion manometrica".
   Using absolute pressures gives incorrect results — user is warned in labels.

5. **Deflected jet formula**: Uses flat-plate model (fluid exits tangentially).
   Not the Pelton bucket or curved vane model.

6. **Trig mode independence**: All trig operations use explicit radian conversion
   (multiply degrees by pi/180), so the calculator's angle mode setting is irrelevant.

7. **ATAN domain**: angulo_() returns values in [-180, 180] degrees using the standard
   four-quadrant reconstruction. It matches the behavior of atan2(y, x) in most languages.

---

### What to Test First

1. Import all 13 .hpprgm files in HP Connectivity Kit. Confirm no syntax errors.
2. Run `FLUIDOS()`. Verify the main menu shows 13 options including "Continuidad" (6)
   and "Cant. Movimiento" (7), and that "Salir" (13) exits cleanly.
3. **MF_CONT test**: Option 6 → sub-option 3 (Continuidad 1-1) → Calcular V2.
   Input A1=0.02, V1=1.5, A2=0.005. Expected V2 = 6.0 m/s. (TEST 12)
4. **MF_MOM test**: Option 7 → sub-option 3 (Chorro contra placa).
   Input rho=1000, V=8.0, A=0.01. Expected F = 640 N. (TEST 16)
5. **Codo 2D test**: Option 7 → sub-option 2.
   Use TEST 17 values. Expected Rx ≈ -340 N, Ry ≈ 190 N.
6. **Level change test**: Option 6 → sub-option 6.
   Qin=0.05, Qout=0.02, As=10. Expected dh/dt = 0.003 m/s, LLENANDO. (TEST 14)
7. Confirm ESC and Volver navigate correctly from both new modules.
8. Confirm MF_CAS (option 11) still shows placeholder message.

---

### Risk Notes

- **13-option CHOOSE**: Unconfirmed on hardware. If it fails, the fix is to split
  FLUIDOS.hpprgm into two CHOOSE menus. No changes to module files are needed.
- **MF_MOM_BEND INPUT with 8 fields**: INPUT with 8 fields has not been tested.
  If the input screen does not render correctly, split into two sequential INPUT calls.
- **Pressure sign convention**: MF_MOM_BEND uses a sign convention where Rx/Ry
  represent the force the fluid exerts ON the elbow. The anchor force is the negative.
  This is documented in the HELP screen and in code comments.

---

### Commit

Phase 2 implementation complete. Ready for:
```
git commit -m "Add continuity and momentum modules to FLUIDOS PRIME"
```
Files to stage:
- MF_CONT.hpprgm (new)
- MF_MOM.hpprgm (new)
- FLUIDOS.hpprgm (modified)
- README.md (modified)
- STRUCTURE.md (modified)
- TEST_PLAN.md (modified)
- PHASE2_CONT_MOM_REPORT.md (new)
