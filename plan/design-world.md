# Millrace · IronStone THREE world atlas

Stand just inside the south gate. Straight ahead, the clock face is a pale disc against a seam of red rock. To the right, a barn sticks one black timber out over the landing like a fishing rod. Beyond it, a wheel dips its paddles into the millrace. A person giving directions has three good sentences already. Meet under the clock. Collect at the hoist. Take repairs to the wheel.

![Arrival key painting looking north. The counting house is on the left, market arcade on the right, clock ahead, receiving hoist and millwheel upstream to the right.](millrace-arrival.jpg)

*Arrival study.* The painting sets the silhouette, warmth and order in which the eye finds things. The overhead plate sets walking distances and door locations. Frame the same composition at the game camera’s default 50-degree pitch, with adjustable zoom. Keep the foreground street clear even when the square is busy.

Millrace is a proposed working town for the BOSS divisions, their PASS counters and the framework apps. This atlas supplies the exterior for T1, three furnished interiors for T3, and the building sheets for the rest. The [concept](concept.md) covers the purpose and the [technical design](design-technical.md) owns rendering, services and deployment. The [building sheets](design-world-buildings.md), [first interiors and review walk](design-world-interiors.md) and [Assist characters](design-world-agents.md) are parts of this atlas.

## 1. Town plan

![Numbered site plan with a north arrow, street doors, scale and the two business routes. Building names and accessible routes follow below.](millrace-plan.svg)

The dome spans about 180 m. The settled ground occupies roughly 130 × 110 m on the technical design's metre grid. North is up. The ironstone ridge closes the north skyline. The water enters from the northeast, drops at the mill, slows at the receiving landing and leaves to the southeast. Keep all everyday destinations on the near bank. Ridge and river are scenery with low coping stones at the water. A gentle rise toward the clock gives the square a head and a foot. All main streets have a continuous level or ramped line.

Read the plan from the south. The gate opens onto Market Street. The covered market sits to the east, the counting house to the west. Straight on is the square with the clock house at its northern end. Turn east there for the landing and follow the water upstream to the mill yard. The western cross street leads past the post loft to the proof yard, then into the works. The Forum steps open off the gate road. The lower, paved side of each stepped gathering place has seats and room for another person alongside.

| Walk from threshold to threshold | Approximate distance along the drawn streets | What guides the last turn |
| --- | ---: | --- |
| Gate to square centre | 37 m | Clock face stays ahead |
| Square to Billing | 18 m | Black shutters and brass coin above the west counter |
| Square to Warehouse | 51 m | Empty hoist on the skyline, staff door on the landward side |
| Square to Smithy | 42 m | Post loft, then the double chimney on Proof Lane |
| Receiving barn to mill | 76 m | Turn inland at the barn, then north beside the scales to the wheel |
| Square to market meeting porch | 31 m | Two facing benches at the market arcade |

Allow 4 m for cart streets, 3 m for side lanes and at least 2 m clear past a sign or bench. Give a portal a level landing on both sides, with benches and interaction points recessed from the line of travel. Keep roofs below the sightlines from the gate to clock, hoist and wheel. Use the close and district zoom settings to check the three gaps. Interior roofs are omitted and near walls are low in the [camera design](design-technical.md#12-movement-camera-and-physics). A discoverable feature never depends on seeing over another building.

**Two trades share one street.** Follow the broken ochre line from Sales at the bargain hall through the square to Warehouse for picking and shipping, then back to Accounting for invoice and receipt. Shop at the market is the storefront branch. Help and returns come back through the mending porch. The green dotted route starts at Purchasing, crosses its own Supplier counter for acknowledgement, then runs down to Receiving and Putaway. The bill comes in at the factors' counter and matching and payment belong to the counting house. Brass pavement inlays bear a folded order or a tied bale so the routes still differ in grayscale. Names, all intermediate steps and authorized record links live on the actual Insights maps. The inlays teach a route and execute no work. [Customer flow](../../mr-boss/report/insights-flowmap-customer.json) · [Supplier flow](../../mr-boss/report/insights-flowmap-supplier.json) · [Internal flow](../../mr-boss/report/insights-flowmap-internal.json)

## 2. Art direction

| Element | Direction for the artist |
| --- | --- |
| Stone, wood and cloth | Ironstone **#AB694C**, limewash **#F4E8CE**, sawn oak **#987047**, river green **#587E70**, writing ink **#243734**. Use ochre roofs on the market, slate-blue ridges on the works and bare timber ends in the mill yard. Repeat the same cornerstone and door hardware across town. |
| Silhouette and scale | People about 1.7 m tall, with large hands, broad cuffs and simple faces. A street door is 2.4 m high. Most eaves sit at 3–4 m. Clock tip 14 m, hoist tip 8 m, wheel 5 m across. Exaggerate the tool by which a building earns its living. Let changing roof shapes do the work of extra textures. |
| Lettering | Paint the trade emblem large enough to read from across the square. At approach show the exact localized app name on a level, screen-facing label in the shell typeface, at least 16 CSS px, ink on opaque limewash. Draw the same label on focus. Keep the full name in the directory when a distant sign folds to an emblem. |
| Books, signs and doors | A pair of brass corners and an ink rule frame every flat working screen. The frame carries the app title, the return place and a labeled Close control. A projecting book spine means open a screen. A pin or dial means read a value. A scalloped speech mark identifies an Assist character. Use the same marks beside directory entries. |
| Light and sky | Late-morning light from the southwest leaves door faces legible. Cloud bands painted inside the dome drift only on higher tiers. The clock may show local time with the zone in its label. Weather has no business meaning. |
| Movement and sound | Let the millwheel turn for pleasure and the pigeon on the counting-house roof change feet. A selected book squares itself against its shelf with one soft tap. Keep motion local and brief. Reduced motion uses the settled pose, labeled focus and immediate travel. Water, timber creak and paper are optional ambience. All speech and effects have a silent route. |

The Titchfield hall's open ground arcade is why our market has room to talk while the road stays clear. The Lurgashall wheel drove several jobs including a sack hoist. Here the wheel identifies making and the separate waterside jib identifies receiving. Waterside warehouses placed loading directly against the boat. Give the barn its projecting tackle on that face and its pedestrian entrance on the other. [Titchfield Market Hall](https://www.wealddown.co.uk/buildings/market-hall-from-titchfield/) · [Lurgashall Watermill](https://www.wealddown.co.uk/buildings/watermill-from-lurgashall/) · [Canal & River Trust warehouse study](https://canalrivertrust.org.uk/media/document/OxyVe7XLOA0NPhVmzYkFRA/yiOhK5j3PwcxZwUiOezx2Pbc7CbqNR6xhMgVN5T7A78/aHR0cHM6Ly9jcnRwcm9kY21zdWtzMDEuYmxvYi5jb3JlLndpbmRvd3MubmV0L2RvY3VtZW50Lw/018f9b65-60c8-76bf-9933-adfa3fecba67.pdf)

Take three gestures from the games. *Job Simulator* puts the thing you want to use within reach. Tip the nearest ledger toward the hand or focus ring. *Carry On Together* makes awkward loads funny. Let one corner of the sling catch, bob once, and settle on selection without altering a receipt. *Human Resource Machine* gives successive operations fixed places. Keep issue, changes and checks in the same order along the proof bench. The skyline uses the same principle at a distance, with each landmark occupying its own gap. [Owlchemy Labs](https://jobsimulatorgame.com/) · [Carry On Together](https://store.steampowered.com/app/4776500/Carry_On_Together/) · [Tomorrow Corporation](https://tomorrowcorporation.com/humanresourcemachine)

### Kit selection

The following files and the included CC0 license texts were checked at the linked creator revisions. Counts are indexed triangles in the source meshes, before instance reuse or conversion. Each sampled asset uses one material.

| Kit and selected files | Treatment | Source and license |
| --- | --- | --- |
| KayKit Medieval Hexagon 1.0, blue watermill (1,921), market (3,125), blacksmith (2,410) | Separate the structures from their tile footprints. Snap entrances to the square metre grid. Recolor one variant. Add the clock face, hoist and proof-yard chimney as distinct pieces. | [Buildings](https://github.com/KayKit-Game-Assets/KayKit-Medieval-Hexagon-Pack-1.0/tree/84fa4e91af6a88989be7c99e0891cede11f2ca38/addons/kaykit_medieval_hexagon_pack/Assets/gltf/buildings/blue) · [license](https://github.com/KayKit-Game-Assets/KayKit-Medieval-Hexagon-Pack-1.0/blob/84fa4e91af6a88989be7c99e0891cede11f2ca38/LICENSE.txt) |
| KayKit Furniture Bits 1.0, book_single (68), chair_stool_wood (216), table_medium (168) | Enlarge the spine slightly and reuse it for working books. Build the adjustable sling, folding ledger, proof tags and state marks from simple geometry. | [Furniture](https://github.com/KayKit-Game-Assets/KayKit-Furniture-Bits-1.0/tree/96d5930a8dbdb363409bbc2d3341718b00e17c9c/addons/kaykit_furniture_bits/Assets/gltf) · [license](https://github.com/KayKit-Game-Assets/KayKit-Furniture-Bits-1.0/blob/96d5930a8dbdb363409bbc2d3341718b00e17c9c/LICENSE.txt) |
| KayKit Adventurers 1.0, Rogue.glb (6,377), skinned and animated | Use the Rig as the first proportion and retargeting sample. Give the civilian figure an apron or plain coat. Keep locomotion and social clips, with a simplified distant figure. | [Character](https://github.com/KayKit-Game-Assets/KayKit-Character-Pack-Adventures-1.0/blob/672074b73ba276876a19e8816ecdc5241817ab47/addons/kaykit_character_pack_adventures/Characters/gltf/Rogue.glb) · [license](https://github.com/KayKit-Game-Assets/KayKit-Character-Pack-Adventures-1.0/blob/672074b73ba276876a19e8816ecdc5241817ab47/LICENSE.txt) |

These are art selections. T1 measures the converted exterior against the existing targets of 5 MB transferred, 150 draw calls, 300,000 visible triangles, 64 MB GPU memory and 30 fps on the target phone. Each interior adds at most 2 MB of transfer as a target. Instance the repeated fittings and prioritize readable silhouettes over roof trim. The arrival painting is a concept illustration for this document. [Rendering budgets](design-technical.md#5-rendering-core) · [Asset pipeline](design-technical.md#15-assets)

## 3. Buildings and thresholds

The [building register](design-world-buildings.md) uses the numbers on the plan. Its bays use the current app menus and its objects cite the screens that supply them. Each building has a way out at the arrival anchor. A lantern bracket on its **street side** marks a separate meeting-room door, including on buildings whose staff door is locked. An organization binding shows its banner only to members. Public-only places at the gate and Forum use the same street bracket.

Use one village per installation, with one configured PASS site for its counters. Organization banners, room membership and app readings follow the viewer’s active scope. The village grant admits someone to the street and offered front counters. The counter opens the configured site's PASS mount. Accounts, invitations, publication and repository policy decide what the person can read there. The back door uses the staff app grant. Interior doors use the section's actual authorization and menu condition. A hidden area becomes plain wall. For a private-only app the street threshold is the lock. Omit a PASS counter when the selected site lacks its mount. A paired building then uses its staff entrance. A building entirely supplied by an absent component becomes garden or ordinary frontage, and its directory entry disappears.

Fit the paired house in a 32 × 24 m rectangle. Give the front strip 7 m, with counter on the left, staff portal in the middle, meeting door on the right. A tinted, stippled pane beside the portal lets staff see out. Seen from the public side, it can show anonymous moving figures once T4 exists and unchanging outlines of furniture. No workload-driven movement, colored status, names, work sounds or working screens cross it. An opaque installation setting replaces the whole pane. T3 looks like a furnished room at rest. The private-only house has an open central floor and bays on three walls. The register gives their order clockwise from the entrance, grouped on shared furniture where appropriate. Make room for the Settings cupboard by the exit wherever the app offers Settings.

Shared floor key for the smaller building sheets. A numbered wall shelf is the next group of menu books in a sheet’s clockwise list. Move the furniture to match the trade, keeping the central aisle and arrival sightline clear.

```text
      PAIRED HOUSE                        STAFF HOUSE
┌──────────────────────────┐       ┌──────────────────────────┐
│ shelf 2       secured bay│       │ shelf 2       secured bay│
│ shelf 1   Home    shelf 3│       │ shelf 1   Home    shelf 3│
│ Settings       staff door│       │ Settings       staff door│
├── privacy pane ─────╥────┤       └───────────────────╥──────┘
│ counter        meet door │                       staff IN
└────────╥────────────╥────┘                 separate street door → room
      public IN      room
```

## 4. Public life and navigation

**At the gate.** A visitor chooses Enter Village, lands facing the clock, and sees the street directory on a tilted map table. A brass swallow perched on its edge can explain the signs. The table itself always works. Select by app name, by trade name or by destination such as “My invoices”. Travel places the person at the appropriate accessible anchor. Selecting a screen opens its real page. A persistent Square command and the shell palette work from every street and room. The closest matching authorized bay owns a screen opened through search. Close the overlay to resume at that bay, or at the object that originally opened it. PASS initially opens in its own tab at the selected site. Preserve the village tab and focus on the same object when the person returns. [Shell navigation](design-technical.md#7-the-village-as-a-shell-layer)

**Under the arcade.** Two people can stand at the bench and hear each other if they choose to listen and speak. In the first audio phase anyone in the exterior space may receive their voices. The street meeting door leads to a separate chat room with its own call. At a public counter, “Meet here” opens the bound room if the viewer may join. Until a staffed invitation or paging source exists, a little counter bell offers the room or the real support/contact page. It cannot promise that a clerk is coming. Room sign states are Available, In session, Unavailable and Private, using the call's state only for people allowed to read it. The Travellers’ table and Speakers’ room are the two general meeting places. Bind the street brackets at division buildings to the division’s chat rooms, with explicit membership. A six-seat ring suggests an ordinary meeting, a twelve-seat hall a larger one. Represent further authorized participants in the roster. Neither number caps the call.

**In a meeting.** Someone joining from the ordinary call screen appears on the same roster, with a little desk plaque in the room where a 3D figure would sit. An empty chair means a place to sit. It makes no claim about other chat-room members. Staff use registered names. An external user chooses “Visitor” or their registered real name for everyone. Bodies, colors and hats are cosmetic. Use text plus a small badge for away, do not disturb, in a call and speaking, as supported by presence/call state. The microphone and listening start independently, both off. Each enclosure has its own audio. [People and sound](design-technical.md#14-audio-and-meetings)

**On the ridge.** The Commons clock has a large readable face and a row of seven ceramic day tiles. Its public face can always indicate the day. Selecting it opens the directory at Commons Calendar, if allowed. Personal events appear only on the viewer's near label or inside the clock house. The square notice board uses explicitly shared dashboard panels or visible Commons items. A small plate gives the title, source and time. In a later event, an authorized operator binds a meeting presentation to the dome. Only the meeting's authorized audience elects to watch or hear it. Everyone else retains the daytime sky. The sky carries no system-health information.

**On a bench or in the terminal.** The text directory has the same named places, objects, six data states, locks, room bindings and Assist actions as the world. It follows app order as an alternative to district order. Search, Tab, Enter and Close cover the entire journey. Navigating to a closed screen restores focus to its launching control. On a phone a real work screen fills the display, with Return to village in reach. A Maps link from the table appears when the framework supplies Maps. The village diagram and direct travel are available at T1.

## 5. The village NPCs

A specialist lives where someone would naturally seek advice. At the gate a brass bird reads fingerposts. At the barn a measuring wheel balances a question in its pan. Counterfoil is a little book tethered to the public cashier's desk with green ribbon. The Smithy's press sets a line of type when someone asks it to read a change. In the survey loft an angled mirror lets a manager turn a number over and see where it came from.

Each is an Assist agent with its own curated **AGENT.md**. The brief, the physical station and the directory entry identify the same specialist. Select **Ask** to address one privately. Motion signals the local interaction with Assist. The ordinary book, door or instrument next to the character still opens its screen directly. See the [cast, instruction files and binding design](design-world-agents.md).

## 6. Functional decorations

| State | What the person sees | What the directory reads |
| --- | --- | --- |
| Value | Object at a level, with shape and text for its condition | Actual value, unit, scope and as-of time |
| Zero | Tidy empty cradle, exposed base and small round zero seal | “0 waiting” (or the correctly formatted zero amount) |
| Missing | No variable insert. A fixed label can still open the underlying screen | “No reading configured” |
| Withheld | Closed cover on a known, visible fixture | “Restricted”. An absent place or unseen back zone contributes no entry at all |
| Stale | Last permissible level, striped dust sleeve and small clock | “Last known …”, value and time. Recheck scope before redisplay |
| Failed | Folded wooden trestle with a wrench shape | “Reading unavailable”. The working screen can still be opened if permitted |

Every live object is evaluated as the viewer in the current organization, site, store, facility or repository. Personal work comes first. A common street object that carries private data draws a different insert for each authorized person. The exterior hoist always carries one empty sling. Its twin inside Warehouse can carry a variable load. A permission or organization change clears old readings. Keep inactive readings, partial results and a genuine zero distinct. Use the source's own condition and filtered destination. A stack suggests magnitude and the near label gives the exact value. Each directory listing names both its object and its real destination. [State contract](design-technical.md#10-functional-decorations)

Use two common fittings wherever the source and destination are available.

| Fitting | Binding and destination |
| --- | --- |
| Personal approvals bell on the clock-house desk | The shell’s viewer-level approvals count lifts the clapper. A single tap on selection opens the approvals section of Commons/Notifications. Keep an app-specific approvals bell scoped to its own topics. [Shell counts](../../../base/framework/service/org/moqui/ui/ShellServices.xml) · [Approvals list](../../mr-boss/screen/Commons/Notifications.xml) |
| Room sign beside each meeting door | Bind the room to its chat room and read org.moqui.call.CallServices.get#Call as an eligible member. Show state and participant count only if returned for this viewer. Select the real room/call. T5 can poll until the room-state push exists. [Meeting bindings](design-technical.md#14-audio-and-meetings) |

**Proposed adapter work.** Several Home services preserve a null metric when a child read is unavailable. Warehouse, for example, initializes queue readings to null and fills them after successful facility reads. A null alone cannot distinguish an unseen metric, incomplete setup and a failed read. Carry authorized scope, capability, read outcome and as-of alongside a live insert. Until the adapter can establish a reason, label it “Reading unavailable” and offer the source screen. Never manufacture a zero or retain the previous viewer’s value. [Warehouse source](../../mr-boss/service/ironstone/warehouse/HomeServices.xml)

## 7. Handoff

T1 takes the street plan, silhouettes, public names, anchors, directory and doors. T3 takes the three furnished [interiors and four checked paper walks](design-world-interiors.md) plus the shared object states. T4 supplies people and veiled figures. T5 supplies actual meeting and ambient audio. External grants and framed PASS pages follow T6. Specialist bodies and their direct screen links can be placed with their room. Conversation is enabled when Assist offers that specialist to this user. Until the village-to-Assist binding is built, a body can be a quiet landmark. These design checks cover documents and sources. Measured performance, reachability in a running world and multi-user behavior belong to their implementation phases.
