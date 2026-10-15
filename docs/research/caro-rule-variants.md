# Caro and Gomoku rule variants

Research for issue #2, "Survey Caro and Gomoku rule variants". Facts were checked on 2026-10-15. This document presents options and trade-offs; the rules decision belongs to the later ticket "Decide the Caro rules for v1".

## Answer

- Every five-in-a-row variant is a combination of three independent choices: the **win rule** (freestyle five-or-more, standard exactly-five, Renju with Black-only fouls, or Caro's "blocked at both ends" rule), an **opening rule** (none, Pro, Swap, Swap2, Soosõrv-N, Taraguchi-N), and a **board size** (15x15 is the competitive norm; 19x19 and 20x20 also appear).
- The first player's advantage is proven, not just suspected: from an empty 15x15 board the first player wins both freestyle and standard (exactly-five) Gomoku ([Allis 1994](https://project.dke.maastrichtuniversity.nl/games/files/phd/SearchingForSolutions.pdf)), and Renju without opening restrictions ([Wágner and Virág 2001](https://unpaywall.org/10.3233%2FICG-2001-24104)). No solution was found for the Caro rule or for any game under a swap-style opening rule.
- Serious human play today: Gomoku world championships use exactly-five on 15x15 with Swap2 ([RIF Gomoku rules](https://www.renju.net/gomokurules/)); Renju uses the RIF rules on 15x15 with a multi-alternative opening rule (Soosõrv-8, and Taraguchi-10 at recent events per secondary sources). Computer play (Gomocup) runs freestyle, standard, Renju and Caro leagues with pre-balanced openings ([Gomocup rules](https://gomocup.org/detail-information/)).
- "Caro rule" has **no single definition**. Gomocup and the Piskvork manager use exactly five, not blocked by opponent stones at both ends, overlines never win ([Gomocup](https://gomocup.org/detail-information/), [Piskvork source](https://github.com/plastovicka/Piskvork)). Board Game Arena and English Wikipedia describe a version where overlines always win and the five must be "not blocked at either end" ([BGA](https://en.doc.boardgamearena.com/Gamehelpgomoku)). The board edge question is answered only by Piskvork's code (the edge does not count as a block). The team must pin each of these points down.
- How Vietnamese players play casually (grid paper, X and O, "chặn hai đầu" as a common house rule) is documented only by secondary, mostly unsourced or commercial sources. Treat it as folk practice, not a standard.

## 1. The building blocks

### 1.1 Win rules

| Win rule | Definition | Primary source |
|---|---|---|
| Freestyle | Five or more in a row wins, for both players. | [Allis 1994, ch. 5](https://project.dke.maastrichtuniversity.nl/games/files/phd/SearchingForSolutions.pdf); [Gomocup](https://gomocup.org/detail-information/) |
| Standard (exactly five) | Exactly five wins; six or more (an overline) does not win, for both players. | [RIF Gomoku rules, art. 1.6 and 4.3](https://www.renju.net/gomokurules/) |
| Renju | Exactly five wins for Black; White also wins with an overline. Black loses by making an overline, a double-four or a double-three (with exceptions in 9.3). | [RIF Renju rules, sec. 9](https://www.renju.net/rifrules/) |
| Caro (Gomocup "standard caro") | Exactly five, not blocked at both ends; "neither oxxxxxo nor xxxxxx is a win for x". | [Gomocup](https://gomocup.org/detail-information/) |
| Caro (BGA / Wikipedia version) | Exactly five "that is not blocked at either end", or more than five "regardless of blocked ends". | [BGA Gomoku help](https://en.doc.boardgamearena.com/Gamehelpgomoku); [Wikipedia, Gomoku](https://en.wikipedia.org/wiki/Gomoku) |

Details worth knowing:

- In RIF Gomoku, an overline simply does not end the game: "the game will continue" (art. 4.3). A full board is a draw (art. 4.5) ([RIF Gomoku rules](https://www.renju.net/gomokurules/)).
- The Renju double-three exception (9.3) is evaluated by testing whether a three can actually become a straight four without creating a foul, which makes foul detection recursive ([RIF Renju rules](https://www.renju.net/rifrules/)). This is the main implementation cost of Renju.
- Other national variants exist but were not researched in depth: Korean Omok (19x19, three-and-three banned) and Chinese "swap after the first move" freestyle on 15x15 or 19x19 ([Wikipedia, Gomoku](https://en.wikipedia.org/wiki/Gomoku), secondary). Capture variants (Ninuki-renju, Pente) change the game more fundamentally and are out of scope here.

### 1.2 The Caro rule, precisely

The Piskvork manager (the program Gomocup uses to referee AI games) is the most precise source because its win check is code. In `source/game.cpp` (repo HEAD `2c32965`, June 2025) it walks both directions from the last stone, counts the run, and voids a run of exactly five when the square past each end holds an opponent stone ([Piskvork source](https://github.com/plastovicka/Piskvork)):

```cpp
if(poc==5 && (ruleFive&8) && p1->z==3-p->z && !p1->blocked() && p2->z==3-p->z && !p2->blocked())
    poc=0;
```

Cells off the board have `z == 3` ("3=outside" in `PISKVORK.cpp`), so `3 - p->z` never matches the edge. Consequences:

- A five with opponent stones at both ends does not win.
- A five touching the board edge at one end and an opponent stone at the other **does** win: the edge is not a block.
- A five blocked at one end only wins.
- Piskvork has two Caro modes: `ruleFive` 8 is "Caro with overlines" (overlines win) and 9 is "standard Caro" (exactly five, so overlines never win). Gomocup's league uses the standard one ([Gomocup](https://gomocup.org/detail-information/)).

The BGA wording "not blocked at either end" reads literally as "both ends must be open", which is stricter than "not blocked at both ends". It is **unverified** whether BGA implements it that way or whether this is loose wording; BGA's help page does not say how the edge is treated ([BGA](https://en.doc.boardgamearena.com/Gamehelpgomoku)). Wikipedia uses the same wording and claims the rule "makes the game more balanced" and gives White "more power to defend" ([Wikipedia, Gomoku](https://en.wikipedia.org/wiki/Gomoku)); no study quantifying this was found (**unverified**).

Questions the team's rule must answer:

1. Does a five blocked at one end win? (Gomocup: yes. BGA wording: arguably no.)
2. Does an overline win? (Gomocup: never. BGA and Wikipedia: always, even blocked. Piskvork supports both.)
3. Does the board edge count as a block? (Piskvork: no. Others: not stated.)
4. On an unbounded board question 3 disappears, but the board size then becomes a server-side limit instead.

### 1.3 Opening rules

| Opening rule | Procedure | Source |
|---|---|---|
| None | Black (first player) moves anywhere. | n/a |
| Pro / Long Pro | First stone in the centre; first player's second stone at least 3 (Pro) or 4 (Long Pro) intersections from the first. | [BGA](https://en.doc.boardgamearena.com/Gamehelpgomoku) |
| Swap | First player places two black and one white stone; the second player chooses a colour. | [BGA](https://en.doc.boardgamearena.com/Gamehelpgomoku) |
| Swap2 | First player places three stones (black, white, black). The opponent either chooses a colour, or adds two stones (white, black) and the first player then chooses a colour. White then moves and play alternates. | [RIF Gomoku rules, art. 2.2 to 2.4](https://www.renju.net/gomokurules/) |
| Soosõrv-N (Renju) | One of the 26 Renju openings is placed; the other player may swap; White plays the 4th move and declares 1 to N fifth-move alternatives; the other player may swap; Black places that many non-symmetric 5th moves; White picks one and plays the 6th. | [Wikipedia, Soosõrv opening rule](https://en.wikipedia.org/wiki/Soos%C3%B5rv_opening_rule) (secondary) |
| Taraguchi-N (Renju) | After White's 4th move Black may put N alternative 5th moves anywhere on the board. Accepted by the RIF General Assembly in 2011. | [Wikipedia, Taraguchi opening rule](https://en.wikipedia.org/wiki/Taraguchi_opening_rule) (secondary; full procedure not checked) |
| RIF opening (Renju, 1996) | Tentative Black places the first three stones (one of 26 patterns), tentative White chooses colours, Black proposes two 5th-move options and White picks one. | [RIF Renju rules, sec. 12](https://www.renju.net/rifrules/) |

Opening rules and win rules are independent: BGA offers all of its opening rules with Standard, Freestyle and Caro, and makes None, Pro and Long Pro unavailable for ranked games "because of first-player advantage" ([BGA](https://en.doc.boardgamearena.com/Gamehelpgomoku)).

### 1.4 Board sizes

- RIF Gomoku and Renju: 15x15 ([RIF Gomoku rules, art. 1.2](https://www.renju.net/gomokurules/); [RIF Renju rules, sec. 2](https://www.renju.net/rifrules/)).
- Allis reports that the game was first played on 19x19 Go boards and that "a larger board increases black's advantage (Sakata and Ikawa, 1981), which resulted in the standard board size of 15x15" ([Allis 1994, ch. 5](https://project.dke.maastrichtuniversity.nl/games/files/phd/SearchingForSolutions.pdf)).
- Gomocup: freestyle on 20x20 through 2023, both 15x15 and 20x20 since 2024; standard, Renju and Caro on 15x15 ([Gomocup](https://gomocup.org/detail-information/)).
- BGA: 9x9, 13x13, 15x15, 19x19 ([BGA](https://en.doc.boardgamearena.com/Gamehelpgomoku)). PlayOK: 15x15, exactly five ([PlayOK](https://www.playok.com/en/gomoku/)).
- Vietnamese casual play: school grid paper (see section 3); no fixed size documented.

## 2. What is known about the first player's advantage

| Variant | Result | Source |
|---|---|---|
| Freestyle, 15x15, no opening rule | First player wins (weak solution by computer search; program Victoria, 11.9 CPU days, solution tree of 138,790 positions). | [Allis 1994, sec. 5.5](https://project.dke.maastrichtuniversity.nl/games/files/phd/SearchingForSolutions.pdf) |
| Standard (exactly five), 15x15, no opening rule | First player wins (15.1 CPU days, tree of 153,284 positions). | [Allis 1994, sec. 5.5](https://project.dke.maastrichtuniversity.nl/games/files/phd/SearchingForSolutions.pdf) |
| Renju, 15x15, unrestricted opening ("free renju") | First player wins (claimed; ICGA Journal 24(1), 2001, pp. 30 to 35). Allis had predicted this. | [Wágner and Virág 2001](https://unpaywall.org/10.3233%2FICG-2001-24104); [Allis 1994, sec. 6.3.11](https://project.dke.maastrichtuniversity.nl/games/files/phd/SearchingForSolutions.pdf) |
| Freestyle five, boards up to 8x8 | Draw. | Hsu, Ko, Hsueh and Wu, ICGA Journal 40(3), 2018, via [Wikipedia, m,n,k-game](https://en.wikipedia.org/wiki/M,n,k-game) (secondary) |
| Freestyle, larger or infinite boards | Not proven; believed to be a first-player win. | [Wikipedia, Gomoku](https://en.wikipedia.org/wiki/Gomoku) (uncited claim) |
| Renju with Yamaguchi or Soosõrv-N openings | Not solved. | [Wikipedia, Renju](https://en.wikipedia.org/wiki/Renju) (secondary) |
| Any variant with Swap2 | Not solved; first-player score about 52% in practice. | [Wikipedia, Gomoku](https://en.wikipedia.org/wiki/Gomoku), citing GomokuWorld (secondary) |
| Caro rule (any version) | No solution or published statistics found. **Unverified.** | n/a |

Notes:

- Freestyle is an m,n,k-game, so the strategy-stealing argument shows the second player cannot have a winning strategy ([Wikipedia, m,n,k-game](https://en.wikipedia.org/wiki/M,n,k-game)). The argument relies on extra stones never hurting, which Allis notes holds in freestyle but not in standard Gomoku, where an extra stone can turn a five into an overline ([Allis 1994, sec. 5.3.5](https://project.dke.maastrichtuniversity.nl/games/files/phd/SearchingForSolutions.pdf)). The same reasoning applies to Renju and to Caro (an extra opponent stone can void a five), so for those rules a second-player win is not ruled out by this argument (this is an inference, not a cited result).
- Historical human data: at the 1989 and 1991 Gomoku world championships, played with the Pro opening, Black won 67% of games ([Wikipedia, Gomoku](https://en.wikipedia.org/wiki/Gomoku), secondary). The RIF Gomoku rules still mention that "until year 2008 also games played by swap1 rule can be counted" for ratings ([RIF Gomoku rules, art. 12.4.1](https://www.renju.net/gomokurules/)), consistent with the reported move to Swap2 in 2009.
- These proofs are about perfect play. For casual human players the advantage shows up as a noticeable but not decisive edge; no source quantifying it for casual online play was found.

## 3. Vietnamese Caro as commonly played

Best available sources are weak. Findings:

- Vietnamese Wikipedia says the game is usually played on student exercise paper with pre-printed squares, using O and X marks, and that the winner makes an unbroken line of five not blocked by the opponent ([vi.wikipedia, Cờ caro](https://vi.wikipedia.org/wiki/C%E1%BB%9D_caro)). The article states it has no references at all.
- Consumer articles describe the same paper-and-pen setup and that the game was originally played on a 19x19 Go board ([FPT Shop](https://fptshop.com.vn/tin-tuc/thu-thuat/cach-choi-co-caro-175953), commercial, undated in text).
- A Vietnamese-developed app, Caro Online, describes the house rule as "an unbroken row of 5 pieces blocked at both ends does not count as a win" and lets players toggle it on or off ([App Store, Caro Online](https://apps.apple.com/us/app/id1523691103)). This is the clearest statement found of the rule as Vietnamese players phrase it ("chặn hai đầu"), and the toggle suggests both versions are played.
- **Unverified** (no source found): that the paper board is treated as unbounded; who traditionally moves first (X or O); how overlines are treated in casual play; whether the board edge counts as a block in casual play. No Vietnamese federation rulebook for Caro was found.

## 4. What serious online play uses today

- **Human Gomoku, competitive**: exactly five, 15x15, Swap2, as in the RIF Gomoku rules and every Gomoku world championship listed from 2009 to 2025 ([RIF Gomoku rules](https://www.renju.net/gomokurules/); [Wikipedia, Gomoku](https://en.wikipedia.org/wiki/Gomoku)). The RIF Gomoku rules were amended with rating rules valid from 1/10/2026 (art. 12).
- **Human Renju, competitive**: RIF rules on 15x15. World championship opening rule: RIF (from 1997), Yamaguchi (2009 to 2015), Soosõrv-8 (from 2017) ([Wikipedia, Renju](https://en.wikipedia.org/wiki/Renju), secondary); the 2025 championship and Team championships from 2024 are listed with Taraguchi-10 ([Wikipedia, World Championships in Renju](https://en.wikipedia.org/wiki/World_Championships_in_Renju), secondary; no RIF decision document found).
- **Computer play (Gomocup)**: freestyle (15x15 and 20x20), standard, Renju and Caro leagues; Caro since 2023. Gomocup 2025 Caro league: AlphaGomoku first, Jax second, Embryo third; Rapfi won all other leagues ([Gomocup 2025 results](https://gomocup.org/results/gomocup-result-2025); [Gomocup](https://gomocup.org/detail-information/)). Gomocup avoids opening rules altogether: games start from a set of balanced openings (12 per rule in 2025, chosen by a panel of players and researchers).
- **Casual platforms**: BGA offers Standard, Freestyle and Caro with None, Pro, Long Pro, Swap and Swap2, ranked only with swap openings ([BGA](https://en.doc.boardgamearena.com/Gamehelpgomoku)). PlayOK plays exactly five on 15x15 ([PlayOK](https://www.playok.com/en/gomoku/)). Vietnamese Caro apps offer the blocked-both-ends rule as an option ([Caro Online](https://apps.apple.com/us/app/id1523691103)).

### For the AI engine survey

The Piskvork/Gomocup protocol sends the rule as `INFO rule`, a bitmask: "1=exactly five in a row win, 2=continuous game, 4=renju, 8=caro" ([protocol spec](https://plastovicka.github.io/protocl2en.htm)). Caro was added on 2023-03-20 and the `SWAP2BOARD` command on 2022-12-20. Gomocup's "standard caro" corresponds to Piskvork's `ruleFive` 9 (exact five plus caro); the protocol page itself does not define what the caro bit means, so the meaning above comes from the Gomocup rules and the Piskvork source. Jax's 2025 entry uses one model for freestyle, standard, Renju and standard Caro on 15x15 ([Gomocup 2025 results](https://gomocup.org/results/gomocup-result-2025)). Choosing a Caro variant other than Gomocup's (for example, overlines win, or the edge counts as a block) means off-the-shelf engines will play by slightly different rules.

## 5. Trade-offs for a casual-to-competitive online game

| Option | For | Against |
|---|---|---|
| Freestyle, no opening rule | Simplest to explain and implement; matches how many people first learn the game. | Proven first-player win on 15x15; unsuitable for rated games without an opening rule or colour alternation. |
| Standard (exactly five) + Swap2, 15x15 | The international competitive standard; strong engines and published rules exist; first-player score near even in practice. | Swap2 is a confusing multi-step start for casual players and needs dedicated UI; the overline rule surprises newcomers. |
| Renju + Soosõrv-8 or Taraguchi-10 | The most studied professional rule set. | Foul detection (recursive double-three) is the hardest rule to implement and explain; opening rules are long; asymmetric rules feel unfair to casual players; little Vietnamese player base found (unverified). |
| Caro (blocked-both-ends), 15x15 | Matches the Vietnamese house rule the target audience is likely to know (based on weak sources); claimed to reduce the first-player edge; Gomocup engines support it. | No standard definition (one end vs both ends, overlines, edge); no solution or balance data; with no opening rule the first-player advantage is still unknown and probably real. |
| Caro + a swap opening | Combines local familiarity with a fairness mechanism. | No community or tournament found that uses this exact combination (unverified); Swap2 UX cost as above. |
| Large or unbounded board | Feels like grid paper. | Larger boards increase Black's advantage (Allis citing Sakata and Ikawa); hard to show on a phone; engines and Gomocup assume 15x15 or 20x20. |

Points that apply whichever rule is chosen:

- Rated games need some answer to the first-player advantage: a swap-style opening, balanced preset openings (as Gomocup does), or alternating colours across a pair of games.
- Settings can differ by mode: a simple rule for casual games and a fairer one for rated games is what BGA does with its ranked restriction.
- Whatever Caro definition is chosen should be written as test cases (oxxxxxo, xxxxxx, five against the edge, five blocked at one end) because the sources disagree.
