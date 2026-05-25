# MF_PIPES line 419 syntax fix

## 1. What was at line 419

Line 419 was the first LOCAL declaration in the new Darcy friction factor submenu:

`LOCAL op, Re, rr, rho, mu, Q, D, eps, A, V;`

## 2. Cause of the HP Prime syntax error

The identifier `Re` in the LOCAL declaration is risky on HP Prime because it can be parsed as a reserved or built-in token instead of a user variable name. The Connectivity Kit reported the error at that LOCAL line before any Darcy calculations were reached.

## 3. What was changed

Only the local program variable was renamed from `Re` to `reyn` inside `MF_PIPES_FD()`. User-facing labels still show `Re`.

## 4. LOG10 replacement

No LOG10 replacement was needed. The module already used `LOG` for base-10 logarithms, and the Darcy formulas continue to use `LOG`.

## 5. Colebrook availability

Colebrook iteration remains available in the Darcy submenu. The fixed-point calculation and residual output were preserved.

## 6. Files modified

- `MF_PIPES.hpprgm`
- `PIPES_LINE_419_FIX_REPORT.md`

## 7. MF_CAS status

`MF_CAS.hpprgm` was not touched.
