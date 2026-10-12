# AEGIS

Autonomous Emergency Geospatial Intelligence & Simulation Software — a map-first emergency digital twin and single-operator decision-support system for CodeFusion 2K26.

AEGIS combines a slowly rotating and searchable 3D world view, source-labelled incident intelligence, deterministic hazard simulation at a selected world location or operating area, time-specific consequence layers, evacuation and resource planning, what-if comparison, reverse-cascade intervention ranking, recovery/re-entry screening, and decision receipts. The active targets are the responsive web command center and Windows desktop wrapper. The phone app is documentation-only for now.

## Live deployment

- **AEGIS command center:** https://aegis.guptashivaani233.workers.dev
- **Dedicated Agent Ledger:** https://aegis-agent-ledger-codefusion-2k26.guptashivaani233.workers.dev/agent-ledger
- **Source repository:** https://github.com/PirateKingLuffie/aegis-codefusion-2k26
- **Windows and presentation downloads:** https://github.com/PirateKingLuffie/aegis-codefusion-2k26/releases/tag/v0.1.0
- **Deployed application source commit:** `ac9c069` (`fix: reset playback with cleared response`)
- **Main Worker version:** `f2d3cb55-907b-4d5a-9cd0-5f57d12c4100`
- **Ledger Worker version:** `af5d0e22-9ed7-4e83-b0ff-ea62a312114f` (unchanged)
- **Deployed verification:** 112 tests total — 111 passed, 0 failed, 1 optional public-live-feed test skipped; TypeScript, ESLint and production build passed.
- **Earlier incident/media baseline (28 August 2026):** `638e063` / Worker `97819d30-6091-41ec-9b57-6320eca81edf`; retained as historical evidence.
- **Remote-browser acceptance:** At 1366 × 768 the centred tilted globe loaded with automatic orbit running and 20 source records after live-feed hydration. A completed three-point area generated the deterministic dry-run response package and evacuation view; its reachable Clear control removed the selection, assessment, evacuation and area-incident overlays, reset playback to T+000 and returned to a clean Monitor globe. Browser warnings and errors remained empty.

Cloudflare D1 is migrated and live for immutable Agent Ledger receipt revisions. Cross-deployment `GET`, `POST` and human-review `PATCH` were verified. D1 is the ledger store only; scenario history remains browser-local unless the separate FastAPI operations service is deployed.

### Automation and evidence companion surfaces

Two additive routes are deployed alongside the command center: [`/live-context`](https://aegis.guptashivaani233.workers.dev/live-context) provides a source-labelled incident/evidence desk, and [`/automation`](https://aegis.guptashivaani233.workers.dev/automation) provides deterministic regional watch evaluation with browser-local, dry-run alert previews. Neither route sends an emergency notification or changes the existing command center. See [Automation and live evidence](docs/AUTOMATION_AND_LIVE_EVIDENCE.md) for architecture, truth classes, limitations and verification.

## Zero-cost stack

- OpenFreeMap is the primary live MapLibre globe; CARTO Dark Matter is the independent automatic fallback.
- OpenStreetMap supplies world context, Overpass footprint imports, Nominatim search and OSRM road geometry.
- Public Terrarium/SRTM-derived Terrain-RGB tiles supply global terrain where available.
- NASA EONET, USGS, GDACS and Google News RSS provide current/recent source-labelled incident context.
- Open-Meteo provides weather-model context and a Copernicus GLO-90 elevation grid; it is not a campus survey.
- Wikimedia Commons provides keyless open-media search when relevant; publisher, official and search links remain available when it has no suitable result.
- The operations brief is deterministic and reads the current twin state. Cloudflare Workers AI can add a grounded narrative through the zero-secret `AI` binding using [`@cf/meta/llama-3.2-3b-instruct`](https://developers.cloudflare.com/workers-ai/models/llama-3.2-3b-instruct/); compatible-provider and Groq paths are optional fallbacks.
- No paid model, card or API key is required for the core workflow. Cloudflare’s current [Workers AI pricing](https://developers.cloudflare.com/workers-ai/platform/pricing/) includes a daily free allocation, but external quotas and terms can change.

Public upstreams can throttle, revise or become unavailable. AEGIS exposes their status and never relabels a simulation, dated report, search link or ordinary video as a live sensor/camera.

## World-to-scenario workflow

- The initial Earth uses a slightly tilted globe projection and a restrained idle orbit. Pointer, wheel, keyboard and camera interaction pause rotation; it resumes only after idle while the camera remains at globe-overview zoom. Reduced-motion preference and background tabs also pause it.
- Nominatim search accepts countries, cities, addresses, streets, buildings, landmarks and coordinates. A selected result receives a persistent focus marker/name and becomes the active planning coordinate.
- At regional/street zoom, one coherent keyless OpenStreetMap raster is kept beneath provider labels and AEGIS operational layers through zoom 19. Sentinel context imagery is optional and fades out by its configured zoom-15 layer limit, preventing opaque provider placeholders or blank low-feature regions when imagery or subtle vector fills are unavailable.
- Place labels, roads and available building context come from public OpenStreetMap/OpenMapTiles/CARTO sources. Coverage is not uniform and the imagery is not live satellite.
- Incidents use one clustered WebGL renderer, without duplicate DOM pings. Only fresh, active or monitoring observed records retrieved from a live source receive red markers and pulses; cached, aging, degraded or unknown-freshness records are gray context, and simulations are amber. Labels use the same classification. “Current observed” describes the provider record, not an AEGIS field observation or verified live damage.
- The operator can place a hazard source, evacuation origins/destinations and an operating area, then run any of the five implemented hazard plugins around the selected world location. “Any location” does not mean survey-grade fidelity everywhere, and “five plugins” does not mean literally every disaster type is modelled.
- Each committed Origin, Safe point or Source marker—and each completed three-or-more-point Area—starts a visible local deterministic assessment. Inspect clicks and uncommitted area vertices do not. A pointer outside the previous planning domain recenters that domain; a completed area recenters it on the selected centroid. Supported region dimensions are 120 m–100 km; selections outside that range use a visibly labelled center-based fallback, not a simulation bounded to the drawn region. The workflow recalculates estimated impact/access/evacuation outputs, creates a local decision brief and offers the detailed evacuation response through one button. It does not send phone notifications, public alerts or field dispatches. See [Globe, incident and operator-selection workflow](docs/GLOBE_INCIDENT_AND_SELECTION_WORKFLOW.md) for the complete interaction and truth limits.

## Development verification

Use Node.js 22 or newer from `E:\CodeFusion EIT Hackathon\AEGIS`:

```powershell
npm ci
npm run verify
npm run demo
```

This command is for a developer-owned local verification session. The published judge build is the HTTPS deployment above; no local server was used for the final public release handoff.

## Full local operations stack

Docker Compose packages the web frontend, FastAPI operations service, PostgreSQL/PostGIS, Redis and nginx:

```powershell
Copy-Item deployment\.env.example deployment\.env
# Replace every CHANGE_ME value in deployment\.env.
docker compose --env-file deployment\.env up --build -d
```

The backend provides versioned records, revisions, a hash-linked audit chain, PostGIS spatial assets, Redis response caching, health telemetry and replayable WebSocket events. The web app continues with local browser scenario history if the durable service is not running.

## Real EIT data

The site view uses the official contact-page map reference plus a bundled OpenStreetMap footprint subset. It is not a surveyed EIT BIM. At runtime, a public Copernicus GLO-90 grid supplies regional elevation context when available; it is not a surveyed campus DEM. Unknown building functions, heights without OSM levels, fine surface levels, drainage, occupancy and infrastructure dependencies remain estimated.

Open **Workspace → Campus data** to import a validated campus JSON file. The schema and provenance rules are in [docs/EIT_DATA_IMPORT.md](docs/EIT_DATA_IMPORT.md); a downloadable template is served at `/api/campus/eit/template`. AEGIS deliberately will not fabricate an exact college boundary, BIM, gates, drains or occupancy from missing records.

## Implemented hazard and decision capabilities

- Five executable plugins: flagship surface-water flood plus earthquake, wildfire, cyclone/surge and chemical-plume screening models.
- Hazard-specific time visuals: flood/deeper-water extents and flow; earthquake isoseismal bands and symbolic pulse outlines; wildfire perimeter, smoke and spread axis; cyclone wind/surface-water fields and scenario track; chemical plume, threshold zone and direction axis.
- A shared deterministic seed, 120-minute timeline and reproducible what-if branches.
- Flood depth, arrival, rise/recession, velocity, surface, waterlines, internal-depth and affected-floor screens.
- Building, road, bridge, critical-facility, utility and population consequences by selected minute.
- Debris, contamination, sewage-overflow, erosion, smoke/surface-water and utility-failure secondary screens where applicable.
- Uncertainty envelopes, confidence layer, recovery priorities, re-entry holds and restoration actions.
- Time- and mode-specific passability for pedestrian, car, bus, ambulance and heavy rescue.
- Capacity-aware evacuation origins/destinations, alternatives, staged departures, resources, shelters, coverage gaps and human approval receipts.
- Imported OSRM candidates screened against active simulated consequence polygons before route styling.
- Scenario save/load/version history, bookmarks, annotations, named layouts, replay, JSON/CSV/print exports, measurement, layer search/presets/solo/threshold, comparison layouts, alerts, command palette and local audit history.
- The primary Scenario panel includes three loaded planning cases at different locations: EIT campus flood, Tokyo earthquake access and Miami cyclone/surge. A separate Sendai coastal-inundation card is explicitly labelled as a tsunami proxy that reuses the cyclone/surge low-point engine; it is not a calibrated tsunami solver.
- Main World map, Incident, Scenarios, Impacts, Sources and Analysis destinations include concise purpose briefings instead of unexplained icons.
- Decision support includes an **Explain evacuation procedure** action grounded in the current departure stages, screened route, destination/capacity, coverage, remaining exposure and model warning. The optional language layer may explain this structured plan; it does not create a route or evacuation order.

All hazard consequences and evacuation outcomes are prototype planning estimates. They are not observed damage, engineering certification, official closures, casualty predictions or evacuation orders. Casualty and economic-damage values remain disabled until authoritative methods and inputs are provided.

## Documentation

- [Architecture](docs/ARCHITECTURE.md)
- [Judge brief](docs/JUDGE_BRIEF.md)
- [Agent Ledger](docs/AI_AGENT_LEDGER.md)
- [Automation and live evidence](docs/AUTOMATION_AND_LIVE_EVIDENCE.md)
- [Real-world reliability and IoT roadmap](docs/REAL_WORLD_AND_IOT_ROADMAP.md)
- [API and providers](docs/API_AND_PROVIDERS.md)
- [Data provenance](docs/DATA_PROVENANCE.md)
- [EIT authoritative-data import](docs/EIT_DATA_IMPORT.md)
- [Globe and interface acceptance](docs/GLOBE_AND_UI_ACCEPTANCE.md)
- [Globe, incident and operator-selection workflow](docs/GLOBE_INCIDENT_AND_SELECTION_WORKFLOW.md)
- [Demo runbook](docs/DEMO_RUNBOOK.md)
- [Deployment handoff](docs/DEPLOYMENT_HANDOFF.md)
- [Windows and deferred mobile builds](docs/PLATFORM_BUILDS.md)
- [Completion ledger](COMPLETION.txt)

## Earlier release verification snapshot

- Five deployed hazard requests returned HTTP 200 in `100–420 ms`, each with 25 timeline frames. Flood returned 41 route candidates in the accepted sample; other hazards can truthfully return zero routes when no destination remains feasible.
- A unique cold live-intelligence query completed in `4,559 ms` with three live sources, one degraded source and nine incidents; its immediate cached repeat completed in `169 ms`.
- Workers AI returned a grounded narrative in approximately `1.94 s`; D1 preserved the human-approved revision 2 and its receipt chain verified across the main and dedicated-ledger deployments.
- Backend tests passed `7/7`; opt-in public-feed tests passed `6/6`; `npm audit` reported zero vulnerabilities.
- Final canonical `npm run verify`: 67 tests total, 66 passed, zero failed and one intentional optional-network skip. Typecheck, lint and production build passed; the build reports only the documented large-chunk performance advisory.
- Remote-browser acceptance on the public Worker confirmed the labelled, slightly tilted globe, visible idle rotation, world search to New Delhi, district and street labels through deep zoom, no black canvas and no browser warnings or errors. A final run on the actual Lenovo LOQ remains the owner’s device-specific check.

These results are retained as earlier baseline evidence. The current released rehaul is recorded in the live-deployment section and in the globe/interface acceptance note above.

## Oracle Free Tier expansion

Deployment assets are ready under `deployment/`: nginx HTTP/HTTPS templates, Oracle Ubuntu bootstrap, Compose services, firewall baseline, health check, backup, update and rollback scripts. The live Cloudflare release does not include FastAPI, PostGIS, Redis or WebSocket replay. Creating that full durable Oracle stack, its public IP, DNS record and TLS certificate still requires the owner’s Oracle and domain access; no source code can substitute for those external credentials.
