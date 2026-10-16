# Online Caro

An online game of Caro, the Vietnamese five-in-a-row game, played in real time against other people or AI.

## Language

### Playing

**Board**:
The 15x15 grid of cells a game is played on.
_Avoid_: Grid, goban

**Cell**:
One square of the board, either empty or holding one mark.
_Avoid_: Square, intersection, point

**Mark**:
An X or an O placed in a cell. Each player places one kind.
_Avoid_: Stone, piece, token, black/white

**Move**:
A player placing one mark in an empty cell.
_Avoid_: Ply

**Five**:
An unbroken horizontal, vertical or diagonal line of five or more of one player's marks, which wins the game whether or not its ends are blocked.
_Avoid_: Overline, row, winning line

**Rules**:
How a game is played, identical in every game.
_Avoid_: Variant, mode

**Game settings**:
The choices fixed for one game before it starts, such as the time control and who plays X.
_Avoid_: Options, config, rules

**Rock-paper-scissors**:
The hand game (Vietnamese: oẳn tù tì) played before a game, whose winner chooses X or O.
_Avoid_: RPS, coin toss, janken

**Time bank**:
The time a player has left for the whole game. It runs only on their turn, is never refilled, and running out loses the game.
_Avoid_: Clock, timer, stopwatch

**Time control**:
The starting size of each player's time bank, one of the game settings.
_Avoid_: Time limit, time mode

### Ratings and AI

**Coach**:
The AI that advises a player about their own play: hints while playing and a review of key mistakes afterwards. It is never available during a rated game.
_Avoid_: Supporter, assistant, helper

**Rated game**:
A game whose result changes the players' ratings.
_Avoid_: Ranked game
