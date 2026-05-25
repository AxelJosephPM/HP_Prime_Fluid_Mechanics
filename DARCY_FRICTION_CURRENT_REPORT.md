# Darcy Friction Current Report

## 1. Current MF_PIPES Menu Options

The current `MF_PIPES()` menu contains:

- `V, Re, f`
- `Perdida tramo`
- `Perdidas K`
- `Sistema simple`
- `Sistema 2 tramos serie`
- `Diseno diametro`
- `K valvula regulacion`
- `Ayuda formulas`
- `Volver`

## 2. Current Friction Factor Formulas

The helper `calcFF(reyn, eps, D)` currently uses:

- laminar: `f = 64/Re` for `Re < 2300`
- turbulent/transition approximation: `f = 0.25/(LOG(eps/(3.7*D) + 5.74/Re^0.9))^2`

This turbulent formula is Swamee-Jain written with absolute roughness and diameter.

## 3. Current Logarithm Function

The current project uses `LOG(arg)` in `MF_PIPES.hpprgm` for the Swamee-Jain base-10 logarithm. This file imports with the existing code, so the Darcy upgrade keeps `LOG` for Colebrook, Haaland, and Swamee-Jain.

## 4. Existing Darcy-Weisbach Support

The module already uses Darcy-Weisbach head loss:

- `hf = f*(L/D)*V^2/(2*g)`

The existing `f` is therefore the Darcy friction factor, not the Fanning friction factor.

## 5. Integration Point

The new `Factor f Darcy` option should be added to the `MF_PIPES()` menu before `Ayuda formulas`, so existing pipe-flow tools remain available and help remains near the end.

## 6. Syntax Risks Before Editing

- Existing `INPUT` calls in the pipe module sometimes use more than four variables. The new feature should keep small `INPUT` screens.
- Existing code uses `LOG`, not `LOG10`; switching syntax would risk import errors.
- No `ELSEIF` or `GETKEY` pattern is needed.
- `MF_CAS.hpprgm` must not be edited.
