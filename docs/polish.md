# BLACKBOX human-play polish

October 6, 2026. Tested in Chromium using Playwright, real mouse movement, pointer capture, clicks, keyboard movement, and native touch events. Main viewport: **390 × 844**. Also checked all five games at **1200 × 800**.

Each of MURMUR, GOO, BLOODLINE, and EVERDUNGEON received at least **185 seconds of active play before changes**, plus further play after fixes. ORBITAL received repeated freehand drawings, all three presets, slider drags, resets, and overlay toggles. Screenshots were opened and visually reviewed, not merely captured. Browser console errors and uncaught exceptions were monitored throughout.

Only the five requested game files and this evidence were changed. The index, other games, and router remain untouched. No libraries, CDNs, or external requests were added.

## ORBITAL

- **Freehand completion:** pointer release never called reconstruction. Valid strokes now start tracing automatically, with endpoints joined. Short strokes and interrupted gestures receive visible explanations. Input is tracked by pointer ID and resets on cancellation or lost capture.
- **Spiky reconstruction:** unsigned DFT bins incorrectly represented negative frequencies between integer samples. Signed frequencies fix the actual continuous path. Only successive final tips enter the persistent trail, with substeps for smooth rendering. Heart RMS at K = 8 / 32 / 96 was **5.60 / 0.61 / 0.13 px**; the continuous K = 32 path measured about **831 px**, without the previous zigzags.
- **Corner line:** the constant Fourier coefficient was drawn as an arm from the canvas origin. It now establishes the center without drawing a line or circle.
- **Slider:** a centered mouse drag did not reproduce event leakage in the fetched revision. Its hit area was only 6 px high. It is now **44 px high**, has its own row and touch handling, and real drags preserve the source drawing while changing K. Changing K resets the old trail and pen.
- **Overlay:** “Trace it” was disabled during reconstruction. The replacement **Hide circles / Show circles** button visibly toggles the circles and arms and exposes its state with `aria-pressed`.

Verified with real closed-loop drawing, a rejected tap, Heart/Star/Cat presets, K = 8–96, actual slider drags, both overlay states, reset, and desktop resizing.

[Before: spiky heart and corner line](polish/before-orbital-heart.png) · [Freehand starts](polish/after-orbital-freehand.png) · [Rejected stroke explanation](polish/after-orbital-rejected.png) · [Clean heart with circles](polish/after-orbital-circles-on.png) · [Circles hidden](polish/after-orbital-circles-off.png) · [Slider at 96](polish/after-orbital-slider-96.png)

## MURMUR

Steering, rings, scoring, hawks, and the end screen worked during play. The simulation and countdown advanced once per display frame, so higher refresh rates ran the game faster.

Added a fixed 60 Hz simulation accumulator, bounded catch-up after stalls, reset timing after visibility changes, and clean input/flock state on replay. The timeout now stops scoring before showing the final result.

Played by repeatedly dragging the flock toward rings, completed timed rounds, and restarted. A deterministic browser check drove identical seeded games at **60 Hz and 120 Hz**: after two seconds both had **88 seconds left**, identical flock positions, identical score, and identical simulation-step count.

[Timed round result](polish/after-murmur-end.png)

## GOO

Slowly pushing one matching drop into another moved the target away instead of merging: separation happened before enough overlap could accumulate. The contour table also used a different corner-bit order from the field sampler, causing visibly broken outlines. The 14-drop overflow limit was hidden, and releasing after holding still could fling a drop using an old velocity.

Matching colors now fuse on contact. Fixed the marching-squares corner bits, refined the grid, and restricted sampling to each blob's bounds. Added brief growth/squish animation, expanding merge rings, merge/sludge messages, a capacity counter and warning, and stale-velocity clearing on release. Final separation positions are constrained to the jar.

Played repeated sorting/merging attempts and natural overflow/replay cycles. Additional controlled fixtures verified a **100-step slow mouse drag** merging two 400-mass drops into one 800-mass drop; mixed colors turning brown; an actual dragged merge crossing the 4200 target and opening the win screen; the fifteenth drop opening the overflow screen; and replay clearing the game. The controlled win fixture validates the win transition and is separate from the natural-spawn playtests.

[Before: broken contours](polish/before-goo-play-28.png) · [Smooth merged blob](polish/after-goo-merge.png) · [Win screen](polish/after-goo-win.png)

## BLOODLINE

After eight seconds, 11 of 12 walkers had left the original mobile camera. Finishing an active first generation reported zero generations and zero distance. The title also overflowed its panel.

The field camera now fits the walkers and every rendered limb while keeping bodies readable. Walker numbers connect the animation to the ranked breeding choices. Finish records the active generation before presenting results. Breeding choices are 44 px buttons with selection state, and the title fits a phone.

Watched successive full generations, alternated manual selection and auto-breeding, tested deselection and the four-parent requirement, finished mid-generation, and replayed. Browser checks verified all 12 walkers in view, limb bounds inside the viewport, and a saved nonzero result for the first interrupted generation.

[Before: walkers offscreen](polish/before-bloodline-inspect.png) · [Whole field visible](polish/after-bloodline-final-camera.png) · [Mid-generation Finish records distance](polish/after-bloodline-finish.png)

## EVERDUNGEON

The instructions allowed dragging the left half of the screen, but input only worked on the canvas. On a phone the lower control area was blank and inactive. Input could also survive a death/restart, and a lethal collision on the exit could continue into the next depth in the same update.

The left half of the full play area now accepts mouse, touch, and pen through one pointer-capture implementation, including the margin below the board. A visible joystick follows the touch origin. Cancellation, focus loss, death, and replay clear movement. Lethal damage stops the update before collecting or descending. The title fits inside the phone layout.

Played with freeform joystick drags and actual keyboard movement along the generated corridors, collected coins, descended through multiple floors, died, and restarted. Native touch drag/cancel and below-board mouse control passed. A controlled lethal-collision-at-exit case stayed on the same depth and showed game over; replay restored three HP and cleared input.

[Joystick in the lower margin](polish/after-everdungeon-margin-drag.png) · [Game over](polish/after-everdungeon-gameover.png)

## Verification

- All five core interactions rerun after fixes, on mobile and desktop viewports.
- **Zero browser console errors or uncaught exceptions** in the completed verification runs.
- **Zero external requests** in the instrumented regression suite.
- Seventeen targeted regression checks passed, including terminal states and replay.
- Screenshots and local detailed logs retained with the working copy under `qa/`.
