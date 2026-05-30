# Ciphra

An offline, single-player code-breaking game (German UI) — guess the hidden
colour code in a limited number of tries. Installable PWA, no backend, no
network calls, no analytics.

Built on the [Ada](https://www.npmjs.com/package/ada-ui) sci-fi CSS framework
(green theme), the same vanilla-TS + SCSS + service-worker setup as its sibling
apps (Vicy, Trica).

## How to play

The app picks a secret code. You guess a row of colours and get feedback pegs:

- **Filled dot** — right colour **and** right position
- **Outline dot** — right colour, **wrong** position

Fewer feedback dots than the code length means some colours aren't in the code
at all. The feedback never says _which_ peg — that's the puzzle.

Configurable in Settings: number of colours (6–10, colourblind-safe
Okabe–Ito palette), code length, number of guesses, and whether colours may
repeat (default: yes). Changing board settings starts a fresh game.

## Development

```sh
npm install
npm run dev        # tsc + sass watchers + live-server
npm run dev:no-sw  # same, but disables the service worker (avoids stale caches)
npm test           # type-check + Jest (game logic + persistence)
npm run build      # compile TS -> JS and SCSS -> style.min.css
```

The game *rules* live in `game.ts` (pure, fully unit-tested); `storage.ts`
handles versioned localStorage persistence; `ui.ts` is the DOM/render/input
shell. The compiled `*.js` and `style.min.css` are gitignored — CI builds them.

## Architecture

| File | Responsibility |
| --- | --- |
| `game.ts` | Pure logic: secret generation, two-pass scoring (duplicate-safe), win/lose, config validation. No DOM. |
| `storage.ts` | Versioned (de)serialisation for localStorage; rejects corrupt/old state. |
| `ui.ts` | Screens, board/palette rendering, input, navigation, persistence wiring. |
| `style.scss` | Imports Ada (green theme) + app styles. |
| `sw.js` | Cache-first service worker; CI injects the commit SHA as the cache name. |

## Deploy

Pushing to `master` triggers `.github/workflows/deploy.yml`: it injects the
commit SHA into `sw.js` (cache-busting), runs `npm run build`, and publishes to
GitHub Pages. All asset paths are relative, so it works on the
`/<repo>/` Pages subpath.

## Credits & licences

- UI framework: [ada-ui](https://www.npmjs.com/package/ada-ui)
- Font: [Open Sans](https://fonts.google.com/specimen/Open+Sans) (Apache-2.0), self-hosted
- Peg palette: [Okabe–Ito colourblind-safe colours](https://jfly.uni-koeln.de/color/)
- Icons and artwork are original.

The board game that inspired the mechanic is a registered trademark of its
owner; this is an independent, non-commercial implementation and uses no
trademarked name or artwork.
