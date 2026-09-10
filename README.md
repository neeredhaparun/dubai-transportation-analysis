# Dubai Public Transportation Network Analysis

An end-to-end data analytics project exploring the structure, geographic
coverage, service patterns, and connectivity of Dubai's public transportation
network (Bus, Metro, Tram, Ferry), using RTA's official GTFS transit feed.

**📊 [View the interactive Tableau dashboard](https://public.tableau.com/views/DubaiPublicTransportationNetworkAnalysis/Overview?:language=en-US&:sid=&:redirect=auth&:display_count=n&:origin=viz_share_link)**

---

## Problem Statement

How is Dubai's public transport network structured, where is coverage
concentrated, when does the network operate most intensively, and how
well-connected are its transfer points?

This is broken into four sub-questions, each answered by its own dashboard:

1. **Network Overview** — How extensive is the network, and how is it
   distributed across modes?
2. **Geographic Coverage** — Where is coverage concentrated across the city?
3. **Service & Schedule** — When and how frequently does the network operate?
4. **Connectivity & Transfers** — How well-connected is the network, and
   which stops function as key interchange hubs?

---

## Data Source

- **Content:** RTA's (Road and Transport Authority, Dubai) official GTFS
  transit feed — routes, stops, trips, stop times, calendar, and transfer
  data for Bus, Metro, Tram, and Ferry
- **Obtained via:** A third-party mirror indexed on the
  [Mobility Database](https://mobilitydatabase.org/feeds/gtfs/mdb-3359)
  (a nonprofit aggregator of public transit GTFS feeds), rather than
  downloaded directly from RTA's own site
- **Verification:** the feed's `agency.txt` explicitly identifies
  "Road and Transport Authority (RTA)" (rta.ae) as the publisher, and
  route/stop names throughout the dataset correspond to real, verifiable
  Dubai transit infrastructure (e.g., Union Station, Al Ghubaiba, Gold Souq,
  Dubai Metro Red/Green lines)
- **Scope:** 201 routes, 2,819 stops, 66,562 scheduled trips, ~1.4M
  stop-time records

---

## Methodology

Analysis was done in Python (pandas, SQLite/SQL) in Google Colab, with the
interactive dashboards built in Tableau Public.

**Mode classification:** GTFS's numeric `route_type` codes were mapped to
mode names (0=Tram, 1=Metro, 3=Bus, 4=Ferry) and verified against actual
route names in the data (e.g., type 1 routes matched Dubai Metro's known
"MRed"/"MGrn" line naming) before use.

**Time handling:** GTFS represents times past midnight as 24:00+ (e.g.,
25:30 = 1:30 AM the next service day) rather than wrapping to 00:00+. All
hour-based analysis explicitly folds these values back into standard 0–23
hour format before aggregating, to avoid double-counting or misrepresenting
late-night activity.

**Two distinct connectivity metrics:** this project measures "connectivity"
two different ways, and treats them as separate, complementary findings
rather than the same thing:
- *Transfer connectivity* — formal, RTA-documented interchange points from
  the GTFS `transfers` file (e.g., Gold Souq, Al Ghubaiba, Union)
- *Route diversity* — how many distinct routes physically serve a given
  stop, derived independently from trip/route data (surfaces different,
  often unofficial high-overlap locations like Satwa and Naif)

**Data quality checks:** the `stops`/`zone_id` field and stop-name text were
both evaluated as potential "area" groupings for geographic analysis and
found too sparse/inconsistent to use reliably; area-level breakdowns were
dropped rather than forced from unreliable groupings.

---

## Key Findings

Dubai's public transportation network comprises 201 routes and 2,819 stops
across four modes, supporting 66,562 scheduled trips. **Bus forms the clear
backbone of the network**, accounting for 90% of routes and 94% of stops,
while Metro, Tram, and Ferry serve smaller, more specialized roles.

**Service patterns vary significantly by mode.** Network-wide activity shows
two clear daily peaks (~8–9 AM and ~5–7 PM), consistent with commuter
demand, with minimal service between 2–4 AM. Looking at the three smaller
modes individually: **Metro shows a pronounced dual commuter-peak pattern**
mirroring the network-wide trend; **Ferry service builds steadily toward a
single evening peak**, suggesting leisure/evening-oriented usage; and
**Tram maintains a flat, consistent schedule** throughout the day, indicating
fixed-interval rather than demand-responsive service. Weekday service runs
about 14% higher than weekend service on average.

**Connectivity analysis reveals two distinct types of network importance.**
Formal transfer data identifies **Gold Souq Bus Station, Al Ghubaiba, and
Union Station** as the busiest official interchange hubs, with Bus↔Metro as
the dominant transfer pairing — reflecting Bus's role feeding passengers into
the Metro system. Separately, route-density analysis surfaces a different
set of locations (Satwa, Naif, Rolla) with high route overlap that are not
formally documented as transfer hubs — potentially informal or underserved
connectivity points worth further investigation.

---

## Repository Structure

```
├── README.md
├── notebook/
│   └── dubai_transportation_analysis.ipynb
└── data_samples/
    ├── agency.txt
    ├── calendar.txt
    ├── routes.txt              (full — 201 rows)
    ├── stops_sample.txt        (first 50 of 2,819 rows)
    ├── trips_sample.txt        (first 50 of 66,562 rows)
    └── transfers_sample.txt    (first 50 of 1,002 rows)
```
Full-size files (`stop_times.txt` at ~1.4M rows, `shapes.txt` at ~185K rows)
are not included due to size — see Data Source above to obtain the complete
feed.

## Tools Used

- **Python (pandas)** — data loading, cleaning, time-handling logic
- **SQLite/SQL** — aggregation queries (joins across routes/trips/stop_times/transfers)
- **Google Colab** — notebook environment
- **Tableau Public** — four interactive dashboards

## Dashboard Overview

| Dashboard | Contents |
|---|---|
| Network Overview | KPI cards (routes/stops/trips/modes), routes & stops by mode, top 10 routes by scheduled trips |
| Geographic Coverage | Interactive stop map colored by mode, with mode filter |
| Service & Schedule | Trips by hour, trips by day of week, weekday vs. weekend, trips by hour × mode |
| Connectivity & Transfers | Top transfer locations, transfers by mode pair, connected routes per stop, transfer hub map |

**[View the live dashboard on Tableau Public](https://public.tableau.com/views/DubaiPublicTransportationNetworkAnalysis/Overview?:language=en-US&:sid=&:redirect=auth&:display_count=n&:origin=viz_share_link)**
