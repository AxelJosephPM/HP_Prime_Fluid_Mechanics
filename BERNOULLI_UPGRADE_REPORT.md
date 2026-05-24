# BERNOULLI UPGRADE REPORT
## FLUIDOS PRIME v2.0 — Bernoulli and Energy Equation Module

---

## Files Changed

| File | Change |
|------|--------|
| `MF_BERN.hpprgm` | Rewritten with 10-option menu (was 5) |
| `README.md` | Updated module 5 description |
| `TEST_PLAN.md` | Added TEST 45-49 |
| `STRUCTURE.md` | Updated MF_BERN entry |

---

## New Options in MF_BERN

| # | Function | Description |
|---|----------|-------------|
| 1 | MF_BERN_BAL | Balance check: E1 + hPump - hTurb - hL = E2 |
| 2 | MF_BERN_P2 | Solve P2: P2 = rho*g*(E1 + hPump - hTurb - hL - V2^2/2g - z2) |
| 3 | MF_BERN_V2G | Solve V2: V2 = sqrt(2*g*term), validates term >= 0 |
| 4 | MF_BERN_Z2 | Solve z2: z2 = E1 + hPump - hTurb - hL - P2/rg - V2^2/2g |
| 5 | MF_BERN_PITOT | Pitot: V = sqrt(2*DeltaP/rho) or from manometer dh |
| 6 | MF_BERN_VENT | Ideal Venturi: Q = A2*sqrt(2*DeltaP/(rho*(1-(A2/A1)^2))) |
| 7 | MF_BERN_TORR | Torricelli: V = sqrt(2*g*h); Q = A*V if A > 0 |
| 8 | MF_BERN_ORIC | Orifice with Cd: Q = Cd*A*sqrt(2*g*h) |
| 9 | MF_BERN_HEAD | Energy heads: hp = P/rg, hv = V^2/2g, H = hp+hv+z |
| 10 | MF_BERN_HELP | Compact formula reference |
| 11 | Volver | Return to main menu |

Key improvements over v1:
- Option 1 (balance check) is new — verifies both sides equal
- Options 3 and 4 (solve V2, solve z2) are new
- Options 5-9 (Pitot, Venturi, Torricelli, Orifice, Heads) are new
- All options use consistent two-step INPUT for complex dialogs
- Pitot has sub-menu: from DeltaP or from differential manometer
- Input validation on all options (rho > 0, g > 0, hL >= 0, Cd in (0,1], D1 > D2, etc.)

---

## Tests Added (TEST_PLAN.md)

| Test | Description | Key expected value |
|------|-------------|-------------------|
| 45 | Despejar P2: P1=200000, V1=2, V2=5, rho=1000, z=0, no losses | P2 = 189500 Pa |
| 46 | Pitot desde DeltaP: DeltaP=500 Pa, rho=1.25 kg/m3 (aire) | V = 28.284 m/s |
| 47 | Torricelli: h=2 m, g=9.81, A=0 | V = 6.264 m/s |
| 48 | Orificio Cd=0.62, A=0.01, h=2 m | Q = 0.0388 m3/s (38.84 L/s) |
| 49 | Alturas: P=98100 Pa, V=2, z=3, rho=1000 | hp=10 m, H=13.204 m |

---

## Known Limitations

- MF_BERN_VENT assumes ideal Venturi (no losses, Cv=1). For real Venturi apply
  a discharge coefficient externally: Q_real = Cv * Q_ideal.
- MF_BERN_BAL computes residual only; it does not solve for any unknown.
  Use options 2-4 to solve for P2, V2, or z2 directly.
- Torricelli assumes free surface with constant head (quasi-steady assumption).
- Pitot sub-option 2 (from manometer) uses rho_fluid for the velocity calculation,
  not rho_mano.

---

## Confirmation

- MF_CAS.hpprgm was NOT touched.
- No push to GitHub was performed.
- No Unicode, Greek letters, or LaTeX syntax in code strings.
- EXPORT MF_BERN() at line 278 — verified with grep -n '^EXPORT'.
- No backtick blocks in .hpprgm files -- OK.
- 17 total EXPORT functions across project -- OK.
