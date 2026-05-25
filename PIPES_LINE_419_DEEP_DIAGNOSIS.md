# MF_PIPES line 419 deep diagnosis

## 1. Exact line 419 before the controlled reduction

Before this fix, line 419 was:

`LOCAL op, reyn, rr, rho, mu, Q, D, eps, A, V;`

After the controlled reduction, the line number moved into the compact Colebrook loop.

## 2. Twenty lines before and after the reported area

The reported area before the reduction was the start of `MF_PIPES_FD()`:

- Lines 399-414 completed `MF_PIPES_KVAL()` and closed with `END;`.
- Line 417 started `MF_PIPES_FD()`.
- Line 418 was `BEGIN`.
- Line 419 was the first `LOCAL` declaration.
- Lines 420-423 were additional `LOCAL` declarations.
- Line 425 started the Darcy `CHOOSE` menu.
- Lines 435 onward contained the large Darcy submenu implementation.

## 3. Whether line 419 itself was suspicious

The corrected identifier `reyn` was not suspicious by itself. The persistent error meant the parser was failing at the start of the Darcy helper body, not because that exact LOCAL line was the only problem.

## 4. Unclosed IF, WHILE, CHOOSE, or INPUT before line 419

No unclosed control block was found above line 419. `MF_PIPES_KVAL()` closed correctly at line 414 with `END;`.

## 5. LOCAL after executable statements

No LOCAL declaration appeared after executable statements in `MF_PIPES_FD()`. The LOCAL declarations were at the top of the function body.

## 6. Parentheses in formulas

The original Darcy block had several complex formulas and repeated Colebrook loops. No obvious parenthesis mismatch was found, but the block had enough parser risk to justify a controlled reduction.

## 7. MSGBOX strings

The original Darcy block used several long multi-line MSGBOX expressions. These were reduced to shorter MSGBOX screens.

## 8. LOG10

`LOG10` was not present. The project uses `LOG` for base-10 logarithms, and the fixed block continues to use `LOG`.

## 9. Darcy option placement

The Darcy option remains a helper function called by the main `EXPORT MF_PIPES()` menu. It is not inside `EXPORT MF_PIPES()`.

## 10. Exact fix

The Darcy-only helper functions and the large Darcy submenu body were replaced with a compact `MF_PIPES_FD()` implementation. It keeps:

- input from `Re` and `rr`
- laminar `f = 64/Re`
- Haaland
- Swamee-Jain
- Colebrook fixed-point iteration

The reduced implementation avoids long menus, long strings, comparison percent outputs, pipe-data input mode, and duplicate Colebrook loops.
