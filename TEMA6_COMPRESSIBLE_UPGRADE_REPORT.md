# TEMA6 COMPRESSIBLE UPGRADE REPORT
## FLUIDOS PRIME v2.0 — Compressible Flow Module

---

## Files Changed

| File | Change |
|------|--------|
| `MF_COMP.hpprgm` | Rewritten with 11-option menu |
| `README.md` | Updated module 10 description |
| `TEST_PLAN.md` | Added TEST 24-28 |
| `STRUCTURE.md` | Updated MF_COMP architecture entry |

---

## New Options in MF_COMP

| # | Function | Description |
|---|----------|-------------|
| 1 | MF_COMP_SONIDO | a = sqrt(gamma*R*T) |
| 2 | MF_COMP_MACH | M = V/a, regime classification |
| 3 | MF_COMP_IS | Isentropic ratios: T0/T, P0/P, rho0/rho |
| 4 | MF_COMP_REMAN | Stagnation properties: T0, P0, rho0 |
| 5 | MF_COMP_ASTAR | A/A* given M |
| 6 | MF_COMP_MINV | M from A/A* via bisection (subsonic or supersonic branch) |
| 7 | MF_COMP_MDOT | Isentropic mass flow through nozzle |
| 8 | MF_COMP_CHOKE | Choked mass flow mdot* and critical pressure ratio P*/P0 |
| 9 | MF_COMP_SHOCK | Normal shock: M2, P2/P1, rho2/rho1, T2/T1, P02/P01 |
| 10 | MF_COMP_HELP | Compact formula reference |
| 11 | Volver | Return to main menu |

Internal helpers retained and updated:
- `aAstar_(M, k)` — A/A* as function of M and gamma
- `solveMach_(tgt, lo, hi, k)` — bisection, updated to 50 iterations

---

## Key Formulas Implemented

```
a     = sqrt(gamma*R*T)
M     = V/a
T0/T  = 1 + (gamma-1)/2 * M^2
P0/P  = (T0/T)^(gamma/(gamma-1))
r0/r  = (T0/T)^(1/(gamma-1))
A/A*  = (1/M)*[(2/(gamma+1))*(1+(gamma-1)/2*M^2)]^((gamma+1)/(2*(gamma-1)))
mdot  = A*P0/sqrt(T0)*sqrt(gamma/R)*M*(1+(gamma-1)/2*M^2)^(-(gamma+1)/(2*(gamma-1)))
mdot* = A**P0/sqrt(T0)*sqrt(gamma/R)*(2/(gamma+1))^((gamma+1)/(2*(gamma-1)))
P*/P0 = (2/(gamma+1))^(gamma/(gamma-1))
M2^2  = (1+(gamma-1)/2*M1^2)/(gamma*M1^2-(gamma-1)/2)
P2/P1 = 1+2*gamma/(gamma+1)*(M1^2-1)
```

---

## Tests Added (TEST_PLAN.md)

| Test | Description | Key expected value |
|------|-------------|-------------------|
| 24 | Speed of sound gamma=1.4, R=287, T=288 | a ~ 340 m/s |
| 25 | Mach number V=170, T=288 | M ~ 0.5, SUBSONICO |
| 26 | Isentropic ratios M=2, gamma=1.4 | T0/T=1.8, P0/P=7.824 |
| 27 | Area-Mach M=1, gamma=1.4 | A/A* = 1 |
| 28 | Normal shock M1=2, gamma=1.4 | M2=0.5774, P2/P1=4.5, P02/P01=0.7209 |

---

## Known Limitations

- Variable named `gam` (not `gamma`) to avoid conflict with HP Prime built-in
  GAMMA() function. INPUT labels display "gamma" correctly.
- Mach from A/A* bisection: range Mmin=0.01 to Mmax=10 for each branch.
  Very large Mach numbers (M > 10) not covered by the supersonic branch.
- Normal shock: only for M1 > 1 (physically required). Error shown otherwise.
- Mass flow formulas assume isentropic, perfect gas, no heat transfer.
- MF_CAS.hpprgm: NOT modified. Pending for final phase.

---

## Confirmation

- MF_CAS.hpprgm was NOT touched.
- No push to GitHub was performed.
- No Unicode, Greek letters, or LaTeX syntax in code strings.
- All EXPORT functions verified: grep -n '^EXPORT' *.hpprgm — OK.
- No backtick blocks in .hpprgm files — OK.
- No disallowed Unicode in .hpprgm files — OK.
