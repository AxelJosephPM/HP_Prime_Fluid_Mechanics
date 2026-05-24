# Angle Mode Audit Report

## 1. Audit Command

Requested broad audit command:

`grep -nEi 'SIN|COS|TAN|ASIN|ACOS|ATAN|HAngle' *.hpprgm`

That command also matches Spanish text such as `densidad` and `sin`. The actual trigonometric function-call audit found these calculator blocks:

- `MF_MANO.hpprgm`: inclined manometer, `SIN(thetaRad)`.
- `MF_HYDRO.hpprgm`: inclined center of pressure, `SIN(thetaRad)`.
- `MF_MOM.hpprgm`: resultant angle helper, elbow velocity components and reaction angle, jet deflection.

## 2. Files With Trigonometric Function Calls

### MF_MANO.hpprgm

Block: `MF_MANO_INC()`

- Line/block: inclined manometer, `dh = L*SIN(thetaRad)`.
- Input angle: degrees, `thetaDeg`.
- Manual conversion: `thetaRad = thetaDeg*PI/180`.
- Previous dependency: yes, `SIN(thetaRad)` could depend on global `HAngle`.
- Fix applied: save `oldAng`, set `HAngle := 0`, compute `SIN`, restore `HAngle`.

### MF_HYDRO.hpprgm

Block: `MF_HYDRO_CPI()`

- Line/block: inclined plate center of pressure, `sinT = SIN(thetaRad)`.
- Input angle: degrees, `thetaDeg`.
- Manual conversion: `thetaRad = thetaDeg*PI/180`.
- Previous dependency: yes, `SIN(thetaRad)` could depend on global `HAngle`.
- Fix applied: save `oldAng`, set `HAngle := 0`, compute `SIN`, restore `HAngle`.

### MF_MOM.hpprgm

Block: `angulo_(Fx, Fy)`

- Line/block: `ATAN(Fy/Fx)*180/PI`.
- Input ratio: dimensionless, ATAN should be evaluated in radians before manual degree conversion.
- Manual conversion: output is converted to degrees.
- Previous dependency: yes, `ATAN` could return in the current global angle mode.
- Fix applied: save `oldAng`, set `HAngle := 0`, compute `ATAN`, restore `HAngle`.

Block: `MF_MOM_BEND()`

- Line/block: elbow velocity components, pressure direction components, and reaction angle.
- Input angles: degrees, `th1`, `th2`.
- Manual conversion: `rad1 = th1*PI/180`, `rad2 = th2*PI/180`.
- Previous dependency: fixed previously; this audit verified the block saves `HAngle`, sets radians mode, computes `SIN`, `COS`, `ATAN`, and restores `HAngle`.
- Fix applied in this audit: verified and retained.

Block: `MF_MOM_DEF()`

- Line/block: jet deflection, `COS(theta*pi/180)`, `SIN(theta*pi/180)`.
- Input angle: degrees, `theta`.
- Manual conversion: inline `theta*PI/180`.
- Previous dependency: yes, `SIN` and `COS` could depend on global `HAngle`.
- Fix applied: save `oldAng`, set `HAngle := 0`, compute `SIN` and `COS`, restore `HAngle`.

## 3. Files Audited Without Trig Fixes

- `MF_PROPS.hpprgm`: no trigonometric function calls in the current file. The expected capillarity calculator is not present in this checkout.
- `MF_CAS.hpprgm`: read-only audit only; not edited.
- Other `.hpprgm` files: the broad grep matches text strings and Spanish words, but no additional actual `SIN`, `COS`, `TAN`, `ASIN`, `ACOS`, or `ATAN` function calls were found.

## 4. MF_CAS

`MF_CAS.hpprgm` was not edited.
