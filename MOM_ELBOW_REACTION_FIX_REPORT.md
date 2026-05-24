# MOM Elbow Reaction Fix Report

## 1. Previous Issue

The previous `MF_MOM_BEND()` implementation asked directly for both pressures and returned `Rx` and `Ry` from a compact expression. It did not show the pressure-force contribution, the wall/support force on the fluid, or the opposite reaction of the fluid on the bend. That made the sign convention easy to misread and caused results that did not match the standard control-volume momentum equation checks.

## 2. Formulation Used

The corrected calculation uses a steady control volume around the fluid in the elbow:

- `V1x = V1*cos(theta1)`, `V1y = V1*sin(theta1)`
- `V2x = V2*cos(theta2)`, `V2y = V2*sin(theta2)`
- `mdot = rho*Q`
- `Mx = mdot*(V2x - V1x)`
- `My = mdot*(V2y - V1y)`
- `Fpx = P1*A1*cos(theta1) - P2*A2*cos(theta2)`
- `Fpy = P1*A1*sin(theta1) - P2*A2*sin(theta2)`
- `Fwx = Mx - Fpx`
- `Fwy = My - Fpy`
- `Rx = -Fwx`
- `Ry = -Fwy`

## 3. Sign Convention

- `+x` is positive to the right.
- `+y` is positive upward.
- `theta` is measured counterclockwise from `+x`.
- `P1` and `P2` are gauge pressures.
- `Fwx,Fwy` are the force of the wall/support on the fluid.
- `Rx,Ry` are the force of the fluid on the bend.

## 4. Input Modes Added

- `Datos completos`: inputs `rho`, `Q`, `A1`, `A2`, `P1`, `P2`, `theta1`, `theta2`.
- `Sin presiones`: sets `P1 = 0`, `P2 = 0`.
- `P2 atmosferica`: inputs `P1` and sets `P2 = 0`.
- `Calcular P2 Bernoulli`: estimates `P2` from Bernoulli without pump or turbine.

## 5. Test Cases Added

Added three `MF_MOM_BEND()` tests to `TEST_PLAN.md`:

- 90 degree bend with no pressure.
- 90 degree bend with inlet gauge pressure and atmospheric outlet.
- Straight pipe with no pressure.

## 6. Known Limitations

- Areas are entered directly; diameters are not converted inside this option.
- Weight of fluid inside the bend is not included.
- Bernoulli mode assumes no pump or turbine and uses the supplied head loss `hL`.
- The reaction angle is reported as approximate and depends on `ATAN`.

## 7. MF_CAS

`MF_CAS.hpprgm` was not touched.
