# Miami GP 2026 · Driver fastest laps

A single-page visualisation of any driver's fastest personal lap at the 2026
Miami Grand Prix, driven by data from the unofficial
[OpenF1](https://openf1.org) API. Pick a driver from the dropdown and the page
re-fetches their fastest timed lap and replays it.

It animates a car marker around the Miami circuit using OpenF1 location samples
(~3.7 Hz, interpolated for smooth playback) and overlays live telemetry: speed,
gear, throttle, brake, RPM and DRS state. The track outline is drawn from the
same coordinate cloud, so no map asset is required.

## How it works

- Session: Miami GP 2026 Race (`session_key=11280`, `meeting_key=1284`).
- On load, the page fetches the full driver list from `/v1/drivers` and
  populates a dropdown.
- When a driver is selected, it pulls every lap they ran via `/v1/laps`,
  picks the fastest non-pit-out lap, and replays it.
- Default selection: Gabriel Bortoleto (#5, Audi) — his personal fastest was
  L56 with sectors 32.882 / 35.313 / 25.893 (per OpenF1).
- The badge and car marker are coloured from each team's `team_colour`.

## Run

It's a static page; just open `index.html` in a modern browser, or serve the
folder over any static server, e.g.:

```sh
python3 -m http.server 8000
# then visit http://localhost:8000
```

OpenF1 returns CORS-enabled JSON, so no proxy is needed.

## Data flow

1. `GET /v1/drivers?session_key=11280` — list of drivers in the session.
2. `GET /v1/laps?session_key=11280&driver_number=<N>` — every lap by that
   driver; client picks the fastest with `is_pit_out_lap=false`.
3. `GET /v1/location?session_key=...&driver_number=<N>&date>=...&date<=...` —
   x/y/z position samples for the lap window.
4. `GET /v1/car_data?session_key=...&driver_number=<N>&date>=...&date<=...` —
   speed / throttle / brake / gear / RPM / DRS at the same cadence.

The viewer normalises the `(x, y)` cloud into screen space, draws the racing
line coloured by throttle/brake state, and animates the marker along the
timestamps with linear interpolation.

## Caveats

- OpenF1 is a community/research API, not a licensed F1 commercial feed.
- OpenF1's docs note that the location coordinates show progress around the
  track but not precise lateral placement; the origin is arbitrary.
- Lap durations from OpenF1 can differ slightly from the official F1 timing
  screen — they're derived from the live positional/timing stream, not the
  official sector-based clock.
