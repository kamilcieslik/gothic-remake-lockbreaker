# Project context: Gothic Remake Lockbreaker

This is a small static web app hosted on GitHub Pages and available at:

https://gothic-remake-lockbreaker.com

Repository:

https://github.com/kamilcieslik/gothic-remake-lockbreaker

The app solves lockpicking puzzles from Gothic Remake.

## Core domain rules

The lock consists of plates.

- The app currently supports 4, 5, 6 and 7 plate locks.
- Plate 1 is the bottom plate.
- Plate N is the top plate.
- UI for plate positions should display plates top-to-bottom: Plate N, ..., Plate 1.
- Internal model still uses Plate 1 as the bottom plate.
- Each plate has a position from -3 to +3.
- 0 means centered.
- The lock is solved when all plates are at 0.
- Moving a selected plate can also move other plates.
- An interaction can be:
  - no effect
  - same direction
  - opposite direction
- A move is atomic:
  - either all affected plates move successfully,
  - or if any affected plate would go outside [-3, +3], the whole move is invalid and should not change state.

## Movement model

Currently:

- Left move = +1
- Right move = -1

When a plate is moved:

1. Move the selected plate by delta.
2. For every affected plate:
   - Same = same delta
   - Opposite = -delta
3. If any resulting position is outside [-3, +3], reject the move.

## Solver algorithms

The app has four algorithm modes.

### Fewer plate switches

Default mode.

- Uses priority-based graph search.
- Prioritizes fewer plate switches / grouped move lines.
- Uses total move count as the secondary criterion.
- Usually produces solutions that are easier to follow in-game.
- May use more individual moves than the shortest-move modes.
- May take longer than fast modes.

Cost order:

1. fewer groups / plate switches
2. fewer individual moves

### Fewer plate switches (fast)

Fast fallback for easier-to-follow solutions.

- Uses a simpler grouped-search approach.
- Focuses on reducing plate switches.
- Usually easier to follow than strict shortest-move output.
- Less optimized than the full Fewer plate switches mode.
- Useful when the full mode takes too long on slower devices.

### Shortest moves

Optimized shortest-move mode.

- Uses priority-based graph search.
- Guarantees the lowest number of individual moves.
- Uses fewer plate switches as a tie-breaker when several shortest solutions exist.
- May take longer than fast modes.

Cost order:

1. fewer individual moves
2. fewer groups / plate switches

### Shortest moves (fast)

Fast strict shortest-move fallback.

- Uses classic BFS with parent pointers.
- Guarantees the lowest number of individual moves.
- Does not optimize plate switches as a tie-breaker.
- May switch between plates more often.
- Useful when solving takes too long or times out.

## Solver implementation notes

- The solver runs in a Web Worker to avoid freezing the UI during long computations.
- The app shows a loading overlay with a minimum display time to avoid flicker on easy locks.
- The timeout is currently 30 seconds.
- Timeout should not freeze the browser.
- Timeout does not necessarily mean the puzzle is impossible. It may mean:
  - the selected mode is too expensive,
  - the device/browser is too slow,
  - the entered lock setup is incorrect,
  - or the puzzle is impossible.
- Timeout/error messages should suggest trying one of the fast modes.
- Do not replace priority queue search with a heuristic unless there is a strong reason.
- Avoid changes that can freeze the browser for large or complex locks.

Simplified shortest-move BFS pseudocode:

```text
solveFastShortestMoves(start):
    goal = [0, 0, ..., 0]
    queue = [start]
    visited = {start}
    parents = {}

    while queue not empty:
        state = queue.pop_front()

        if state == goal:
            return rebuildPath(parents)

        for plate in plates:
            for direction in [left, right]:
                next = applyMove(state, plate, direction)

                if next is invalid:
                    continue

                if next not visited:
                    visited.add(next)
                    parents[next] = (state, move)
                    queue.push(next)

    return no solution
```

Simplified priority-search idea:

```text
solveWithPriority(start, mode):
    priorityQueue = [startNode]
    bestCost = {start + lastMove: cost}

    while priorityQueue not empty:
        node = priorityQueue.pop_best_by_mode()

        if node.state == goal:
            return rebuildPath(node)

        for each valid move:
            nextCost = calculateCost(node, move, mode)
            nextKey = nextState + lastMove

            if nextKey has no better or equal cost:
                save parent
                push next node

    return no solution
```

## Current app scope

The app is intentionally simple:

- static HTML/CSS/JS
- no backend
- no framework
- no build step
- works fully in browser
- works asynchronously via Web Worker to prevent UI freezing
- loading overlay with minimum display time for visual feedback
- no account
- no tracking by the app itself
- GitHub Pages deployment
- Cloudflare domain/DNS
- Cloudflare Web Analytics may be enabled externally

Prefer simple code over clever abstractions.

## Current features

- Choose plate count using 4 / 5 / 6 / 7 buttons
- Set initial plate positions
- Define interactions between plates
- Solve lock with 30-second timeout protection and async Web Worker
- Four algorithm modes:
  - Fewer plate switches
  - Fewer plate switches (fast)
  - Shortest moves
  - Shortest moves (fast)
- Copy solution to clipboard
- Share lock setup via URL
- Keyboard shortcut: Enter in lock name field to solve
- Accessibility: aria-labels for screen readers
- Reset lock
  - clears lock name, positions, interactions, result, share info and URL
  - preserves the currently selected plate count
  - preserves the selected algorithm mode
- SEO metadata
- FAQ section
- robots.txt and sitemap.xml
- Link to source code on GitHub

## UX principles

This is a tool for players, not developers.

Prioritize:

- fast input
- mobile usability
- no clutter
- clear terminology
- minimal maintenance burden
- no unnecessary "support project" / "star GitHub" messaging
- avoid features that imply heavy maintenance obligations

Do not add intrusive popups, ads, login, donation prompts, tracking prompts, or complex onboarding.

If optional support links are ever added, keep them subtle, neutral and author-focused, e.g. "Support my work", not as a primary CTA and not as a promise of support or maintenance.

## Wording

Use English in the app.

Preferred project name:

Gothic Remake Lockbreaker

SEO wording can include:

- Gothic Remake Lockpick Solver
- Gothic Remake lockpicking puzzle solver
- Gothic Remake lock solution
- Gothic Remake lock helper

## GitHub / open source posture

This is a small side project.

Contributions are welcome, but active maintenance is not guaranteed.

Avoid wording that creates strong support expectations, such as:

- Report bug as a primary CTA
- Support the project
- Star this repo

A small “View source” link is fine.

## Important concerns

Be careful with increasing max plate count.

Known:
- 4, 5, 6 and 7 plate locks are the current supported range.
- 7-plate locks exist.
- Higher counts are not confirmed unless a user provides evidence.
- 1-3 plate locks are currently not supported in the UI because they are not expected in the game.

Because some solver modes use priority-based graph search and BFS-style traversal, large plate counts can become expensive and may consume significant resources.

If supporting more than 7 plates:
- ask whether such locks are confirmed in the game,
- consider adding a hard max,
- consider showing a warning,
- keep Web Worker protection,
- keep timeout protection,
- do not blindly set max to 20 without further safeguards.

Already implemented safeguards:
- 30-second timeout (`SOLVE_TIMEOUT_MS`)
- Web Worker to prevent browser freezing
- loading overlay with minimum display time
- fast algorithm modes for slower devices or expensive locks

## PR review guidance

For external PRs:

Good additions:
- small UX improvements,
- bug fixes,
- confirmed game mechanic updates,
- better mobile layout,
- SEO corrections,
- clearer wording,
- safe performance improvements.

Be cautious with:
- large max plate count increases,
- complex architecture,
- framework migration,
- backend features,
- AI/OCR features,
- analytics inside the app,
- features that add maintenance burden,
- broad redesigns,
- features not grounded in real player needs.

## Known discussion

Someone proposed:
- numeric input for 1-20 plates,
- interactive step tracking,
- copy solution,
- desktop layout changes.

Assessment:
- Copy solution is useful and has been added.
- Interactive step tracking is questionable because most players solve a lock once and move on.
- 20 plates is not confirmed and may be risky for solver performance.
- Changing plate count to free numeric input may reduce UX clarity.
- Current UX intentionally uses explicit 4 / 5 / 6 / 7 buttons instead of a dropdown or free numeric input.
- Avoid mixing unrelated changes in one PR.

## Suggested README contributing note

Use something like:

Found a bug or have an improvement?

Feel free to open an issue or submit a pull request. This is a small side project, so active maintenance is not guaranteed.

## Disclaimer

Keep this disclaimer visible in README and/or page:

Gothic Remake Lockbreaker is a fan-made project and is not affiliated with, endorsed by, or sponsored by Alkimia Interactive, THQ Nordic, or the Gothic Remake development team.

"Gothic" and "Gothic Remake" are trademarks of their respective owners.

## Code style

- Keep everything simple and readable.
- Use plain JavaScript.
- Avoid dependencies.
- Avoid build tooling unless clearly justified.
- Keep functions small when possible.
- Preserve existing behavior unless intentionally changing it.
- Preserve accessibility: use aria-labels for interactive elements.
- Preserve keyboard navigation: test Enter and Tab keys in all new interactive features.
- Keep Web Worker solving responsive.
- Avoid long synchronous solver work on the main thread.

Manual testing checklist:
- 4 plates
- 5 plates
- 6 plates
- 7 plates
- switching between all algorithm modes
- already solved state
- no-solution state
- timeout scenario
- fast modes after timeout
- reset lock preserves selected plate count
- reset lock clears positions/interactions/result/share URL
- share link load
- invalid old share link with 1-3 plates
- mobile viewport
- desktop viewport
- keyboard navigation with Enter and Tab
