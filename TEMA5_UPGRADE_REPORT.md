# TEMA5 UPGRADE REPORT
## FLUIDOS PRIME v2.0 — Pipes, Pumps, Cavitation

---

## Files Changed

| File | Change |
|------|--------|
| `MF_PIPES.hpprgm` | Rewritten with 9-option menu |
| `MF_PUMPS.hpprgm` | Rewritten with 8-option menu |
| `README.md` | Updated modules 8 and 9, noted MF_CAS pending |
| `TEST_PLAN.md` | Added TEST 18-23 |
| `STRUCTURE.md` | Updated MF_PIPES and MF_PUMPS entries |

---

## New Options in MF_PIPES

| # | Option | Description |
|---|--------|-------------|
| 1 | V, Re, f | Computes A, V, Re, regime, f from Q, D, rho, mu, eps |
| 2 | Perdida tramo | hf and dP for a pipe section |
| 3 | Perdidas K | Minor losses hm = Ktot*V^2/(2g) |
| 4 | Sistema simple | Full system: V, Re, f, hf, hm, hL, dP |
| 5 | Sistema 2 tramos serie | Series pipes: V1, V2, Re1, Re2, f1, f2, h1, h2, hm, hL, dP |
| 6 | Diseno diametro | Bisection: find D such that DeltaZ + hL(D) = Hav (50 iter.) |
| 7 | K valvula regulacion | Compute Kvalve = hvalve / (V^2/2g) to balance system |
| 8 | Ayuda formulas | On-screen formula reference |
| 9 | Volver | Return to main menu |

Internal helpers added:
- `calcFF(reyn, eps, D)` — Darcy f, laminar or Swamee-Jain
- `calcHL_(Q, D, L, K, rho, mu, eps)` — total hL for one pipe section

---

## New Options in MF_PUMPS

| # | Option | Description |
|---|--------|-------------|
| 1 | Potencia bomba | Ph = rho*g*Q*H, Pshaft = Ph/eta |
| 2 | Carga bomba Bernoulli | Hpump from Bernoulli between two sections |
| 3 | NPSHa | Full suction pipe calc: V, Re, f, hLs, NPSHa |
| 4 | Check cavitacion | margin = NPSHa - NPSHr, diagnosis |
| 5 | Q max sin cavitacion | Bisection: NPSHa(Q) = NPSHr (40 iter.) |
| 6 | Punto operacion simple | Bisection: Hpump = Hsystem, curves H0-c*Q^2 and Hstatic+R*Q^2 |
| 7 | Ayuda formulas | On-screen formula reference |
| 8 | Volver | Return to main menu |

Internal helpers added:
- `calcFFp_(reyn, eps, D)` — friction factor (separate name avoids global conflict)
- `calcNPSHa_(Q, D, Ls, K, eps, mu, rho, Patm, Pv, zsuc)` — NPSHa for bisection

---

## Tests Added (TEST_PLAN.md)

| Test | Description | Key value |
|------|-------------|-----------|
| 18 | Pipe velocity Q=0.004, D=0.05 | V ~ 2.04 m/s |
| 19 | Reynolds for same case | Re ~ 101850 |
| 20 | Pump power rho=1000, Q=0.004, H=10, eta=0.8 | Pshaft ~ 490 W |
| 21 | Minor loss Ktot=1, V=2 m/s | hm ~ 0.204 m |
| 22 | Cavitation check NPSHa=8 > NPSHr=3.5 | No cavitacion |
| 23 | Cavitation check NPSHa=2 < NPSHr=3.5 | RIESGO CAVITACION |

---

## Known Limitations

- Sistema 2 tramos: minor losses (Ktot) are referenced to V1. User should
  assign K values for fittings in tramo 1 only, or note the convention.
- Diseno diametro: bisection assumes hL(D) is monotonically decreasing in D
  (larger D → lower losses). This holds for all physical cases.
- Q max sin cavitacion: assumes NPSHa decreases monotonically with Q.
  Input bracket [Qmin, Qmax] must straddle the root. Error shown if not.
- Punto operacion simple: uses simplified quadratic pump curve H0 - c*Q^2.
  For multi-speed or non-quadratic curves, use the full bisection form.
- MF_CAS.hpprgm: NOT modified. Still pending for final phase.

---

## Confirmation

- MF_CAS.hpprgm was NOT touched.
- No push to GitHub was performed.
- No Unicode, Greek letters, or LaTeX syntax introduced in code strings.
- All EXPORT functions verified present: grep -n '^EXPORT' *.hpprgm — OK.
- No backtick blocks in .hpprgm files — OK.
- No disallowed Unicode in .hpprgm files — OK.
