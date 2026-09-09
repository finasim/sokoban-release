# Play Store listing — draft

Copy for the Play Console. Review before publishing; nothing here should be
submitted unread.

- **Package:** `com.apps.finstudio.sokoban`
- **Version:** 1.0.0 (versionCode 1)
- **Category:** Games → Puzzle
- **Content rating:** intended for Everyone (complete the questionnaire in the
  console; the app has no violence, no user content, no purchases)
- **Ads:** none in 1.0.0. Advertising is planned for a later release — see
  **When ads are added** at the end of this file. The "Contains ads" label and
  the ads answer in the content-rating questionnaire both have to change in the
  same release that ships them, not before.
- **In-app purchases:** none

## App name (30 char max)

```
Sokoban
```

## Short description (80 char max)

```
Classic crate-pushing puzzles. 100 levels, plays offline.
```

## Full description (4000 char max)

```
Sokoban is the classic warehouse puzzle: push every crate onto a goal tile.

You can only push, never pull, and only one crate at a time — so a single
careless shove can wedge a crate into a corner and cost you the level. Undo is
always one tap away.

100 levels across five packs
Starter, Depot, Warehouse, Foundry and Labyrinth — each pack strictly harder
than the last, and each level checked to be solvable before it shipped. The
first is a single push. The last will take you a while.

Play the way you like
Swipe anywhere on the board, or use the on-screen pad. One long swipe walks
several squares.

Track your best
Moves and pushes are counted separately, and your best of each is saved per
level — because the shortest walk and the fewest pushes are rarely the same
solution.

Made to stay out of your way
No in-app purchases and no accounts. Everything you do stays on your device,
and the whole game plays offline.

Light and dark themes, and a stuck-crate warning that offers an undo rather than
ending your game.
```

## Data safety form

For 1.0.0, answer **"No data collected"** and **"No data shared"**. See
`PRIVACY.md` for the reasoning; this build has no `INTERNET` permission, which
is verifiable from the manifest.

Both answers stop being true the moment an ad SDK is added — do not carry them
forward into that release. See **When ads are added** below.

Privacy policy URL — paste this into the console:

```
https://finasim.github.io/sokoban-release/privacypolicy.html
```

It is live and publicly reachable. `PRIVACY.md` in this repo is the same policy
in short form; `docs/privacypolicy.html` is the hosted copy. Keep all three in
step with what `Settings.toJson()` in `lib/data/progress_store.dart` actually
persists — the Data safety answers above have to stay true of the code.

## Graphic assets

| Asset | Requirement | Status |
| --- | --- | --- |
| App icon | 512×512 PNG, 32-bit | `store_icon_512.png` ✅ |
| Phone screenshots | 2–8, 320–3840px, longest side ≤ 2× shortest, 24-bit PNG (no alpha) | `store/screenshots/` — 4 at 1080×2160 ✅ |
| Feature graphic | 1024×500 24-bit PNG/JPG, no alpha | `store/feature_graphic.png` ✅ |

The feature graphic is drawn by `tools/make_feature_graphic.py` from the board
palette in `lib/app/theme.dart`, so the banner, the icon and the screenshots
share one set of colours and one drawing style. Play crops this asset
differently across surfaces; the wordmark and the board both sit well inside
the edges, and the script refuses to render if the copy grows wide enough to
collide with the board.

Note on the screenshot size: the captures come off the device at 1080×2400,
which Play rejects twice over — 20:9 is taller than the 2:1 limit, and a
screen capture is 32-bit RGBA where Play wants 24-bit with no alpha. Rather
than crop the UI, `tools/fix_screenshots.py` drops the alpha channel and
splices 240px out of the band of flat background above the gesture bar, so the
result is 1080×2160 with every pixel of UI still present. Re-run it after any
fresh capture.

## Release checklist

1. Generate the upload keystore and `android/key.properties` — see
   `android/key.properties.example`. Back the keystore up; losing it means
   losing the ability to update this app.
2. `./tools/release.sh`
3. Confirm the build log does **not** warn about debug keys.
4. Upload `build/app/outputs/bundle/release/app-release.aab`.
5. Upload `build/symbols/` to the Play Console (or archive it) — obfuscated
   crash reports are unreadable without the matching symbol files.
6. Tag the release: `git tag -a v1.0.0 -m "Sokoban 1.0.0" && git push --tags`

## When ads are added

Advertising is planned for a release after 1.0.0. It is not a drop-in SDK
change — it moves the app from "collects nothing" to "collects and shares data
with a third party", and several declarations have to move with it, in the
same release that ships the ads and not before.

**The app**

1. Add the `INTERNET` permission to the release manifest. It is currently
   absent, which is what makes the present privacy claims verifiable.
2. Add `com.google.android.gms.permission.AD_ID`. Play requires it to read the
   advertising ID on `targetSdk` 33+, and the SDK will read it.
3. Ads are network I/O on a device that has never had any: handle no
   connectivity, a slow response and a failed fill without stalling a puzzle
   or losing the player's progress.

**Play Console**

4. Data safety: "No data collected" and "No data shared" both become false.
   Declare what the ad SDK collects — usually the advertising ID under Device
   or other IDs — and mark it as shared with a third party. The provider
   publishes the list; use theirs rather than guessing.
5. Set **Contains ads** on the store listing, and answer the ads question in
   the content-rating questionnaire again. The rating may change.
6. If the app is ever put in a Families programme, ad SDKs and ad content must
   be certified for it. Check before opting in.

**Docs and copy** — the "no ads" wording was removed from all of these in
1.0.0, so nothing should now contradict an ads release, but each still needs a
pass:

7. `PRIVACY.md` and `docs/privacypolicy.html` — describe the ad SDK, what it
   collects, and link its own privacy policy. Update the effective date and add
   a line to the change list. The hosted copy is what Play links to, so it must
   be live *before* the release goes out.
8. `store/listing.md` — this file: the **Ads** field, the Data safety section,
   and the full description.
9. `docs/README.md` — the Privacy section and the **Ads** row.
10. `store/feature_graphic.png` — regenerate if the banner copy makes any claim
    that no longer holds. `tools/make_feature_graphic.py`.
