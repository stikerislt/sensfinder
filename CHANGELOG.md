# Changelog

Newest first. "Player" = what people on the server notice, "Server" = what server owners need to know.

## 0.5.20

**Fixed**
- The welcome sign could show two texts on top of each other ("!search to start" and "!resume continues") when
  a player joined before Steam had identified them. The welcome now waits for that, and the sign is redrawn
  cleanly.
- `!resume` is only offered when it will work. A saved run that can no longer be continued (older than 24 hours,
  or `!setup` changed since) used to be offered on the sign and then refused; chat now says once that it was
  discarded and why.
- After a CHANGE result, the next step pointed to `!search`, which does not test the new value. It now says how
  to switch: `!setup` with the new value, play it for at least 3 days, then the confirm test from `!menu`.
- The run length shown at the start was too long ("~39 min"); it now matches the real ~30 minutes.
- `!setup` during a run is refused (it used to change the profile under a run that kept the old values).
  Changing DPI or sensitivity says when it ends a saved run.
- Running `!setup` again with the same values no longer restarts the 3-day wait for the confirm test.
- A positive_control or confirm run picked from `!menu` no longer repeats on every later `!search`; a refused
  confirm test no longer blocks later searches.
- `!search` no longer throws away a saved run when the new run cannot be set up.

**Changed**
- Clearer chat during a run: plain block messages ("Block 3 of 13 at sens 0.85") and the true number of
  repeats, instead of internal names.
- After a plugin update, players with a saved run are told it is saved, without "your run stopped" for players
  who were not playing.

## 0.5.19

**New**
- `!finetune` (player): available after 3 complete `!search` runs. Plays one more run with every test value
  inside the range your runs could not tell apart, then gives ONE concrete sensitivity - the best estimate from
  all your runs combined, rounded to 0.01. Your current sensitivity can win ("keep your current sensitivity").
  The report also says which range performs the same, because differences that small cannot be measured.
- After the run that brings a player to 3 complete runs, chat and the result sign point to `!finetune`.
- While a finetune run is going, the sign reads "FINETUNE block 3 of 13".
- When `!finetune` is not available yet, it lists the runs that counted (date, blocks) and how many runs were
  started but not finished. Only runs played to the end count; a stopped and resumed run counts once.

**Fixed**
- Warm-up trials showed "not scored - skipped" even after a clean headshot, which read as "my hits do not
  register". The sign now says what happened: "warm-up (not scored): hit in 0.62s" or "... MISS". Trials that
  really are not scored name the reason (skipped, you never moved, the bot left the server, no free bot).
- A rush trial with only one bot down said "both bots down"; it now says "1 of 2 bots down".
- Spawn protection is switched off (`mp_respawn_immunitytime -1`, in the cfg and set by the plugin). In
  deathmatch-type game modes a respawned bot could not be hit for 10 seconds, so the first trials of a run
  could not register.

**Changed**
- Moving-flick instructions: "flick to the running bot's head and shoot at once - don't follow it".
- Server name in `sensfinder.cfg`: "SensFinder arena".

## 0.5.18

**New**
- `!resume` (player): progress is saved after every finished block. A run cut short by a disconnect, crash,
  server restart, map change or plugin update continues where it stopped (after a short warm-up). On joining,
  chat and the wall sign say when an unfinished run exists. `!search` always starts a new run.
- Trials with no mouse movement and no shot are not scored (a dropped connection, alt-tab, AFK) instead of
  counting as misses against the sensitivity being tested.
- A run pauses after 8 unplayed trials in a row (about 30-40 seconds of no input); finished blocks stay saved.

**Changed**
- Idle kick after 5 minutes of no mouse movement (warning at 4), was 10 minutes.
- After a plugin update, players who were mid-run are told their progress is saved.

## 0.5.17

- The INCONCLUSIVE result explains itself: nothing tested was measurably better, and the range that performs
  the same is narrower than the +/-10% needed to say KEEP. Points to `!sf_report`, which combines runs.

## 0.5.16

- `!setup`: a sensitivity typed with a decimal comma ("0,9") was read as 9 and saved. A comma is now the
  decimal mark for the sensitivity question (and still groups thousands for DPI), and a rejected answer keeps
  the question open.
- A trial whose bot left the server mid-trial is no longer scored as a miss.
- A disk problem on the server no longer freezes a run mid-block or hides the final result.
- No more confusing "centre 0" warnings at the start of a run after a run that ended too early.

## 0.5.15

- A player joining while someone else is mid-run no longer spawns inside that player's room.

## 0.5.14

- `css_sf_lanes` (server console) shows the player cap: "player cap: 5 humans (15 slots, up to 10 bots)".

## 0.5.13

- Starting a run during the break between tasks of a manual block could leave a player stuck waiting for a
  sensitivity change; it is now refused like any other busy moment.
- The full-server check no longer counts a player who has already left.

## 0.5.12

- The player cap follows the server's slots: one player per 3 slots (each player needs 2 bots), at most 5.
  9 slots = 3 players, 12 = 4, 15 or more = 5.

## 0.5.11

- Kicked players see a reason: CS2's "server is full" and idle messages instead of "Kicked by server".

## 0.5.10

- `weapon_accuracy_nospread 1` (in the cfg and set by the plugin).

## 0.5.9

- Bots a trial is moving are no longer frozen; `css_sf_pinmovers 0|1` (server console) switches it for testing.

## 0.5.8

- Result files: every row of a search run was one column short, which broke `!sf_report`. Fixed, and older
  files are read correctly.
- `!sf_report` used the oldest run as the latest one when combining runs.
- A second `!search` in the same visit wrote into the first run's file and reused its block order; each run
  now has its own file and order.
- Changing sensitivity during the break between tasks or the start countdown is now detected (the block is
  marked invalid / the countdown waits for the right value again).

## Known issues

- Running bots can look slightly jittery. Cosmetic: scoring uses the server's positions.
- `weapon_accuracy_nospread` was reported as not working; under investigation. The plugin log shows whether
  the server accepted it ("Cvars: ... all hold").
