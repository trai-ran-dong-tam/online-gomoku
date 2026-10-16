# Freestyle win rule with no opening rule

v1 plays freestyle: any unbroken line of five or more marks wins, even when the opponent has blocked both ends, and X moves first anywhere on the 15x15 board with no opening rule. This matches how the team plays Caro and keeps a single win rule for every game, bot and Coach feature. It knowingly accepts that freestyle on 15x15 is a proven first-player win (Allis 1994), and relies on who plays X being settled by rock-paper-scissors to keep things fair across games.

## Considered Options

- **The "chặn hai đầu" blocking rule** (Gomocup's Caro, a common Vietnamese house rule): rejected because its definition varies between sources (overlines, the board edge), and Rapfi, the strongest open engine, plays freestyle but not Caro. It is not offered as a game setting either, so bots and the Coach handle one win rule.
- **Swap2 or Pro opening**: Swap2 is a confusing multi-step start for casual players. Under Pro, the first player still won about 67% of games at the 1989 and 1991 world championships.

## Consequences

- X's win rate in real games is the signal for revisiting the opening rule.
