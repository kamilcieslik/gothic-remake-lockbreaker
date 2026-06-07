# Project context: Gothic Remake Lockbreaker

This is a small static web app hosted on GitHub Pages and available at:

https://gothic-remake-lockbreaker.com

Repository:

https://github.com/kamilcieslik/gothic-remake-lockbreaker

The app solves lockpicking puzzles from Gothic Remake.

## Core domain rules

The lock consists of plates.

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

## Solver algorithm

The solver uses BFS over the state graph with a shared priority queue.

- State is an array of plate positions.
- Goal is all zeroes.
- Edges are valid moves: each plate × left/right.
- BFS explores states with equal cost priority; cost is calculated differently depending on the selected algorithm mode:
  - **Fewer plate switches** (default): prioritizes fewer plate switches (groups of moves), then fewest total moves.
  - **Shortest moves**: prioritizes fewest total moves, then fewer plate switches as tiebreaker.
- Do not replace priority queue search with a heuristic unless there is a strong reason.
- Avoid changes that can freeze the browser for large plate counts.

Pseudocode:

solve(start):
    goal = [0, 0, ..., 0]
    queue = [(start, [])]
    visited = {start}

    while queue not empty:
        state, moves = queue.pop_front()

        if state == goal:
            return moves

        for plate in plates:
            for direction in [left, right]:
                next = applyMove(state, plate, direction)

                if next is invalid:
                    continue

                if next not visited:
                    visited.add(next)
                    queue.push((next, moves + move))

    return no solution

## Current app scope

The app is intentionally simple:

- static HTML/CSS/JS
- no backend
- no framework
- no build step
- works fully in browser
- no account
- no tracking by the app itself
- GitHub Pages deployment
- Cloudflare domain/DNS
- Cloudflare Web Analytics may be enabled externally

Prefer simple code over clever abstractions.

## Current features

- Select plate count
- Set initial plate positions
- Define interactions between plates
- Solve lock (with 15-second timeout protection)
- Two algorithm modes: Fewer plate switches (default, prioritizes fewer plate switches) and Shortest moves (prioritizes minimum move count)
- Copy solution to clipboard
- Share lock setup via URL
- Keyboard shortcut (Enter in lock name field to solve)
- Accessibility (aria-labels for screen readers)
- Reset
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
- 7-plate locks exist.
- Higher counts are not confirmed unless a user provides evidence.

Because the solver uses BFS, large plate counts can become expensive and may freeze the browser.

If supporting more than 7 plates:
- ask whether such locks are confirmed in the game,
- consider adding a hard max,
- consider showing a warning,
- ✅ **DONE**: 15-second timeout (SOLVE_TIMEOUT_MS) prevents browser freezing,
- do not blindly set max to 20 without further safeguards.

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
- Copy solution could be useful for sharing exact move sequences on Discord/Steam/Reddit.
- Interactive step tracking is questionable because most players solve a lock once and move on.
- 20 plates is not confirmed and may be risky for BFS performance.
- Changing dropdown to free numeric input may reduce UX clarity.
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
- Test manually after changes:
  - 1 plate
  - 5 plates
  - 7 plates
  - already solved state
  - no-solution state
  - share link load
  - mobile viewport
  - desktop viewport
  - timeout scenario (try unsolvable puzzle)