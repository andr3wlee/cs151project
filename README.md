# Mastermind Playground — CS 151 Final Project

Mastermind Playground is a single-page browser game for the CS 151 Logic Programming term project. The computer chooses a hidden four-color code, and the player has ten guesses to crack it. Game logic is written in Epilog facts and rules; JavaScript handles the UI and queries Epilog for answers.

## Files in This Repository

- `index.html` — UI, styling, JavaScript bridge code, and embedded Epilog rules/data
- `epilog.js` — EpilogJS engine loaded by `index.html`

Both files must be in the same directory for the game to run.

## How to Run

1. Download or clone the repository.
2. Open `index.html` in a browser (double-click or File → Open).

No build step or package install is required. If a browser has trouble with local files, run a simple server from the project folder:

```bash
python3 -m http.server 8000
```

Then open `http://localhost:8000/index.html`.

## How to Play

1. Click a slot in the highlighted round, then pick a color from the tray.
2. Fill all four slots with different colors and click **Submit Guess**.
3. Read the feedback pegs: black = exact match, white = right color wrong position, empty = not in the secret code.
4. Win by matching all four positions within ten rounds.

## Game Rules

- 8 colors, 4 positions, 10 guesses maximum
- No repeated colors in the secret code or in a submitted guess
- Feedback is ordered per slot (not classic unordered Mastermind feedback)

## Logic Programming Design

The Epilog facts and rules are embedded in `index.html` in two string constants:

- `BASE_FACTS` defines colors, positions, rounds, and round order.
- `RULES` defines helper relations, legal code generation, feedback, legality checks, illegal-state constraints, and win/terminal conditions.

The main predicates are:

- `generatedcode(C1,C2,C3,C4)` — enumerates all legal no-repeat secret codes.
- `secret(P,C)` — stores the generated hidden code for the current game.
- `guess(R,P,C)` — stores submitted guesses.
- `legalguess(R)` — validates that a round has four colors from the domain with no repeats.
- `exact(R,P)` — true when the guess matches the secret at the same position.
- `present(R,P)` — true when the guessed color appears elsewhere in the secret.
- `absent(R,P)` — true when the guessed color is not in the secret.
- `win(R)` — true when all four positions are exact.
- `terminal(R)` — true when the player has won or reached round 10.

JavaScript calls the Epilog engine rather than reimplementing these rules:

- `compfinds(...)` enumerates legal secret codes.
- `comptransform(...)` converts temporary `pending(P,C)` facts into permanent `guess(R,P,C)` facts.
- `compfindp(...)` asks yes/no questions such as whether a guess is legal or whether a feedback predicate holds.
