# Gothic Remake Lockpick Solver

Free online lockpicking puzzle solver for Gothic Remake.

🔓 Launch App:  
https://gothic-remake-lockbreaker.com

---

## Screenshot

<div align="center">

![Screenshot Mobile](demo-mobile.png)
![Screenshot Desktop](demo-desktop.png)

</div>

---

## Features

- Supports 4-7 plate locks
- Four solving modes:
  - Fewer plate switches
  - Fewer plate switches (fast)
  - Shortest moves
  - Shortest moves (fast)
- Mobile friendly
- Shareable lock URLs
- Copy solution to clipboard
- Runs in your browser using a Web Worker, so longer solves do not freeze the UI
- No registration required

---

## How to use

1. Choose the number of plates: **4**, **5**, **6** or **7**
2. Set plate positions
3. Define plate interactions
4. Choose an algorithm mode if needed
5. Click **Solve Lock**

---

## Algorithm modes

### Fewer plate switches

Default mode.  
Prioritizes reducing how often you switch between plates, then minimizes total moves. This is usually easier to follow in-game, but it may use more individual moves.

### Fewer plate switches (fast)

A quicker version focused on reducing plate switches. It is useful on slower devices or when the full mode takes too long, but it is less optimized than the full version.

### Shortest moves

Guarantees the lowest number of individual moves and prefers fewer plate switches when several shortest solutions exist.

### Shortest moves (fast)

Fastest strict shortest-move solver. It guarantees the lowest number of individual moves, but may switch between plates more often.

---

## Notes

- The solver supports Gothic Remake lockpicking puzzles with 4 to 7 plates.
- The app runs fully in the browser.
- Solving is done in a Web Worker to avoid freezing the UI.
- If solving times out, try one of the fast modes or check the entered lock setup.
- A timeout does not always mean the lock is impossible.

---

## Keywords

- Gothic Remake lockpick solver
- Gothic Remake lockpicking puzzle solver
- Gothic Remake lock solution
- Gothic Remake lock helper
- Gothic Remake lockpicking guide
- Gothic Remake lock breaker
- Gothic Remake lock puzzle solver

---

## Contributing

Found a bug or have an improvement?

Feel free to open an issue or submit a pull request. This is a small side project, so active maintenance is not guaranteed.

---

## Support

If this project helped you, you can support my open-source work through GitHub Sponsors.

Sponsorship is optional and does not unlock paid features or guaranteed support.

---

## Disclaimer

Gothic Remake Lockbreaker is a fan-made project and is not affiliated with, endorsed by, or sponsored by Alkimia Interactive, THQ Nordic, or the Gothic Remake development team.

"Gothic" and "Gothic Remake" are trademarks of their respective owners.
