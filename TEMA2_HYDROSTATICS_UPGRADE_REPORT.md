# TEMA2 HYDROSTATICS UPGRADE REPORT
## FLUIDOS PRIME v2.0 — Manometers, Hydrostatics, Buoyancy Modules

---

## Files Changed

| File | Change |
|------|--------|
| `MF_MANO.hpprgm` | Rewritten with 8-option menu |
| `MF_HYDRO.hpprgm` | Rewritten with 8-option menu |
| `MF_BUOY.hpprgm` | Rewritten with 8-option menu |
| `README.md` | Updated modules 2, 3, 4 descriptions |
| `TEST_PLAN.md` | Added TEST 39-44 |
| `STRUCTURE.md` | Updated MF_MANO, MF_HYDRO, MF_BUOY entries |

---

## New Options in MF_MANO

| # | Function | Description |
|---|----------|-------------|
| 1 | MF_MANO_HIDRO | P = P0 + rho*g*h |
| 2 | MF_MANO_SIM | DeltaP = (rho_m - rho_f)*g*dh (U tube) |
| 3 | MF_MANO_DIF | Differential manometer, same formula |
| 4 | MF_MANO_INC | Inclined: dh = L*sin(theta), two-fluid DeltaP |
| 5 | MF_MANO_ABS | Abs/gauge/vacuum sub-menu |
| 6 | MF_MANO_HEQV | h = DeltaP/(rho*g) |
| 7 | MF_MANO_HELP | Compact formula reference |
| 8 | Volver | Return to main menu |

Key improvements over v1:
- Option 1 (hydrostatic pressure) added directly to manometer menu
- Options 2-4 now use two-fluid formula consistently (rho_mano, rho_fluid)
- Option 6 (height equivalent) is new
- Help option added

---

## New Options in MF_HYDRO

| # | Function | Description |
|---|----------|-------------|
| 1 | MF_HYDRO_FV | F = rho*g*yc*A (vertical, inputs: rho,g,A,yc) |
| 2 | MF_HYDRO_FI | F = rho*g*yc*A (inclined, inputs: rho,g,A,yc) |
| 3 | MF_HYDRO_CPV | yp = yc + Ixc/(yc*A) |
| 4 | MF_HYDRO_CPI | yp = yc + Ixc*sin2(theta)/(yc*A) |
| 5 | MF_HYDRO_RECT | Full rectangular gate: A, yc, F, yp from b, h, y_top |
| 6 | MF_HYDRO_FH | F = rho*g*h*A (horizontal surface) |
| 7 | MF_HYDRO_HELP | Compact formula reference |
| 8 | Volver | Return to main menu |

Key improvements over v1:
- Force options now take yc and A directly (simpler for general geometry)
- Center of pressure separated into dedicated options (3 and 4)
- Rectangular gate option (5) computes all quantities in one step
- Help option added

---

## New Options in MF_BUOY

| # | Function | Description |
|---|----------|-------------|
| 1 | MF_BUOY_FB | Fb = rho_fluid*g*Vdisp |
| 2 | MF_BUOY_FL | Float/sink/neutral by density comparison |
| 3 | MF_BUOY_FRAC | frac = rho_obj/rho_fluid, with percentages |
| 4 | MF_BUOY_VSUB | Vsub = m/rho_fluid |
| 5 | MF_BUOY_RHOOBJ | rho_obj = m/V |
| 6 | MF_BUOY_CMAX | Max payload: m_payload = rho_fluid*Vmax - m_body |
| 7 | MF_BUOY_HELP | Compact formula reference |
| 8 | Volver | Return to main menu |

Key improvements over v1:
- Options 4-6 are new: volume submerged, object density, max payload
- Fraction submerged shows percentages (submerged and exposed)
- Help option added

---

## Tests Added (TEST_PLAN.md)

| Test | Description | Key expected value |
|------|-------------|-------------------|
| 39 | Hydrostatic P: rho=1000, h=2, P0=0 | P = 19620 Pa |
| 40 | U manometer: rho_m=13600, rho_f=1000, dh=0.1 | DeltaP ~ 12360 Pa |
| 41 | Vertical plate: rho=1000, A=2, yc=3 | F = 58860 N |
| 42 | Rectangular gate: b=2, h=3, y_top=1 | A=6, yc=2.5, F=147150 N, yp=2.8 m |
| 43 | Buoyancy: rho_f=1000, Vdisp=0.1 | Fb = 981 N |
| 44 | Submerged fraction: rho_obj=600, rho_f=1000 | frac = 0.6 (60%) |

---

## Known Limitations

- MF_HYDRO options 1-2 use F = rho*g*yc*A only. Center of pressure must be
  computed separately using options 3 or 4.
- MF_HYDRO_RECT (option 5) assumes vertical plate only. For inclined gate
  use options 2 and 4 in sequence.
- MF_MANO options 2 and 3 use the same formula; option 3 omits P1 (absolute
  value) to keep it focused on differential pressure only.
- MF_BUOY_CMAX: Vmax is the total available displaced volume of the hull.
  Does not account for freeboard or stability.

---

## Confirmation

- MF_CAS.hpprgm was NOT touched.
- No push to GitHub was performed.
- No Unicode, Greek letters, or LaTeX syntax in code strings.
- All EXPORT functions verified: grep -n '^EXPORT' *.hpprgm -- OK (17 exports).
- No backtick blocks in .hpprgm files -- OK.
- No disallowed Unicode in .hpprgm files -- OK.
