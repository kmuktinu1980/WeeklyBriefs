---
date: 2026-10-10
domain: Space & Orbital Compute
deep_dive: Radiative cooling — why heat sets the economics of orbital compute
doc: https://claude.ai/code/artifact/d40ff729-ca96-47b2-abea-40d1050f0e6d
---

# Weekly Brief — Oct 10, 2026: Heat is the real wall in orbit

Each megawatt of orbital compute needs roughly 1,000 m² of radiator and 3,000 m² of solar array, so launch cost per kg decides whether space data centers ever beat the ground.

## Deep dive: Heat is the real wall for data centers in space

### The hook
On **October 1, Google launched its first TPU into orbit** on a Planet Labs–built satellite riding a SpaceX rideshare. The detail that caught my eye: after commissioning, the TPU will run only in **15-minute bursts** so it doesn't overload the satellite's power and thermal systems. That chip draws about **1 kW**. The same day, Google's team published a peer-reviewed paper in *Joule* estimating that a path to roughly **$200/kg launch by 2035** would need Starship to fly about **1,800 times over 10 years** (~180/year; Starship has never exceeded five flights in a year).

Most of the debate focuses on launch cost. The 15-minute-burst detail points at the constraint that physics, not markets, sets: **getting rid of heat**.

### The concept, from first principles

**1. In space there is no air, so there is no convection.** On Earth a data center dumps heat into air or water that carries it away. In orbit the only way to shed heat is to *radiate* it as infrared light. Radiated power follows the **Stefan–Boltzmann law**:

> **q = ε · σ · T⁴**
> q = watts radiated per m² of surface · ε = emissivity (~0.9 for good radiator coatings) · σ = 5.67×10⁻⁸ W/m²K⁴ · T = radiator temperature in kelvin

The T⁴ is the whole story. Radiators are weak at room temperature and get dramatically better as they run hotter.

**2. Worked example: radiator area for 1 MW of compute.**
Assumptions (stated so you can challenge them): a flat panel radiating from both sides, held edge-on to the sun; ε = 0.9; ~150 W/m² of absorbed Earth infrared and reflected sunlight subtracted; every watt of electricity becomes heat.

| Radiator temp | Per side | Net, two sides | Area for 1 MW | Mass at 5–10 kg/m² |
|---|---|---|---|---|
| 300 K (27 °C) | 413 W/m² | ~680 W/m² | ~1,480 m² | 7–15 t |
| 330 K (57 °C) | 605 W/m² | ~1,060 W/m² | ~940 m² | 5–9 t |
| 360 K (87 °C) | 857 W/m² | ~1,560 W/m² | ~640 m² | 3–6 t |
| 400 K (127 °C) | 1,306 W/m² | ~2,460 W/m² | ~410 m² | 2–4 t |

**Reading the table:** a GPU or TPU junction runs at about 85 °C, and heat must flow *downhill* from chip → cold plate → fluid loop → radiator. So a realistic radiator sits around **330 K**, which means about **1,000 m² of radiator (roughly a quarter acre) per megawatt**. A 100 MW orbital cluster, small by terrestrial AI standards, would need ~0.1 km² of radiator.

**3. Power is the other half, and it's also area.** Sunlight in orbit delivers ~1,361 W/m². At ~30% cell efficiency and 85% packing, 1 MW needs **~2,900 m² of solar array**. So each megawatt in orbit is roughly **3,000 m² of solar plus 1,000 m² of radiator**, all of which must be launched, deployed, pointed and kept alive for five years.

**4. Why this turns into money.** At 5–9 t of radiator per MW:
- At today's Falcon 9 ~$2,700/kg → **$13–25M per MW just to launch the radiators**
- At $1,000/kg → **$5–9M per MW**
- At Google's 2035 target of $200/kg → **$1–2M per MW**

For scale, building a terrestrial data center commonly runs on the order of ~$10M per MW all-in (my own rough figure, not from this week's sources). So **radiators alone can exceed the entire terrestrial build cost until launch gets well below ~$1,000/kg**. This is why every orbital-compute business plan is secretly a bet on Starship.

### The four engineering levers (and their catch)
1. **Run hotter.** Doubling absolute temperature gives 16× the radiated power. *Catch:* chips degrade and leak more current when hot; you need hardware designed for high-temperature operation, or a heat pump.
2. **Heat pumps.** Like a refrigerator in reverse, a heat pump lifts heat from 60 °C coolant to a 130 °C radiator, shrinking the radiator. *Catch:* it costs electricity (set by its coefficient of performance, or COP). That electricity becomes more heat, which needs more solar array. There's an optimum, not a free lunch.
3. **Lighter radiators.** Deployable thin-film or carbon-composite panels push kg/m² down. *Catch:* micrometeoroid punctures in fluid loops, and five-year degradation of coatings (ε drops, solar absorption rises).
4. **Orbit choice.** Dawn–dusk sun-synchronous orbits keep panels in near-constant sunlight (no batteries for eclipse) and let radiators face deep space. *Catch:* that orbit is getting crowded, and every serious player (Starcloud, Google, SpaceX) wants it.

### Where the hype outruns reality
- **"Free cooling in space"** is the most common error in pitch decks. Space is cold, but vacuum is a *perfect insulator*. You can't touch the cold. You can only glow at it.
- **The cost numbers don't agree.** Starcloud projects energy at about $0.005/kWh, while Lonestar's claimed operating savings imply about $0.001/kWh. A Varda engineer's public calculator puts orbital compute at roughly **3× the cost per watt** of ground in the base case. Treat any single headline cost figure as a marketing number until you see the radiator mass assumption.
- **Solar efficiency claims of 40–50%** appear in some industry write-ups. Fielded space-grade multi-junction cells are typically closer to ~30% (my own knowledge). That gap alone changes array area by ~40%.
- **Radiation is real but manageable for inference.** Google's re-run accelerator tests showed slightly more logic errors than first measured, yet still about one error in a million for typical inference. Long training runs across thousands of chips are where it hurts.

### Why it matters to you
- **Starcloud diligence (Fuse co-invest).** The three questions that separate a real plan from a slide: (1) radiator **kg per kW** and the assumed radiator temperature, (2) the launch $/kg in the base case and the year it's achieved, (3) end-of-life ε and solar degradation. If a deck doesn't state these, the economics aren't yet underwritten.
- **Steel thread to your power thesis.** You've argued that power, not GPUs, is the binding constraint on the ground because grid interconnects take years. Orbit swaps the *permitting* constraint for a *thermal-area* constraint. It doesn't remove the constraint, it converts it into launch mass. The winning business is whoever drives kg/MW down fastest.
- **Founding-team angle.** Thermal and power engineering for compute (high-temperature-tolerant silicon, two-phase loops, deployable radiators) is an under-staffed specialty. Teams that pair ML-infrastructure people with thermal engineers are rare.

### Go deeper (ranked)
1. **Google's Project Suncatcher design paper** (arXiv, Nov 2025; peer-reviewed version in *Joule*, Oct 2026). The best primary source on formation flying, TPU radiation testing and launch learning curves. *~2 hours.*
2. **NASA "State-of-the-Art of Small Spacecraft Technology," Thermal Control chapter** (free, NASA). A survey of real radiators, heat pipes and coatings with flight heritage. *~1.5 hours.*
3. **Build the model yourself.** A 50-line Python notebook: inputs = compute MW, chip temp, ε, kg/m², $/kg; outputs = radiator area, mass, launch $. Then add a heat pump with COP and find the optimum radiator temperature. *~2 hours, highest learning per hour.*
4. **Gilmore, *Spacecraft Thermal Control Handbook*, Vol. 1, chapters on radiators.** The standard reference. *Dip in as needed.*
5. **MIT OCW 16.851 Satellite Engineering**, power and thermal lectures. A useful companion to your astrodynamics material. *~3 hours.*

### Self-check
1. If a startup doubles its radiator temperature from 300 K to 600 K, by what factor does radiated power per m² rise, and why might it still not halve the system cost?
2. Why does adding a heat pump *increase* the solar array size even as it shrinks the radiator?
3. Google limits a 1 kW TPU to 15-minute bursts. What does that tell you about the satellite's thermal mass and radiator sizing?

---

## Breadth

### Physical AI
- **Humanoid shipments are now a China story.** IDC estimates ~25,000 humanoids shipped worldwide in H1 2026 (up ~432% YoY), with China at ~78% and AGIBOT alone above 8,600 units. *Why it matters:* the data flywheel (robots deployed → data → better policies) is spinning fastest in China, the same dynamic you saw with Amazon Go's deployment-driven data.
- **Boston Dynamics showed a new Atlas hand:** four fingers, 13 directly actuated degrees of freedom, dense fingertip and palm tactile sensing. *Mechanism:* direct actuation (a motor per joint, not tendons) trades weight for precise force control. Tactile sensing is what lets learned policies handle tool use, where vision alone fails at the contact moment.
- **Runway entered robotics with Praxis-1,** a "world-action model" pretrained on third-person video (open weights promised, not yet released). *Why it matters:* this is the "learn from YouTube" bet. Human video is abundant, but it has no action labels and the bodies don't match a robot's, so the open question is how much of it transfers.
- **Nucleus published uncut factory footage and self-reported 60% autonomy / 40% teleoperation.** Rare honesty. Use it as a calibration point when other demos don't disclose teleop share.

### Agent context engineering / Data Cloud
- **Graph structure measurably cuts hallucination.** Research highlighted by Neo4j (mid-September) found a lightweight graph halves hallucination versus vector-only RAG, with multi-hop answer rates rising from 29% to ~66%. The researcher flags it may not hold on messy enterprise data. *Your angle:* CRM's pre-resolved entity identity is exactly the "reliable links" condition that study needed.
- **Snowflake's ontology-grounded Cortex Agents benchmark** (May) is still the clearest public comparison: semantic view 50% → knowledge graph 60% → GraphRAG 70% → GraphRAG + **hand-curated** terminology mappings 78%. *Gap check:* the last eight points came from manual curation stuffed into the system prompt, with no curation UI and no incremental refresh. That validates your "triple-curation workflow" and "incremental graph refresh" gaps directly.
- **Neo4j's three-pass agent memory pattern:** capture sessions → distill tool failures into reusable fix patterns → gate durable "learnings" behind contradiction checks and human review. A useful reference design for Agentforce agent memory.

### AI moonshots
- **Chemistry Nobel: mirror molecules.** Henri Kagan (nonlinear effects, 1986) and Kenso Soai (asymmetric autocatalysis, 1995). In Soai's reaction the product catalyzes its own formation, amplifying a 0.00005% excess of one mirror form to over 99.5% in three rounds. *Why it matters:* many drugs work only in one mirror form, and it's a leading explanation for why life uses only one "hand." Generative chemistry models that ignore chirality produce plausible-looking but wrong molecules.
- **Physics Nobel: Francis Halzen and IceCube.** 5,160 light sensors buried ~2 km in Antarctic ice detect the blue Cherenkov light from rare neutrino interactions. It is the founding instrument of neutrino astronomy. *AI tie (my own knowledge):* IceCube is a showcase for graph neural networks, because event reconstruction is a sparse, irregular 3-D point cloud.

### Strategy & markets
- **CRM is down ~11% YTD** (closed $234.69 on Oct 2) at ~14× forward earnings, despite Agentforce ARR above $1.5B and Q2 FY27 revenue of $11.3B (+11%, including $456M from Informatica). Non-current debt rose to ~$39B after a $25B issuance funding buybacks. *Steel thread:* the market isn't paying for agents yet. A re-rating likely needs *organic* growth to re-accelerate, not ARR headlines. This is the "seat cannibalization" SaaSquatch showing up in the multiple.
- **India AI funding: $438M in Q3 2026 (+265% YoY, 35 deals),** led by Emergent's $130M. Advanced hardware including robotics and semiconductors raised $290M (+176%). *Caveat:* investors say that outside a few big rounds funding hasn't moved much, and ~63% see froth.

---

## Sources
- [Google thinks Starship has to launch 1,800 times before space data centers get off the ground](https://techcrunch.com/2026/10/01/google-thinks-spacexs-starship-has-to-launch-1600-times-before-space-data-centers-get-off-the-ground/) — TechCrunch, Oct 1, 2026
- [The Orbital Data Center Race](https://introl.com/blog/orbital-data-centers-space-computing-race-2026) — Introl, Feb 2026 (vendor blog)
- [Space data centers: Starcloud, SpaceX and Project Suncatcher explained](https://www.fierce-network.com/cloud/space-data-centers-starcloud-spacex-and-project-suncatcher-explained) — Fierce Network, Mar 2026
- [RobotToday Weekly, Sep 28 – Oct 2, 2026](https://robottoday.com/article/robottoday-weekly-september-28-october-2-2026)
- [This Week in Neo4j: NODES 2026, Agent Memory, GraphRAG](https://neo4j.com/blog/twin4j/this-week-in-neo4j-nodes-agentmemory-graphrag-knowledgelayer-and-more/) — Sep 18, 2026
- [Ontology in Snowflake: Building Cortex Agents](https://www.snowflake.com/en/blog/engineering/ontology-grounded-cortex-agents/) — May 25, 2026
- [The 2026 Nobel Prize in Chemistry goes to Kagan and Soai](https://axial.acs.org/multidisciplinary/nobel-prize-chemistry-2026) — ACS Axial
- [AIP congratulates 2026 Nobel Prize winner in Physics](https://www.aip.org/aip/aip-congratulates-2026-nobel-prize-winner-in-physics) — AIP
- [Salesforce (CRM) trails the market in 2026](https://finance.yahoo.com/markets/stocks/articles/salesforce-crm-trails-market-2026-210621882.html) — Yahoo Finance, Oct 7, 2026
- [Indian AI startup funding surges 265% YoY in Q3](https://inc42.com/features/indian-ai-startup-funding-surges-265-yoy-in-q3-will-momentum-continue/) — Inc42, Oct 9, 2026
- Radiator and solar sizing: own Stefan–Boltzmann calculation with the assumptions stated above
