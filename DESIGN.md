# Atlantis Sub - design doc

2D browser horror game. Single index.html, canvas + vanilla JS, built in small steps.

## Pitch
You are a lone captain in a small sub sent to loot Atlantis. Barotrauma's flooding, hatches and creatures breaking in, plus Tin Can's "everything runs off one pot of power and you pick what to sacrifice". You are the whole crew, so every second on one job is a second not spent on another.

## Design rules
- Gameplay first, not story.
- Every mechanic must matter more than once.
- One sense (sonar) with several costs.
- Failure is the player's fault: they own the sub.

## Demo scope
Skeleton to show systems. One hardcoded level, one stationary creature, endless rising quota. Success = how long you stay alive and keep up with the quota.
Cut for now: surface ship and launch, procedural levels, rescue call, shop menus, roaming creature AI, scan-triggered spawns, horn shockwave.

## Look (interior)
Grim submarine, not a cartoon. All textures are currently procedural (see CREDITS.md); every texture goes through one pipeline: desaturate to ~30%, multiply by dark teal-grey #1a2a33, low-frequency grime.
- Surfaces: riveted wall plating (cabin), rusty plating (engine room), exterior hull plates with weld seams, floor grating, diamond treadplate walkway.
- Props drawn in code and kept dark: pipes with flanges, structural ribs, hanging cables, vent, ceiling tube fixtures, hazard stripe bands on the door frame, storage and engine bay, a crate. Equipment keeps its sketch color (purple engine, green panel, orange storage, blue pump, red slots, yellow door) but darkened.
- Lighting: a light map multiplied over the sub. Dim ambient fill per room, flickering fluorescent tubes with soft cones (the engine room one flickers worse), green/cyan screen light from the panel, violet/warm engine glow while running, red strobe in damaged rooms. Power out: red emergency strobe for 30 s, then pitch dark with only the bay wheel glowing. The engine room is always darker and redder.
- Post: bloom on light sources, blue-green grade (folded into the light map), thick vignette, film grain. Marine snow drifts outside the hull.
- Screens, labels, gauges, the sonar and the terminal are drawn in code.
- Performance (target: 60 fps at 1080p even when the browser renders the canvas on the CPU, as on older laptops): the static sub is rendered once into a cached image; the light map lives in the sub's own small coordinate space; vignette, grain and the red alarm glow are pre-rendered; the sonar glass and the terminal are cached; sonar blobs are drawn straight to the screen (no buffers or clip masks). Graphics quality drops to LOW by itself if frames get slow (no grain, bloom, marine snow, screen static); G toggles it.

## The level: the trench
One fixed level, generated from a fixed seed (identical every run). About 550 m deep.
- A wide mouth at the top with a floating boulder, narrowing into a jagged trench with overhanging ledges.
- The throat (~335 m): the tightest part. The creature hugs its left wall with two bait relics floating beside it. You have to squeeze past it to go deeper.
- Below the throat the trench opens into a huge cavern: Atlantis. A tower, a colonnade of broken columns, a stepped temple with a fallen roof and pediment, a jagged wall fragment, an arch, and a domed building.
- Walls are organic polygons, ruins are straight-edged architecture. On sonar, ruins return pale bone-white so you can tell architecture from rock.
- Loot: deeper = richer. 2 bait relics (125), 2 on ledges/boulders in the trench (75), 4 in the ruins (175), reshuffled every dive. Loot rests on real surfaces (temple steps, dome top, ledges). Scrap is spread along walls, ledges and the ruins.

## Core loop
- Each dive starts with about 1 minute of free repairs.
- Dive, ping with sonar, find relics, grab with arm, send up on balloons. Things break, fix/lock/restart.
- About 3 dives per quota. Hit quota, it rises, repeat.
- Only fail state: sinking. Then restart from zero. No game over screen, no debt system.
- Quota is a debt with a deadline. You keep every coin. Decision is when to pay: early for safety, or late to keep cash for extra hull hits. Coins spent on safety do not count toward the quota.
- Hull is repaired free between dives only up to about 51% (one hit and you sink). Coins buy a few extra hits.
- Run length: about 3 min perfect, about 7 min until fuel runs out and you sink.

## The sub
Layout follows the second sketch (sub faces RIGHT): engine room top-left (back) with the purple engine and the breaker inside, propeller disc hanging under it; yellow door + hand wheel between engine room and crew room; crew room on the right with the chair facing the green panel; orange storage top-right; rounded nose on the right; a diagonal back-bottom wall. A green rail runs under the hull (back diagonal, bottom, up the nose); the robot arm's base slides along it. Loose wobbly controls with momentum, drag and rotation lag.

The crew room is deliberately cramped: you barely walk, but everything is slightly out of reach, so you shuffle.

The stick figure: A/D walks (the left end climbs the diagonal wall), the mouse steers his hand (the inside version of the robot arm). Limited reach, two facings only: he flips instantly to whichever side the mouse is on (a cap shows which way he faces). The hand can't pass through walls, only through the open bay door. Hold E to use what the hand touches, LMB to grab (wheel, crank, modules).

Parts:
- Propeller: outside, back. Thrust. Breaks, cannot be repaired. Only creatures break it, 3 bumps (warning stage).
- Engine: inside, back, own hatch (wheel lock). Power for everything. Internals repairable from inside.
- Control panel (green): front wall, facing the chair. On/off for every system, power readout, damage reports, runs the arm. Second most important after the engine.
- Storage (orange): top. Holds shockwave charges and spare parts. Most fragile, 1 in 2 break roll.
- Water pump mechanism (blue): floor, front corner. Only works with the pump module installed in the floor (bottom) slot.
- Hull: 200 HP, 20 per hit. Most durable.
- Arm: slides on a rail along the bottom middle.
- Chair: crew room, facing the panel.
- 4 red module slots (hatches): top, front corner, bottom (floor), leg.

Hit rules:
- Only ONE part on the side that hit rolls per impact. Chance scales with impact speed: 15% just over the threshold, up to 50% for a hard slam (storage x1.5). Hull is a health bar, not a roll.
- Two stages: the first bad roll leaves the part DAMAGED (yellow "!", still works), the next breaks it. Repairs clear both.
- Only hits above a speed threshold count. Slow nudges are free.
- After a roll, that part gets 1.5 seconds of immunity (per part, not global).
- Measured: light scrapes break something after ~11 hits (about the hull's whole life), hard slams after 3-5.
- Direction decides what breaks: diving lands on the bottom, sideways hits the nose (front) or back, climbing scrapes the fragile top.
- Creature attack order: propeller, then engine, then control panel. Keep it from being fully scripted.

## Seated and standing
Seated: Barotrauma-style monitors pop up (round sonar screen + terminal). WASD steers, mouse aims sonar and arm, income active.
Standing: camera zooms into the hull (about 0.6 s ease, about 3x), outside dims to about 30%, A/D walks, W/S climbs the ladder. WASD no longer steers, mouse no longer aims. Income stops. Arm freezes in place if powered, goes limp and wobbles if not.
- F toggles. Standing takes 0.5 s. Sitting only works next to the chair.
- Sub keeps drifting and sinking while standing.
- A hit while standing makes the figure stumble and lose 0.5 s of control. Bigger screen shake when zoomed in.
- Engine bay at the far back, panel at the far front, so the walk hurts.

## Power and engine
Fuel starts at 100%. Every active system drains it. When the engine stops: no thrust, pump, sonar, terminal, lights or air scrubbing.
Engine states: RUNNING, OFF. OFF carries a stored report type, decided at impact, never re-rolled.
- Prevention shutdown: "ENGINE MALFUNCTION PREVENTION", yellow "!", one low tone, bay dry, restart 2 s hold. Cause: rock hit that did hull damage, 25%.
- Malfunction: "ENGINE MALFUNCTION", red "X", two beeps, opening the engine bay floods it, restart 5 s hold. Cause: any creature contact, always.
Both look the same from outside. Cause is readable by feel: wall = dull thud and small shake, creature = big shake from the engine side.

Terminal: report appears 0.5 s BEFORE the engine stops. Terminal stays alive 7 s after power drops (draining bar), then goes black.
Restart: manual, standing at the engine inside the bay, hold E. Works with terminal dead. No failed restart.
Engine bay hatch: sealed by a hand wheel you spin with the mouse (about 1.5 s of spinning). With a broken propeller, an open bay lets the sea in through the shaft.

Darkness timeline after power drops: 0-7 s terminal readable, 0-30 s red emergency light, after 30 s pitch dark with only the bay wheel glowing, 60 s suffocation.
Manual power cut: breaker switch. Cuts everything. Fix a frozen panel by feel, turn power back on (3 s boot), settings reset.
Air hand crank: works with no power, alternate A/D rhythm. Slows air loss (about 60%). You do nothing else while cranking.
Control panel failure: everything freezes in its current state (systems, screens, and the throttle you were holding).

## Sonar, creatures
- Sonar is the only sense. Drains fuel, only way to find loot.
- Two sonars (like Barotrauma's active / directional):
  - All-around ping (RMB): built into the hull, no module needed. Full 360 degrees from the middle of the hull, but crude: coarse rays, returns smeared ~9 px off the walls (up to ~25), more dropout, fuzzier blobs, longer cooldown (1.6 s), more fuel per ping. Tells you where the walls are, not the details.
  - Directional wave (LMB): the sonar module. A narrow cone out of its slot's side, sharp (~2 px off the walls) and bright. The way to actually read shapes and spot loot.
- World invisible until a ping hits it. Dots fade (rock 6 s, creature 9 s). Limited range.
- Look (Barotrauma-style): only the outer skin of walls shows, as soft additive glow blobs. The hardware's position error smears returns ALONG the surface, barely into it, so walls read as a thin wobbly outline. Fresh returns bloom cyan-green and cool to blue as they fade; repeatedly pinged walls burn in brighter. Dark teal glass with grain and scanlines, beige rim with tick marks.
- Sonar module is directional: it can only aim within the arc its slot faces.
- Demo creature: one, stationary, about 70x70, invisible until pinged. Contact: 30 damage, hard knockback, always an engine malfunction, bumps the propeller.

## Hatches, water, pump, modules
- Horn: Q (or the terminal button) from the chair. Runs on 3 compressed-air charges (refilled at the dock), no power needed, but a frozen panel can't fire it. The blast goes out of whatever side the horn is mounted on, shoves a creature it catches away (it drifts back), and kicks the sub the other way. Charges can be vented into the cabin from storage for +30% air.
- Modules: sonar, pump, air generator, horn. Any module fits any slot; taking one out or putting one in cycles the hatch and traps one bucket of water (5%). Free while docked.
- Spare modules live in the orange storage: touch it and press E to open the inventory, click a module to take it in your hand, click a slot to install it (whatever was there swaps into your hand).
- Air generator: works in any slot, needs power. Not installed or no power = air falls (60 s), the hand crank slows that.
- Starting setup: AIR top, SONAR nose, PUMP floor, diagonal slot empty, HORN in storage.
- Pump runs only in the floor slot, needs water to run. Dry running sparks for 1.5 s then explodes (dead for the run, dumps air). Manual on/off only.
- Pump clears one bucket per 10 s and drains fuel.
- Water is a lock, not weight: above 30% you cannot ascend. 100% = sunk.
- Spare parts are the weight: constant slow sink, more inertia, slower throttle spool-up.

## Arm, loot, economy
- Arm is the income. Hold E while seated, aims at the mouse on the sonar screen, base slides on its rail. Reach 90 px.
- Grabs automatically. Relics: tie a balloon in 1.5 s, they float up and count when they leave the top. Scrap: pulled into storage as spare parts.
- Two hits while holding a relic break the arm. Broken arm: repaired only at the dock.
- Demo loot: 8 relics per dive (125 coins each), two near the creature. Scrap respawns each dive.
- Spare parts: burned by mid-dive repairs (docked repairs are free).

## Open questions
- Sonar and horn swap freely between slots (decided: yes, any slot; pump only works in the floor slot).
- Final suffocation time and crank strength.
- What counts as "looked at" by sonar for creature spawn count.
- Quota rise: currently x1.35 per quota.
- What happens on a missed quota (currently ends the run).
