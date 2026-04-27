# Eyewear-Form-Factor Nasal Filter — Initial Product Spec

*Working document. Last updated: April 27, 2026.*

## The problem

Personal exposure to combustion-source air pollution — black carbon (BC), NO₂, PAHs, ultrafine particles — is concentrated in commute windows that face masks address poorly because of the social cost of wearing them. NYC subway platforms run 10–17× outdoor PM concentrations (Luglio et al., NYU/EHP 2021); Brooklyn truck-route corridors drive infiltrated indoor BC that correlates with neighborhood asthma prevalence (Columbia/EHP studies). The bedroom HEPA solves the long-duration low-concentration window. The commute window is unsolved without facewear, and facewear has a compliance ceiling driven by aesthetics, not physics.

Existing nasal filter products (O2, NOSK, Clenare, etc.) target the right exposure window but achieve <20% PM1 capture in real-world conditions because they tried to fit medical-grade filtration into nostril volume. The form factor is the bottleneck, not the underlying physics.

## The thesis

Treat respiratory protection as eyewear-grade industrial design rather than medical-device industrial design. Decouple the seal point (nostril) from the filter media (temples / front shield), enabling H13-grade filtration and meaningful carbon mass without the user feeling resistance. Position as wellness + exposure-measurement device, not as a respirator, to escape the NIOSH regulatory pathway in early versions.

The product is three things at once sharing hardware:

1. **Protection** — meaningfully reduce BC/PM/VOC exposure during commute windows
2. **Exposure measurement** — onboard particle sensor logs personal exposure tagged to location
3. **Citizen-science data contribution** — aggregated user data becomes a high-resolution mobile BC sensor network, more granular than any fixed-station network could produce

No single one of these justifies eyewear-form-factor hardware. Together they do.

## Architectural decisions

### Decision 1: Eyewear form factor (not nostril-only, not full mask)

**Choice:** Glasses-frame chassis with structural support via ear hooks and nose bridge.

**Reasoning:** Nostril-only designs hit a hard ceiling because filter area is constrained to ~1 cm². Full masks have a compliance ceiling driven by social cost. Eyewear form factor escapes both: filter media lives along the temples or in a front shield (30+ cm² achievable), seal lives at the nostril, and the social register is "fashion-adjacent wearable" rather than "medical device."

**Tradeoff accepted:** More visible than nostril-only inserts. But "head-turning" is acceptable if the design is good — glasses are head-turning when they're well-designed; the goal is to make the device read as deliberate eyewear, not as compromised eyewear.

**Open question:** Cheekbone-routed vs. ear-routed filter housings. Cheekbone is the more visible "design statement"; ear-routing is more invisible and uses geometry every audio earpiece has solved. Probably build both as prototypes and decide from real wear.

### Decision 2: Directed-flow architecture, not plenum

**Choice:** Filter media → cupped volume in front of nose → sealed nostril inserts. Seal at nostrils only, *not* at upper lip / cheek perimeter.

**Reasoning:** A plenum design (sealed cup over nose + lip) concentrates the seal onto the hardest geometry on the face — the philtrum, the wet/dry skin transition, mobile during talking. This is exactly where N95s leak. Directed-flow keeps the seal small and at the nostril interior (the original nasal-filter insight), but moves the filter off the nostril (escaping the surface-area constraint).

**Tradeoff accepted:** Visible cup or shield in front of the nose. Aesthetic register: closer to sci-fi rebreather / deliberate prosthetic than to medical device. This is a feature, not a bug, given the target user.

### Decision 3: Battery-powered (PAPR-style positive pressure)

**Choice:** Small fan continuously drawing air through filter into cup, maintaining slight positive pressure at the nostrils.

**Reasoning:** Powered architecture flips the fundamental tradeoff:

- Pressure budget becomes the fan's problem, not the user's diaphragm — H13/H14 HEPA + pelleted carbon become viable
- Positive pressure makes seal failures *forgiving* — leaks push out, not in. This is why industrial PAPRs don't require fit-testing
- Mouth breathing becomes survivable rather than catastrophic (some clean air still reaches the airway)

**Why the Dyson Zone failure doesn't refute this:** Dyson blew filtered air at an unsealed visor. Particles routed around the airstream. The architecture failed; powered nasal filtration with a real seal does not.

**Tradeoff accepted:** Battery weight on the temples, recurring charge cycle, fan acoustics.

### Decision 4: Continuous-flow with passive exhaust valve, NOT predicted-breath synchronization (V1)

**Choice:** One-way exhaust valve handles exhaled air. Fan runs continuously at low speed.

**Reasoning:** CO₂ dead-space concerns at this plenum volume (~30–60 mL) are not a real engineering problem — continuous flow flushes them passively. A predictive breath-sync system solves a problem that doesn't exist at V1.

**Where breath prediction *does* belong (V3+):** Battery life optimization (demand-flow rather than continuous-flow could 2–3x runtime), acoustic profile improvement, comfort during conversation. Reframe as power/UX refinement, not as a CO₂ solution.

**Tradeoff accepted:** Battery life of continuous-flow V1 is shorter than the eventual demand-flow version. Acceptable for first prototype.

### Decision 5: Onboard particle sensor + BLE to phone

**Choice:** Laser scattering PM sensor at the inlet, BLE link to companion phone app.

**Reasoning:** This is the architectural piece that makes the device interesting beyond personal protection.

- Closed-loop fan control: sensor detects elevated PM → fan ramps to max + logs exposure event. Quiet bedroom → fan idles. Battery and noise both win.
- Exposure logging tagged to GPS becomes the citizen-science data layer.
- Subway-platform mode could auto-engage when sensor detects characteristic underground PM signature.

**Hardware bet:** PMSA003I (Adafruit 4632) is the obvious V0 sensor — already familiar territory. Eventually a more compact module, possibly custom.

**Open question:** Does a proper black-carbon-specific sensor (bcMeter-style optical absorption) fit in this form factor? Likely not at V1; PM proxy is acceptable. Worth revisiting as multi-wavelength bcMeter hardware shrinks.

### Decision 6: Replaceable filter cartridges + active carbon regeneration

**Choice:** Slide-out filter inserts in the temples or shield. Small heating element to mildly heat carbon stage during overnight charging (60–80°C drives off adsorbed VOCs).

**Reasoning:** Cartridge replacement is the obvious subscription-model business layer. Active regeneration is a thing that powered devices can do but passive ones structurally can't — it extends carbon life from months to years and is a defensible technical moat.

**Tradeoff accepted:** Heating element adds complexity and a small failure mode. Worth it for the regeneration capability.

### Decision 7: Wellness device positioning, not respirator

**Choice:** Market as "personal nasal air filter / exposure monitor." Avoid N95-equivalent claims. Defer NIOSH certification to V3+ or never.

**Reasoning:** NIOSH respirator certification is slow, expensive, and forces a different design optimization (worst-case industrial protection rather than commute-window comfort). The Apple Watch playbook applies: position as wellness, let third-party labs publish protection numbers, let users draw protection conclusions.

**Tradeoff accepted:** Cannot legally claim "N95-equivalent." Acceptable — the actual value proposition is "meaningfully reduced exposure during commutes with social cost low enough that you'll actually wear it," not "industrial respirator protection."

## V0 prototype plan

Goal: validate that the architecture is comfortable enough on the actual face to be worth pursuing, before any CAD work.

- Off-the-shelf safety glasses as structural base
- Cardboard cup taped to bridge, ~6 cm wide × 1.5 cm projection, lined with N95 media
- Silicone earplugs trimmed as nostril inserts, connected to cup with short straws
- Adafruit USB fan ($8) as stand-in for integrated fan
- Bench-test particle penetration past inserts at simulated breathing flow rates using bcMeter or PMSA003I

If the V0 hits even 60–70% PM2.5 capture at platform-walking flow rates while being wearable for 10+ minutes, it already beats every nasal filter in the literature and is worth iterating.

## Roadmap (rough, decade-scale)

**V0 — passive cardboard prototype** (weeks): architecture feasibility on actual face

**V1 — 3D-printed powered prototype** (months): replaceable HEPA + carbon, USB battery, single-board sensor, simple threshold fan control

**V2 — integrated wearable** (year): custom PCB, integrated battery, BLE app, modular nostril inserts in three sizes, industrial design partnership

**V3 — refined product** (years): demand-flow fan control with breath sensing, active carbon regeneration, multi-wavelength BC sensor if size permits, citizen-science data layer live

**V∞ — platform** (decade): aggregated mobile exposure dataset becomes a public-health resource. Hardware iterations follow eyewear/audio-wearable industry curves rather than medical-device curves.

## Open risks (named to be re-evaluated)

- **Nostril seal at long durations.** ENT/industrial-design problem. Likely the deciding comfort factor. Modular sizing probably required.
- **Condensation in the airflow path.** Exhale air is saturated at body temp; cold ambient air hits cold tube interior. Solvable (hydrophobic coatings, heat exchange) but not free.
- **Industrial design quality.** The aesthetic thesis only works if the design is genuinely good. This is the single largest non-physics risk and the place where a partnership matters most.
- **Closed-mouth breathing requirement.** Acceptable to project owner (Brian); compliance ceiling for general users unclear. The positive-pressure architecture softens this but doesn't eliminate it.
- **Regulatory drift.** If the FDA or EPA reclassifies "nasal exposure-reducing wearables" into a stricter category, the wellness positioning could be lost. Worth tracking.

## Sources & evidence base

- BMJ Open 2022 meta-analysis (PMID 35504636): BC causally linked to cardiovascular and respiratory mortality
- WHO Europe 2012 BC report: BC as universal carrier of co-pollutants; cardiopulmonary effect evidence sufficient
- Columbia / EHP studies (PMC 3685864, EHP 2021): NYC neighborhood asthma prevalence correlates with truck-route density and infiltrated indoor BC
- NYU Langone / EHP 2021 (Luglio et al.): subway platform PM concentrations 2–7× outdoor; PATH Christopher St up to 77×
- Tandfonline 2021 nasal filter test: existing nasal filter PM1 penetration >80% under realistic breathing
- Sciencedirect S1309104223001940: nasal filters deliver 2.5–17.6× higher inhaled PM10 dose than face masks
- bcmeter.org: open-source BC sensor, R² 0.77–0.83 vs. reference instruments — relevant reference for V3 sensor integration
