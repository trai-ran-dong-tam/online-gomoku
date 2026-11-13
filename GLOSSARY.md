# Online Caro

An online game of Caro, the Vietnamese five-in-a-row game, played in real time against other people or AI.

## Language

### Playing

**Game**:
One game of Caro between two players, ending in a win for one of them or a draw.
_Avoid_: Match, round

**Player**:
Whoever holds a seat in a game: a person or a bot.
_Avoid_: Participant

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

**Resign**:
A player ending a game as a loss for themselves.
_Avoid_: Forfeit, surrender, give up

**Draw**:
A game ending with no winner, either agreed by both players or forced by a full board with no Five.
_Avoid_: Tie

**Draw offer**:
A player's proposal to end the game as a draw, which their opponent accepts or declines.
_Avoid_: Draw request

**Abandonment**:
The end of a game when one player has been disconnected so long that their opponent takes the win.
_Avoid_: Forfeit, disconnect loss

**Rules**:
How a game is played, identical in every game.
_Avoid_: Variant, mode

**Game settings**:
The choices fixed for one game before it starts, such as the time control and who plays X.
_Avoid_: Options, config, rules

**Rock-paper-scissors**:
The hand game (Vietnamese: oẳn tù tì) played before a game between two people, whose winner chooses X or O.
_Avoid_: RPS, coin toss, janken

**Time bank**:
The time a person has left for the whole game, in a game between two people. It runs only on their turn, is never refilled, and running out loses the game.
_Avoid_: Clock, timer, stopwatch

**Time control**:
The starting size of each player's time bank, one of the game settings.
_Avoid_: Time limit, time mode

**Thinking time**:
How long a person took on each move in a bot game, recorded and shown afterwards but never a way to lose.
_Avoid_: Time bank, clock

### Rooms and modes

**Room**:
Where games are played: two seats and any spectators, holding one game at a time and then the next.
_Avoid_: Table, lobby, match

**Seat**:
One of a room's two places for a player. A player keeps their seat from game to game, while X and O are settled again for each game.
_Avoid_: Side, slot

**Spectator**:
A person in a room without a seat, watching its games live.
_Avoid_: Viewer, observer, watcher

**Rematch**:
The next game in the same room between the same two players, started when one offers it and the other accepts.
_Avoid_: Replay, revenge, new game

**Quick message**:
One of a fixed set of short messages the two players can send each other.
_Avoid_: Chat, emote, sticker

**Live games**:
The public list of quick play games in progress, where anyone can pick a game to watch.
_Avoid_: Lobby, game list, TV

**Quick play**:
The mode where the site pairs a person with someone else from the queue.
_Avoid_: Ranked, random game, matchmaking

**Queue**:
The people waiting to be paired for quick play.
_Avoid_: Lobby, pool, seek

**Pairing**:
Two people from the queue proposed as opponents, who must both accept before their game starts.
_Avoid_: Match

**Private room**:
A room reached only through its link and never listed in Live games.
_Avoid_: Custom game, invite

**Host**:
The player in a private room who, while no game is in progress, can change its game settings and seat password and remove the other player. When the host leaves, the role passes to whoever is still seated.
_Avoid_: Owner, admin, creator

**Seat password**:
An optional password on a private room that a person needs to take a seat, but not to watch.
_Avoid_: Room password, PIN

**Bot game**:
A game in which one seat is held by a bot.
_Avoid_: vs AI, practice, single player

### Ratings and AI

**Bot**:
An AI that holds a seat and plays a game, unlike the Coach, which only advises.
_Avoid_: AI opponent, computer, engine

**Coach**:
The AI that advises a player about their own play: hints while playing and a review of key mistakes afterwards. It is never available during a rated game.
_Avoid_: Supporter, assistant, helper

**Rated game**:
A game whose result changes the players' ratings.
_Avoid_: Ranked game
