# Current 2D Elbow Reaction Report

## 1. Menu Option

`Reaccion en codo 2D` is implemented by `MF_MOM_BEND()` in `MF_MOM.hpprgm`. It is called from the `MF_MOM()` main menu option `2. Reaccion en codo 2D`.

## 2. Existing Input Modes

The current submenu contains:

- `Con diametros y presiones`
- `Con diametros sin presiones`
- `Con areas y presiones`
- `Con areas sin presiones`
- `P2 atmosferica`
- `P2 por Bernoulli`
- `Ayuda signos`
- `Volver`

## 3. Current Variables

- Density: `rho`
- Volumetric flow: `Q`
- Mass flow: `mdot = rho*Q`
- Diameters: `D1`, `D2`
- Areas: `A1`, `A2`
- Angles: `th1`, `th2`
- Pressures: `P1`, `P2`
- Fluid volume: `VolCV`
- Fluid weight: `Wfluid`
- Total assembly weight: `Wtotal`
- Elevations/loss: `z1`, `z2`, `hL`

## 4. Current Formulas

- `V1 = Q/A1`
- `V2 = Q/A2`
- `rad1 = th1*PI/180`
- `rad2 = th2*PI/180`
- `V1x = V1*COS(rad1)`
- `V1y = V1*SIN(rad1)`
- `V2x = V2*COS(rad2)`
- `V2y = V2*SIN(rad2)`
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
- `Sx = -Rx`
- `Sy = -Ry + Wtotal`

## 5. Why It Does Not Match Tema 4

The solved Tema 4 elbow exercises compare the class-style support or anchor reaction, written as:

- `Fanch_x = Mx - Fpx`
- `Fanch_y = My - Fpy + W`

The current program makes `Rx,Ry` the primary displayed result, where `Rx,Ry` are the force of the fluid on the elbow. That is the opposite sign of the wall force on the fluid and is not the same output convention used by the solved class exercises. The support output `Sx,Sy` is only shown in one weight mode and uses an equilibrium sign convention that does not match the class formulas above.

The Bernoulli option also computes `P2` from a known `P1`, while the Tema 4 reducing elbow exercise needs `P1g` computed from atmospheric outlet pressure, `dz`, and velocity change.

## 6. Variable To Compare

After the fix, the solved exercise result must be compared against:

- `Fx = Fanch_x`
- `Fy = Fanch_y`
- `F = Fanch`

These are the support/anchor reaction values in the Tema 4 convention.
