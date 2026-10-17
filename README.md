# Letter Hunt

A single-file word search game that runs in the browser. Pick a theme and a difficulty level, then drag across the letter grid to find the hidden words.

No build step, no dependencies, no backend. Everything lives in `wordsearch.html`.

## Features

- **10 themes:** Nations, Cities, Books, Science, Literature, Space, Food, Animals, Instruments, Mythology
- **3 difficulty levels:** Easy, Medium, Hard (grid size, word count, and allowed directions change per level)
- **Random puzzles:** each game picks a different set of words from the chosen theme and places them at random
- **Drag to select:** works with mouse, touch, and pen through pointer events
- **Either direction:** a word is accepted whether you drag it forwards or backwards
- **Color-coded results:** every found word gets its own highlight color and is crossed off the list
- **Timer and best times:** best time per theme and level is stored in the browser
- **Hint button:** flashes the first letter of a word you haven't found yet
- **Responsive and themed:** adapts to phones and desktops, follows the system light or dark mode, and respects reduced-motion settings

## Getting started

1. Clone the repository:
   ```bash
   git clone <your-repo-url>
   cd <your-repo-folder>
   ```
2. Open `wordsearch.html` in any modern browser.

To serve it locally instead:

```bash
python3 -m http.server 8000
# then visit http://localhost:8000/wordsearch.html
```

To host it on GitHub Pages, rename the file to `index.html` (or link to it directly) and enable Pages for the repository.

## How to play

1. Choose a theme and a level on the start screen, then press **Start hunting**.
2. Press and drag from the first letter of a word to its last letter. The selection snaps to a straight line (horizontal, vertical, or diagonal).
3. Release to check the word. If it matches, it stays highlighted and is crossed off the list.
4. Find every word to finish. Use **Hint** if you're stuck, **New puzzle** for a fresh grid in the same theme, or **Themes** to go back to the start screen.

### Levels

| Level  | Grid  | Words | Directions                                  |
|--------|-------|-------|---------------------------------------------|
| Easy   | 10×10 | 6     | Right, down                                 |
| Medium | 12×12 | 8     | Right, down, and diagonals                  |
| Hard   | 14×14 | 10    | All 8 directions, including backwards       |

## Project structure

```
.
├── wordsearch.html   # markup, styles, and game logic in one file
└── README.md
```

Inside `wordsearch.html`:

| Section              | Purpose                                                            |
|----------------------|--------------------------------------------------------------------|
| `<style>`            | Design tokens (CSS variables), layout, light and dark themes       |
| `CATS`               | Theme definitions: icon, display name, and the word pool           |
| `LV`                 | Level definitions: grid size, word count, allowed directions       |
| `build()`            | Picks words, places them in the grid, and fills the gaps           |
| `path()` / pointer handlers | Turns a drag into a straight line of cells                  |
| `check()`            | Matches the selection (forwards or reversed) against unfound words |
| `win()`              | Stops the timer, saves the best time, and shows the result dialog  |

## Customizing

### Add or edit a theme

Add an entry to the `CATS` object. Words are separated by spaces.

```js
sports: { i: '🏏', n: 'Sports', w: 'CRICKET HOCKEY TENNIS KABADDI POLO SQUASH RUGBY' }
```

| Key | Meaning                              |
|-----|--------------------------------------|
| `i` | Icon (emoji) shown on the theme card |
| `n` | Display name                         |
| `w` | Space-separated word pool            |

Word rules:

- Use uppercase letters A–Z only. No spaces, hyphens, accents, or digits.
- A word longer than the grid size of the selected level is skipped.
- Provide more words than the largest level uses (10) so each puzzle varies.

The theme list on the start screen is generated from `CATS`, so no HTML changes are needed.

### Change a level

Edit the `LV` array. Each entry has:

- `s`: grid size (the grid is `s × s`)
- `n`: number of words to place
- `d`: allowed directions as `[rowStep, colStep]` pairs
- `t`: description shown under the level selector

### Change the look

Colors are CSS variables on `:root` (`--bg`, `--ink`, `--card`, `--acc`, and so on). The dark palette is defined once for the system setting and once for `data-theme="dark"`, so keep the two in sync. The font is Bricolage Grotesque from Google Fonts, with system fonts as the fallback if it can't load.

## Technical notes

- **Word placement:** words are sorted longest first. For each word the game tries up to 400 random positions and directions. A placement is valid if every cell is empty or already holds the same letter, so words can cross. Remaining cells are filled with random letters.
- **Fewer words than expected:** in rare cases a word can't be placed after 400 attempts and is dropped. The word list and the "found" counter always reflect only the words that were actually placed.
- **Selection:** a drag is tracked with pointer capture and `document.elementFromPoint`, which keeps it working on touch screens. If the pointer isn't on a valid line from the start cell, only the start cell is highlighted.
- **Storage:** best times are saved in `localStorage` under the key `lh-<theme>-<levelIndex>` (for example `lh-space-1`). All storage access is wrapped in `try/catch`, so the game still works when storage is blocked.

## Browser support

Any current version of Chrome, Edge, Firefox, or Safari. The layout uses CSS container query units (`cqw`), which need a browser released since 2023.

## Ideas for future work

- Custom word lists entered by the player
- Daily puzzle with a seeded generator
- Sound effects and a streak counter
- More themes (sports, movies, technology)
- Keyboard-only play for accessibility

## License

Add a license of your choice, for example MIT, as a `LICENSE` file.
