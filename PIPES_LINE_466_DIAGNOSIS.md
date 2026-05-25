# MF_PIPES line 466 diagnosis

## 1. Exact text of line 466

Before the fix, line 466 was:

`LOCAL g, A, V, reyn, hMajor, hMinor, hTotal, dP, Ploss;`

After the fix, the line number moved into the input prompt block for the simplified `MF_PL_FG()` helper.

## 2. Twenty lines before and after line 466

Before the fix, the area was:

- Line 446 closed the working Darcy factor helper.
- Lines 448-461 defined `MF_PIPES_LOSS_OUT(...)`, a new shared output helper with eight parameters.
- Lines 463-466 started `MF_PIPES_LOSS_FG()` and its LOCAL declarations.
- Lines 468-487 contained the first small INPUT screens for the given-f pipe loss option.

## 3. Whether line 466 itself was suspicious

Line 466 was a normal LOCAL declaration. It was not suspicious by itself.

## 4. Block structure above line 466

No unclosed IF, WHILE, REPEAT, CHOOSE, INPUT, or EXPORT block was found above line 466. The working Darcy factor helper closed with `END;` before the new pipe-loss helpers began.

## 5. LOCAL after executable code

No LOCAL declaration appeared after executable code in the failing helper. The LOCAL lines were at the beginning of `MF_PIPES_LOSS_FG()`.

## 6. MSGBOX/string check

No malformed string was found at line 466. The output helper before it contained several multi-line MSGBOX calls, but quotes and parentheses were balanced.

## 7. Formula parenthesis check

No mismatched parentheses were found in the head-loss formulas.

## 8. LOG10

`LOG10` was not present. The file continues to use `LOG`, matching the working Darcy factor code.

## 9. Did the error belong to the pipe losses block?

Yes. The previous Darcy factor block was already working. The new parser failure occurred at the start of the first new pipe-loss helper, immediately after the new shared output helper.

## 10. Exact fix applied

The new pipe-loss code was simplified without disabling functionality:

- Removed the shared `MF_PIPES_LOSS_OUT(...)` helper with eight parameters.
- Inlined the short output MSGBOX screens inside the pipe-loss calculation helpers.
- Shortened the new helper names to `MF_PL_FG()`, `MF_PL_COLE()`, and `MF_PL_MIN()`.
- Kept the `Perdidas tuberia` menu active through `MF_PIPES_LOSSES()`.
