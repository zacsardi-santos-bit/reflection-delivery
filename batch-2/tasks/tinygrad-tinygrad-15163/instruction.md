I'm working on improving tinygrad's symbolic expression simplifier and noticed a gap in the modulo simplification rules.

*   The symbolic simplification system must implement a modulo nesting rule: for an expression (a*f + b) mod (f*k), when the remainder term b is known to lie in the range [0, f), the expression must simplify to b + (a mod k)*f.

*   The expression (gidx0*4+lidx0)%8, where gidx0 is in [0,15] and lidx0 is in [0,3], must simplify to the canonical string '(lidx0+gidx0%2*4)'.

*   The expression (gidx0*2+lidx1)%8, where gidx0 is in [0,15] and lidx1 is in [0,1], must simplify to the canonical string '(lidx1+gidx0%4*2)'.

*   The expression (a*3+b)%9, where a is in [0,10] and b is in [0,2], must simplify to the canonical string '(b+a%3*3)'.

*   After the modulo nesting rule fires, the existing recombination identity — that the sum of (x floor-divided by n, times n) and (x mod n) equals x — must still fire on the nested forms. Specifically: (x//8)*8 + x%8 where x=gidx0*4+lidx0 must simplify to '(lidx0+gidx0*4)'; (x//8)*16 + (x%8)*2 must simplify to '(gidx0*8+lidx0*2)'; (y//12)*12 + y%12 where y=a*6+b must simplify to '(b+a*6)'.

*   A real-world image index expression whose x-component was previously simplified to '((((idx0%8)*32)+(idx0//32))%64)' must now simplify further to '(idx0%2*32+idx0//32)' as a result of the modulo nesting rule.

*   A real-world image load validity condition that previously simplified to '(((idx0+(idx1*64))%192)<160)' must now simplify to None (no validity constraint) after the improved index optimization.

*   The image index optimization pass that selects among candidate index expressions by minimizing expression complexity must measure complexity using the candidate expression's raw node count (backward slice length) in its current form — not the node count after applying an additional simplification step to the candidate before scoring.


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.