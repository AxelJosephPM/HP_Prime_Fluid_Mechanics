# TEMA7 DIFFERENTIAL UPGRADE REPORT
## FLUIDOS PRIME v2.0 — Differential and Viscous Flow Module

---

## Files Changed

| File | Change |
|------|--------|
| `MF_DIFF.hpprgm` | New module: 8-option menu |
| `FLUIDOS.hpprgm` | Added "Diferencial" to FLUIDOS_P2_() sub-menu |
| `README.md` | Added module 11 description |
| `TEST_PLAN.md` | Added TEST 29-33 |
| `STRUCTURE.md` | Added MF_DIFF architecture entry |

---

## New Module: MF_DIFF

| # | Function | Description |
|---|----------|-------------|
| 1 | MF_DIFF_TAU | tau = mu*(du/dy) or du/dy = tau/mu |
| 2 | MF_DIFF_PUIS | Poiseuille tube: Q, Vavg, Vmax, tau_w |
| 3 | MF_DIFF_COUETTE | Couette flow: u(y) = U*y/h, tau = mu*U/h |
| 4 | MF_DIFF_PUIS2 | Plane Poiseuille: Q, Vavg, Vmax, tau_w |
| 5 | MF_DIFF_CAMP | Linear 2D field: divergence, vorticity |
| 6 | MF_DIFF_DEF | 2D deformation: exx, eyy, exy |
| 7 | MF_DIFF_VORT | Free vortex: vtheta = C/r |
| 8 | MF_DIFF_HELP | Compact formula reference |
| 9 | Volver | Return to main menu |

---

## Key Formulas Implemented

```
Newton:           tau = mu * du/dy
Poiseuille tube:  Q = PI*R^4*dP/(8*mu*L),  Vavg = Q/(PI*R^2),  Vmax = 2*Vavg,  tau_w = 4*mu*Vavg/R
Couette:          u(y) = U*y/h,  tau = mu*U/h
Plane Poiseuille: Q = (2/3)*(dP/(2*mu*L))*h^3*W,  Vavg = Q/(2*h*W),  Vmax = (dP/(2*mu*L))*h^2,  tau_w = (dP/L)*h
Linear 2D:        u = a*x + b*y,  v = c*x + d*y
                  div = a + d  (incompressible if div = 0)
                  rotz = c - b  (2D vorticity)
Deformation 2D:   exx = a,  eyy = d,  exy = (b + c)/2
Free vortex:      vtheta = C / r
```

---

## Tests Added (TEST_PLAN.md)

| Test | Description | Key expected value |
|------|-------------|-------------------|
| 29 | Newton tau, mu=0.001, du/dy=100 | tau = 0.1 Pa |
| 30 | Poiseuille tubo D=0.01, dP=400 | Q ~ 9.82e-5 m3/s, Vavg ~ 1.25 m/s |
| 31 | Couette U=1, h=0.005, y=0.0025 | u = 0.5 m/s, tau = 0.2 Pa |
| 32 | Campo 2D a=1,b=2,c=-1,d=-1 | div=0, rotz=-3 |
| 33 | Vortice libre C=1, r=1 | vtheta = 1.0 m/s |

---

## FLUIDOS.hpprgm Change

FLUIDOS_P2_() updated:
- Title changed to "FLUIDOS PRIME v2.0"
- Added "Diferencial" option between "Compresible" and "CAS"
- Exit condition updated from op2==6 to op2==7
- CAS renumbered to op2==5, Utilidades to op2==6

---

## Known Limitations

- Plane Poiseuille (option 4): inputs are half-gap h, width W, and pressure gradient dP/L.
  The result Q is per full channel (both halves: width 2*h, total width W).
- Campo 2D (option 5): only linear fields (constant gradient coefficients a,b,c,d).
  Non-linear fields require CAS module.
- Deformation (option 6): uses same linear field inputs. Normal and shear rates only.
  No principal strain directions computed.
- Free vortex (option 7): irrotational (vtheta*r = C = const). Does NOT handle
  forced (solid-body) rotation vtheta = omega*r.

---

## Confirmation

- MF_CAS.hpprgm was NOT touched.
- No push to GitHub was performed.
- No Unicode, Greek letters, or LaTeX syntax in code strings.
- All EXPORT functions verified: grep -n '^EXPORT' *.hpprgm -- OK (16 exports total).
- No backtick blocks in .hpprgm files -- OK.
- No disallowed Unicode in .hpprgm files -- OK.
