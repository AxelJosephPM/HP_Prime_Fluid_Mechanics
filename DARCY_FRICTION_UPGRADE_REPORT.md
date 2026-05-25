# Darcy Friction Upgrade Report

## 1. Files Modified

- `MF_PIPES.hpprgm`
- `README.md`
- `STRUCTURE.md`
- `TEST_PLAN.md`
- `DARCY_FRICTION_CURRENT_REPORT.md`
- `DARCY_FRICTION_UPGRADE_REPORT.md`

## 2. New Menu Options Added

`MF_PIPES()` now includes:

- `Factor f Darcy`

The new submenu contains:

- `Desde Re y rugosidad`
- `Desde datos de tuberia`
- `Colebrook iterativo`
- `Comparar formulas`
- `Ayuda f Darcy`
- `Volver`

## 3. Formulas Implemented

- Laminar: `f = 64/Re`
- Blasius: `f = 0.3164/Re^0.25`
- Haaland: `f = 1/(-1.8*LOG((rr/3.7)^1.11 + 6.9/Re))^2`
- Swamee-Jain: `f = 0.25/(LOG(rr/3.7 + 5.74/Re^0.9))^2`
- Darcy-Weisbach help: `hL = f*(L/D)*V^2/(2*g)`

The module uses `LOG` because the existing Swamee-Jain code already uses `LOG` as the working base-10 logarithm in HP PPL.

## 4. Colebrook Iteration

Colebrook-White is solved with fixed-point iteration:

- `fNew = 1/(-2*LOG(rr/3.7 + 2.51/(Re*SQRT(fOld))))^2`

The initial value is Haaland when possible. If the initial value is invalid, the solver uses `fOld = 0.02`.

## 5. Convergence Check

The solver uses:

- `tol = 1E-7`
- `iterMax = 50`
- `err = ABS(fNew - fOld)`

The loop stops by reducing the local iteration limit to the current iteration when `err < tol`, avoiding `BREAK`.

The focused Colebrook option also reports the residual:

- `res = 1/SQRT(f) + 2*LOG(rr/3.7 + 2.51/(Re*SQRT(f)))`

## 6. Recommended Friction Factor Logic

- `Re < 2300`: recommend laminar `64/Re`.
- `2300 <= Re < 4000`: show transition warning and use Colebrook only as an orientation value.
- `Re >= 4000`: recommend Colebrook.

All factors are Darcy friction factors. The help notes `f_Darcy = 4*f_Fanning`.

## 7. Test Cases

Added tests for:

- Laminar `Re=1000`, `rr=0`, expected `f=0.064`.
- Smooth turbulent `Re=100000`, `rr=0`.
- Commercial steel-like roughness `Re=100000`, `rr=0.00045`.
- Rough turbulent `Re=1000000`, `rr=0.001`.
- Pipe-data input with `rho=1000`, `mu=0.001`, `Q=0.01`, `D=0.1`, `eps=0.000045`.

## 8. Known Limitations

- Colebrook is reported as a turbulent formula; transition results are only orientative.
- Existing pipe-loss options still use the established `calcFF()` helper based on laminar/Swamee-Jain to preserve previous behavior.
- The new detailed Darcy comparison is available through the new `Factor f Darcy` submenu.

## 9. MF_CAS

`MF_CAS.hpprgm` was not edited.
