# Puzzle No. 3 – Wednesday 5 July

### This is no ordinary crossword!

> Instead of a word or phrase, each clue is a regular expression (or a ‘regex’).
> To complete the puzzle, find the letter matching both the horizontal and
> vertical regex for each square.
> 
> ![BBC RegEx Crossword](puzzle.jpg)
> 
> (_[source](http://www.bbc.co.uk/programmes/articles/5LCB3rN2dWLqsmGMy5KYtBf/puzzle-for-today)_)

---

## Step 0 - Setup the puzzle and clean regexes

OK, so first thing, let's (painstakingly) write out the
[Regular Expressions](http://www.regular-expressions.info/) that are in the
attached image:

    0: [^XZCVHFJLQM]+
    1: [^PZVJG]{4}(.)[EFUG]{6}\1[^\sPZVJI]{2}
    2: [^\sPQFB]{7}[^MGVAJNZ\s]+[^MVZJ]
    3: N[OYSRU]{5}[NICE]{6}\s\-
    4: .A[A\sDL]{4}O[AECLV\s]+
    
    A: \sA?(SA|PE|N\s){2}
    B: ([XYZ])(P|GO|EL)\1.
    C: (LS|CA|OS)[L\sP][DO]{2}
    D: (U)(T)\2\1[AOB?]
    E: (.)\1\1\1\s
    F: (FF|BE|QU|OS){2}L
    G: ES?F?(OBZ|UCO|PTE)
    H: S[MVU]B(TZ|BP|IV)
    I: T[GLMV]{2}(E)\1
    J: (JK|AE|EN|MG){2}L
    K: N(XN|ZB|CA|FS){2}
    L: [XHDJ]R[MZVIJ]EC
    M: X?W(\sE|OS|PE){2}
    N: [VIMZJ]{3}\-\s

There are several [character classes](http://www.regular-expressions.info/charclass.html)
used throughout the puzzle, but the [literals](http://www.regular-expressions.info/characters.html)
within the character class aren't sorted alphabetically, which makes
it hard to see the similarities between say `[^MVZJ]` and `[VIMZJ]`.

So, let's sort the literals to makes things easier for us down the road. We'll
use this going forward.

    # Character Classes Sorted
    
    0: [^CFHJLMQVXZ]+
    1: [^GJPVZ]{4}(.)[EFGU]{6}\1[^IJPVZ\s]{2}
    2: [^BFPQ\s]{7}[^AGJMNVZ\s]+[^JMVZ]
    3: N[ORSUY]{5}[CEIN]{6}\s\-
    4: .A[ADL\s]{4}O[ACELV\s]+
    
    A: \sA?(SA|PE|N\s){2}
    B: ([XYZ])(P|GO|EL)\1.
    C: (LS|CA|OS)[LP\s][DO]{2}
    D: (U)(T)\2\1[ABO?]
    E: (.)\1\1\1\s
    F: (FF|BE|QU|OS){2}L
    G: ES?F?(OBZ|UCO|PTE)
    H: S[MUV]B(TZ|BP|IV)
    I: T[GLMV]{2}(E)\1
    J: (JK|AE|EN|MG){2}L
    K: N(XN|ZB|CA|FS){2}
    L: [DHJX]R[IJMVZ]EC
    M: X?W(\sE|OS|PE){2}
    N: [IJMVZ]{3}\-\s

OK, so let's setup our blank "board." I'm using dots (`.`) to denote a space
where we don't know the answer. At the start, we only have dots!

    +---+-----------------------------+
    |░░░| A B C D E F G H I J K L M N |
    +---+-----------------------------+
    | 0 | . . . . . . . . . . . . . . |
    | 1 | . . . . . . . . . . . . . . |
    | 2 | . . . . . . . . . . . . . . |
    | 3 | . . . . . . . . . . . . . . |
    | 4 | . . . . . . . . . . . . . . |
    +---+-----------------------------+

The other "cleanup" step, by looking at the board, is we see each row has a
length of **14** and each column has a height of **5**.

If we look closely, some of our regexes can be cleaned up because they would
create a string that doesn't match the length in either the row or column.

These are the rules that can be cleaned:

1. Regex **B** (`([XYZ])(P|GO|EL)\1.`) - If the char `P` was chosen in the 2nd
    [captured group](http://www.regular-expressions.info/brackets.html)
    then the length would be 4; we need a length of 5. So that can be removed.
    Regex **B** becomes `([XYZ])(GO|EL)\1.`.
2. Regex **M** (`X?W(\sE|OS|PE){2}`) - In the `X?` part of the regex, we have
    an [optional](http://www.regular-expressions.info/optional.html) modifier on
    the `X`, so the `X` can appear 0 or 1 times. If it was shown once, then the
    whole string would have a length of 6; we need a length of 5. So that can be
    removed, so regex **M** becomes `W(\sE|OS|PE){2}`.
3. Regex **A** (`\sA?(SA|PE|N\s){2}`) - Regex **A** is similar to regex **M**
    with tha `A?` part, so we can remove that so **A** becomes
    `\s(SA|PE|N\s){2}`.
4. Regex **G** (`ES?F?(OBZ|UCO|PTE)`) - The part that can be cleaned up is the
    `S?F?` section. The following strings match that pattern: `''` (empty), `S`,
    `F`, and `SF`. If we had an empty string, the whole length would be 4 (we need
    5). If we chose `SF`, the whole length would be 6. So the only valid choices
    are `S` or `F`. This means we can modify the regex a tiny bit to make it a
    bit clearer, and replace it with a character class of `[FS]`. So regex
    **G** becomes `E[FS](OBZ|UCO|PTE)`.

After our cleaning steps, our **final rules** look like:

    # Character classes sorted and all rules "cleaned"

    0: [^CFHJLMQVXZ]+
    1: [^GJPVZ]{4}(.)[EFGU]{6}\1[^IJPVZ\s]{2}
    2: [^BFPQ\s]{7}[^AGJMNVZ\s]+[^JMVZ]
    3: N[ORSUY]{5}[CEIN]{6}\s\-
    4: .A[ADL\s]{4}O[ACELV\s]+
    
    A: \s(SA|PE|N\s){2}
    B: ([XYZ])(GO|EL)\1.
    C: (LS|CA|OS)[LP\s][DO]{2}
    D: (U)(T)\2\1[ABO?]
    E: (.)\1\1\1\s
    F: (FF|BE|QU|OS){2}L
    G: E[FS](OBZ|UCO|PTE)
    H: S[MUV]B(TZ|BP|IV)
    I: T[GLMV]{2}(E)\1
    J: (JK|AE|EN|MG){2}L
    K: N(XN|ZB|CA|FS){2}
    L: [DHJX]R[IJMVZ]EC
    M: W(\sE|OS|PE){2}
    N: [IJMVZ]{3}\-\s

Everything should be setup the way we want it, so now we can start solving it!

## Step 1 - Literals and Backreferences

In the RegExes, there are a decent number of [simple literals](http://www.regular-expressions.info/characters.html).
Let's fill those in.

> Filled in:
> 
> A0, D0, G0, H0, I0, K0, M0 
> D1, L1,  
> H2,  
> A3, I3, L3, M3, N3  
> B4, E4, F4, G4, J4, L4, N4

    +---+-----------------------------+
    |░░░| A B C D E F G H I J K L M N |
    +---+-----------------------------+
    | 0 |   . . U . . E S T . N . W . |
    | 1 | . . . T . . . . . . . R . . |
    | 2 | . . . . . . . B . . . . . . |
    | 3 | N . . . . . . . E . . E   - |
    | 4 | . A . .   L O . . L . C .   |
    +---+-----------------------------+

Some of those literals are referenced by [backreferences](http://www.regular-expressions.info/backref.html).
Let's fill those in where we can.

> Filled in:
> 
> D2,  
> D3,  
> I4

    +---+-----------------------------+
    |░░░| A B C D E F G H I J K L M N |
    +---+-----------------------------+
    | 0 |   . . U . . E S T . N . W . |
    | 1 | . . . T . . . . . . . R . . |
    | 2 | . . . T . . . B . . . . . . |
    | 3 | N . . U . . . . E . . E   - |
    | 4 | . A . .   L O . E L . C .   |
    +---+-----------------------------+

There is another backreference at _L1_, which references _E1_, so let's fill
that in.

> Filled in:
> 
> E1

    +---+-----------------------------+
    |░░░| A B C D E F G H I J K L M N |
    +---+-----------------------------+
    | 0 |   . . U . . E S T . N . W . |
    | 1 | . . . T R . . . . . . R . . |
    | 2 | . . . T . . . B . . . . . . |
    | 3 | N . . U . . . . E . . E   - |
    | 4 | . A . .   L O . E L . C .   |
    +---+-----------------------------+

OK, that should be it for all the obvious answers. Next we have to start getting
a little clever. For each of our characters so far, we only used one rule to get
the letter. For example, for _D0_, we used the regex from rule **0**. Let's make
a list of where the answers were derived from, and then use that to "inspect"
the other linked rule. So again from our example, the cell _D0_ used regex **0**
to determine its state, so let's inspect rule **D** to see if that helps us with
any part of the rule.

- _A0_ - derived from regex **A** → look at regex **0**
- _D0_ - derived from regex **D** → look at regex **0**
- _G0_ - derived from regex **G** → look at regex **0**
- _H0_ - derived from regex **H** → look at regex **0**
- _I0_ - derived from regex **I** → look at regex **0**
- _K0_ - derived from regex **K** → look at regex **0**
- _M0_ - derived from regex **M** → look at regex **0**
- _D1_ - derived from regex **D** → look at regex **1**
- _E1_ - derived from regex **1** → look at regex **E**
- _L1_ - derived from regex **L** → look at regex **1**
- _D2_ - derived from regex **D** → look at regex **2**
- _H2_ - derived from regex **H** → look at regex **2**
- _A3_ - derived from regex **3** → look at regex **A**
- _D3_ - derived from regex **D** → look at regex **3**
- _I3_ - derived from regex **I** → look at regex **3**
- _L3_ - derived from regex **L** → look at regex **3**
- _M3_ - derived from regex **M** → look at regex **3**
- _N3_ - derived from regex **N** → look at regex **3**
- _B4_ - derived from regex **4** → look at regex **B**
- _E4_ - derived from regex **E** → look at regex **4**
- _F4_ - derived from regex **F** → look at regex **4**
- _G4_ - derived from regex **4** → look at regex **G**
- _J4_ - derived from regex **J** → look at regex **4**
- _L4_ - derived from regex **L** → look at regex **4**
- _N4_ - derived from regex **N** → look at regex **4**
- _I4_ - derived from regex **I** → look at regex **4**

Looking at regex **0**, that isn't going to be a big help just yet:

    0: [^CFHJLMQVXZ]+

So we can skip that. _D1_ (value of `T`) also doesn't help with regex **1**, it
merely fits in the character class `[^GJPVZ]{4}`.

_E1_ (value of `R`) is the first one that helps us. We used regex **1**, so
let's look at regex **E**:

    E: (.)\1\1\1\s

OK, so since the second char is an `R`, and it was a backreference to the first
char which was a [dot](http://www.regular-expressions.info/dot.html), that means
the first char is an `R`. Also, there are two more backreferences to that first
char (chars 3 and 4), so those are also `R`s.

So we can get _E0_, _E2_, and _E3_ to give:

> Filled in:
> 
> E0,
> E2,
> E3

    +---+-----------------------------+
    |░░░| A B C D E F G H I J K L M N |
    +---+-----------------------------+
    | 0 |   . . U R . E S T . N . W . |
    | 1 | . . . T R . . . . . . R . . |
    | 2 | . . . T R . . B . . . . . . |
    | 3 | N . . U R . . . E . . E   - |
    | 4 | . A . .   L O . E L . C .   |
    +---+-----------------------------+

Moving on, _L1_ already helped with the one backreference (for cell _E1_). _D2_
and _H2_ also don't tell us anything about regex **2**.


