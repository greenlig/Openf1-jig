# Bortoleto · Miami GP 2026 · Fastest Lap (L56)

A single-page visualisation of Gabriel Bortoleto's fastest personal lap at the
2026 Miami Grand Prix (lap 56), driven by data from the unofficial
[OpenF1](https://openf1.org) API.

It animates a car marker around the Miami circuit using OpenF1 location samples
(~3.7 Hz, interpolated for smooth playback) and overlays live telemetry: speed,
gear, throttle, brake, RPM and DRS state. The track outline is drawn from the
same coordinate cloud, so no map asset is required.

## Lap details

- Driver: Gabriel Bortoleto (#5, Audi)
- Session: Miami GP 2026 Race (`session_key=11280`, `meeting_key=1284`)
- Lap: 56 — Bortoleto's personal fastest of the race
- Sectors: S1 32.882s · S2 35.313s · S3 25.893s (per OpenF1)
- Top trap speed (per OpenF1 lap row): 309 km/h

The race fastest lap (Lando Norris, 1:31.869 on L35) is **not** what this
prototype shows — this is Bortoleto's own fastest.

## Run

It's a static page; just open `index.html` in a modern browser, or serve the
folder over any static server, e.g.:

```sh
python3 -m http.server 8000
# then visit http://localhost:8000
```

OpenF1 returns CORS-enabled JSON, so no proxy is needed.

## Data flow

1. `GET /v1/laps?session_key=11280&driver_number=5&lap_number=56` — gets
   `date_start`, `lap_duration`, sector times, trap speeds.
2. `GET /v1/location?session_key=...&driver_number=5&date>=...&date<=...` —
   gets x/y/z position samples for the lap window.
3. `GET /v1/car_data?session_key=...&driver_number=5&date>=...&date<=...` —
   gets speed / throttle / brake / gear / RPM / DRS at the same cadence.

The viewer normalises the `(x, y)` cloud into screen space, draws the racing
line coloured by throttle/brake state, and animates the marker along the
timestamps with linear interpolation.

## Caveats

- OpenF1 is a community/research API, not a licensed F1 commercial feed.
- OpenF1's docs note that the location coordinates show progress around the
  track but not precise lateral placement; the origin is arbitrary.
- The lap duration the API returns (94.088s) differs slightly from the
  official F1 timing screen (1:33.500), as OpenF1 derives it from the live
  positional/timing stream.
