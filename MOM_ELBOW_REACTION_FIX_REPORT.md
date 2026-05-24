# MOM Elbow Reaction Fix Report

## 1. Previous Input Bug

The previous `MF_MOM_BEND()` version used large `INPUT` calls with many variables in one dialog. On HP Prime G2/Connectivity Kit this can become hidden multi-page input behavior, making navigation unreliable and cancellation confusing.

## 2. Input Change

The elbow option now uses several small `INPUT` screens, each with one or two variables. This avoids hidden input pages and returns safely to the `MF_MOM` menu if the user cancels any screen.

## 3. Correct Formulation

The calculation uses a steady control volume around the fluid in the elbow:

- `V1x = V1*COS(rad1)`
- `V1y = V1*SIN(rad1)`
- `V2x = V2*COS(rad2)`
- `V2y = V2*SIN(rad2)`
- `mdot = rho*Q`
- `Mx = mdot*(V2x - V1x)`
- `My = mdot*(V2y - V1y)`
- `Fpx = P1*A1*COS(rad1) - P2*A2*COS(rad2)`
- `Fpy = P1*A1*SIN(rad1) - P2*A2*SIN(rad2)`
- `Wx = 0`
- `Wy = -rho*g*VolCV`
- `Fwx = Mx - Fpx - Wx`
- `Fwy = My - Fpy - Wy`
- `Rx = -Fwx`
- `Ry = -Fwy`

## 4. Sign Convention

- `+x` is positive to the right.
- `+y` is positive upward.
- `theta1` and `theta2` are absolute velocity directions measured from `+x`.
- `P1` and `P2` are gauge pressures.
- `Fwx,Fwy` are force of elbow/wall on fluid.
- `Rx,Ry` are force of fluid on elbow.
- `VolCV = 0` ignores weight of fluid in the control volume.

## 5. Input Modes

- `Con diametros y presiones`
- `Con diametros sin presiones`
- `Con areas y presiones`
- `Con areas sin presiones`
- `P2 atmosferica`
- `P2 por Bernoulli`
- `Ayuda signos`
- `Volver`

## 6. Test Cases

Added or updated these `TEST_PLAN.md` cases:

- 90 degree elbow, areas, no pressure, no weight: `Rx=1000`, `Ry=-1000`, `R about 1414.2`.
- 90 degree elbow, areas, inlet pressure, no weight: `Rx=3000`, `Ry=-1000`, `R about 3162.3`.
- Straight pipe, no pressure: `Rx=0`, `Ry=0`, `R=0`.
- 90 degree elbow with weight: `Wx=0`, `Wy=-196.2`, `Rx=1000`, `Ry=-1196.2`.

## 7. Known Limitations

- Diameter modes assume circular sections.
- Bernoulli mode assumes no pump or turbine and uses the supplied `hL`.
- The model is steady, one-dimensional at sections, and incompressible.
- The angle is approximate; force components are the primary result.

## 8. MF_CAS

`MF_CAS.hpprgm` was not touched.
