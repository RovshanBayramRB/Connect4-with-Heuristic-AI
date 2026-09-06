# Connect 4 with Heuristic AI

A Connect 4 game built with object-oriented Python and a Tkinter interface, played against an AI opponent that evaluates board positions using a weighted threat-counting heuristic.

---

## The game

Standard Connect 4 on a 6×7 grid. The human plays first as `X`; the AI answers immediately as `O`. Four in a row wins — horizontally, vertically, or on either diagonal — and the winning four turn red on the board.

**Controls:** seven "Column" buttons drop a piece into the corresponding column, plus **New game** and **Quit**. A status label tracks the turn number and whose move it is.

---

## How the AI works

The AI is a **greedy one-ply search**: it considers every legal move, scores the position each one produces, and takes the best. In `TakingBestMove()`:

1. Copy the board and drop a piece into the candidate column
2. Return `100000` immediately if that move wins outright
3. Otherwise evaluate the resulting position with `calculateScore()`
4. Keep the highest-scoring column

### The evaluation function

`calculateScore()` counts threats for both sides and returns the difference:

```
score = (own twos × 10) + (own threes × 1000)
      − (opponent twos × 10) − (opponent threes × 1000)
```

`checkThree()` counts three-in-a-row patterns with an **open fourth cell** — a live threat rather than a dead one. `checkTwo()` does the same for pairs backed by two open cells. Both scan horizontally in each direction, vertically, and along both diagonals.

Two design choices do the real work here:

**Threes are weighted 100× twos.** The AI will always take a winning setup or deny one before it thinks about building pairs. Pair-building only breaks ties among positions where no threes are in play.

**Opponent threats are subtracted with identical weight.** Blocking and attacking are therefore valued the same, and the AI defends as a natural consequence of maximizing the differential — there is no special-cased "if the opponent is about to win, block" rule anywhere in the code. It falls out of the arithmetic.

### The limits of one ply

Evaluating only the position immediately after its own move means the AI cannot see the reply. It will happily play a move that hands the opponent a win on the next turn, and it cannot construct a double threat — the standard Connect 4 winning pattern — because setting one up requires reasoning two moves ahead. It plays a solid blocking game and loses to anything resembling a trap.

Adding depth-limited minimax on top of the existing `calculateScore()` is the natural next step; see *Next steps*.

---

Requires Python 3 with Tkinter (bundled on Windows and macOS; `apt install python3-tk` on Debian/Ubuntu). Needs a display — it will not run headless.

---

## Code structure

Single file, two classes:

| Class | Responsibility |
|---|---|
| `Board` | Grid state, legal-move detection, win checking, position evaluation, canvas drawing |
| `Connect4` | Turn management, column button handlers, status label, game-over logic |

Module-level code builds the window, draws the grid lines, and wires up the buttons.

Key `Board` methods:

| Method | Purpose |
|---|---|
| `CheckValidLocation(grid, col)` | Is the column playable? |
| `getNextOpenRow(grid, col)` | Lowest empty row in a column |
| `findPlayableLocations(grid)` | All legal columns |
| `checkTwo` / `checkThree` | Threat counting for evaluation |
| `calculateScore(grid, piece)` | Differential position score |
| `TakingBestMove(grid, piece)` | Move selection |
| `winCheck(grid, piece)` | Four-in-a-row test, used during search |
| `check_victory()` | Four-in-a-row test that also highlights the winning line |

---

## Running it

```bash
git clone https://github.com/RovshanBayramRB/Connect4-with-Heuristic-AI.git
cd Connect4-with-Heuristic-AI
pip install numpy
python connect4_ai.py
```

---

## Repository structure

```
.
├── connect4_ai.py   # Board + Connect4 classes, heuristic AI, Tkinter GUI
└── README.md
```
