# MOM Elbow Reaction Fix Report

## 1. Previous Input Bug

The original `MF_MOM_BEND()` used large `INPUT` calls with many variables in one dialog. On HP Prime G2/Connectivity Kit this can become hidden multi-page input behavior, making navigation unreliable and cancellation confusing.

## 2. Input Change

The elbow option now uses several small `INPUT` screens, normally one or two variables each. Hydraulic inputs are collected first. Weight handling is then selected from a separate menu, so the user does not need to know `VolCV` unless they choose the volume-based fluid-weight mode.

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
- `Wy = -Wfluid`
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
- `Sx,Sy` are support force on the elbow assembly when total assembly weight is used.

## 5. Input Modes

- `Con diametros y presiones`
- `Con diametros sin presiones`
- `Con areas y presiones`
- `Con areas sin presiones`
- `P2 atmosferica`
- `P2 por Bernoulli`
- `Ayuda signos`
- `Volver`

## 6. Weight Modes

- `Ignorar peso`: sets `Wfluid = 0` and does not compute support force.
- `Peso fluido desde volumen`: asks `VolCV`, computes `Wfluid = rho*g*VolCV`, and includes it only in the fluid momentum equation.
- `Peso fluido directo`: asks `Wfluid` directly and includes it only in the fluid momentum equation.
- `Peso conjunto codo+fluido`: asks `Wtotal`, leaves `Wfluid = 0` in the fluid momentum equation, then computes support force for the physical assembly.

Total elbow plus fluid weight is not inserted into the fluid momentum equation because it includes solid elbow weight. It is used after the fluid reaction is known:

- `Sx = -Rx`
- `Sy = -Ry + Wtotal`
- `S = SQRT(Sx^2 + Sy^2)`

## 7. Test Cases

Updated `TEST_PLAN.md` with:

- 90 degree elbow, no pressures, ignore weight: `Rx=1000`, `Ry=-1000`, `R about 1414.2`.
- 90 degree elbow, direct fluid weight: `Rx=1000`, `Ry=-1196.2`, `R about 1559.1`.
- 90 degree elbow, total assembly weight: support `Sx=-1000`, `Sy=1500`, `S about 1802.8`.
- 90 degree elbow with inlet pressure and total assembly weight: support `Sx=-3000`, `Sy=1500`, `S about 3354.1`.
- Straight pipe, no pressure: `Rx=0`, `Ry=0`, `R=0`.

## 8. Known Limitations

- Diameter modes assume circular sections.
- Bernoulli mode assumes no pump or turbine and uses the supplied `hL`.
- The model is steady, one-dimensional at sections, and incompressible.
- The angle is approximate; force components are the primary result.

## 9. MF_CAS

`MF_CAS.hpprgm` was not touched.
