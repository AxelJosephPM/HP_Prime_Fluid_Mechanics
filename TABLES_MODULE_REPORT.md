# TABLES MODULE REPORT
## FLUIDOS PRIME v2.0 — Fluid Properties and Tables Module

---

## Files Changed

| File | Change |
|------|--------|
| `MF_TABLES.hpprgm` | New module: 8-option menu |
| `FLUIDOS.hpprgm` | Added "Tablas" to FLUIDOS_P2_() sub-menu |
| `README.md` | Added module 12 description, renumbered 13-15 |
| `TEST_PLAN.md` | Added TEST 34-38 |
| `STRUCTURE.md` | Added MF_TABLES architecture entry |

---

## New Module: MF_TABLES

| # | Function | Description |
|---|----------|-------------|
| 1 | MF_TABLES_AGUA | Water properties by T: rho, mu, nu (0-100 C) |
| 2 | MF_TABLES_AIRE | Air properties by T: rho, mu, nu (0-40 C, 1 atm) |
| 3 | MF_TABLES_PV | Water vapor pressure by T: Pv in Pa and kPa |
| 4 | MF_TABLES_EPS | Pipe roughness: absolute eps in m and mm |
| 5 | MF_TABLES_K | Minor loss coefficients K |
| 6 | MF_TABLES_CST | Useful constants: g, R_air, k_air, rho_w, mu_w, Patm |
| 7 | MF_TABLES_HELP | Help: how to use the tables |
| 8 | Volver | Return to main menu |

---

## Data Stored

### Water (option 1)
| T (C) | rho (kg/m3) | mu (Pa*s) | nu (m2/s) |
|-------|-------------|-----------|-----------|
| 0     | 999.8       | 1.79E-3   | 1.79E-6   |
| 10    | 999.7       | 1.31E-3   | 1.31E-6   |
| 20    | 998.2       | 1.00E-3   | 1.00E-6   |
| 30    | 995.7       | 7.98E-4   | 8.01E-7   |
| 40    | 992.2       | 6.53E-4   | 6.58E-7   |
| 60    | 983.2       | 4.67E-4   | 4.75E-7   |
| 80    | 971.8       | 3.55E-4   | 3.65E-7   |
| 100   | 958.4       | 2.82E-4   | 2.94E-7   |

### Air at 1 atm (option 2)
| T (C) | rho (kg/m3) | mu (Pa*s) | nu (m2/s) |
|-------|-------------|-----------|-----------|
| 0     | 1.293       | 1.72E-5   | 1.33E-5   |
| 10    | 1.247       | 1.76E-5   | 1.41E-5   |
| 15    | 1.225       | 1.79E-5   | 1.46E-5   |
| 20    | 1.204       | 1.81E-5   | 1.50E-5   |
| 25    | 1.184       | 1.84E-5   | 1.55E-5   |
| 30    | 1.165       | 1.86E-5   | 1.60E-5   |
| 40    | 1.127       | 1.91E-5   | 1.69E-5   |

### Vapor pressure (option 3)
0C: 611 Pa | 10C: 1228 Pa | 20C: 2339 Pa | 30C: 4246 Pa
40C: 7384 Pa | 60C: 19946 Pa | 80C: 47414 Pa | 100C: 101325 Pa

### Roughness (option 4)
Glass/plastic: 1.5E-6 m | Copper/brass: 1.5E-6 m | Commercial steel: 4.5E-5 m
Galvanized iron: 1.5E-4 m | Cast iron: 2.6E-4 m | Concrete: 3.0E-4 m | Riveted steel: 3.0E-3 m

### Minor loss K (option 5)
Sharp entrance: 0.5 | Rounded entrance: 0.04 | Tank exit: 1.0
Smooth 90 elbow: 0.3 | Sharp 90 elbow: 0.9 | Gate valve open: 0.2
Globe valve open: 10 | Check valve: 2

---

## FLUIDOS.hpprgm Change

FLUIDOS_P2_() updated:
- Added "Tablas" between "Diferencial" and "CAS"
- Exit condition updated from op2==7 to op2==8
- op2==5: MF_TABLES()
- op2==6: MF_CAS() (was 5)
- op2==7: MF_UTILS() (was 6)

---

## Tests Added (TEST_PLAN.md)

| Test | Description | Key expected value |
|------|-------------|-------------------|
| 34 | Water at 20 C | rho=998.2, mu=1.00E-3, nu=1.00E-6 |
| 35 | Air at 15 C | rho=1.225, mu=1.79E-5, nu=1.46E-5 |
| 36 | Vapor pressure at 20 C | Pv=2339 Pa, 2.339 kPa |
| 37 | Commercial steel roughness | eps=4.5E-5 m, 0.045 mm |
| 38 | Sharp entrance K | K=0.5 |

---

## Known Limitations

- Discrete table only: no interpolation between temperatures.
  For exam problems, choose the nearest tabulated temperature or use
  values given in the problem statement.
- Air properties assume ideal gas at 1 atm. Not valid at high pressure.
- Roughness values are typical estimates. Actual values depend on pipe age,
  corrosion, and manufacturer. Always use problem statement data when given.
- K values are representative. Exact values depend on geometry and Re.

---

## Confirmation

- MF_CAS.hpprgm was NOT touched.
- No push to GitHub was performed.
- No Unicode, Greek letters, or LaTeX syntax in code strings.
- All EXPORT functions verified: grep -n '^EXPORT' *.hpprgm -- OK (17 exports total).
- No backtick blocks in .hpprgm files -- OK.
- No disallowed Unicode in .hpprgm files -- OK.
