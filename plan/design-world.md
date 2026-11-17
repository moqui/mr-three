# IronStone THREE — World design

November 17, 2026 — skeleton; nothing designed in detail and nothing built. This file will hold the layout of the
village, the building and interior for each application, and the functional decorations of every space. The
[concept](concept.md) is its fixed input and the [technical design](design-technical.md) says how a layout is
stored and drawn. Sections marked **To design** are for a later effort; the tables show the shape to fill, and rows
marked *example* are illustrations, not decisions.

Sections: [1 how to fill this](#1-how-to-fill-this-document) · [2 art direction](#2-art-direction) ·
[3 village plan](#3-village-plan) · [4 building register](#4-building-register) ·
[5 interiors](#5-interiors) · [6 functional decorations](#6-functional-decorations) ·
[7 global decorations](#7-global-decorations) · [8 meeting rooms](#8-meeting-rooms-and-gathering-places) ·
[9 avatars and villagers](#9-avatars-and-villagers) · [10 navigation aids](#10-navigation-aids) · [11 open](#11-open)

---

## 1. How to fill this document

- **One space at a time.** A space is the village exterior or one building's interior. Each gets a plan, a list of
  places (doors, bays, rooms) and a functional decoration table ([§6](#6-functional-decorations)).
- **Start from the app's menu and Home.** An interior's bays are the app's top-level sections; its first decorations
  are the counters and work lists the app's Home already shows. Nothing in the world needs data the app does not
  already produce, unless this file says so and the technical design lists it as a need.
- **Decide, with a source.** Each decoration names the screen it opens and the service or dashboard panel its state
  comes from, checked against the apps as the BOSS designs check names against Mantle.
- **Every decoration earns its place.** For each one, say what it gives access to or what state it shows. A prop
  that does neither is filler: allowed where a space would otherwise look empty, and never where people walk.
- **Split when it grows.** When this file gets long, a building moves to `design-world-<app>.md` and its row in the
  register links to it.

## 2. Art direction

**To design.** The rules it must keep are in [concept §4](concept.md#4-design-principles): soft angles, comical
style, low stress; beautiful but simple; low poly; few textures.

| Topic | To decide |
| --- | --- |
| Palette | One palette for the whole world, with an accent per district or app; how status colors (late, blocked, done) stay readable against it and for color-blind users |
| Shapes | Proportions of buildings, props and people; how much exaggeration |
| Lettering | Sign and banner fonts; a readable minimum size at the usual camera height |
| Screen frames | The border that marks a real 2D screen: one per origin (book, sign, door), or one for all |
| Animation | The idle life of a space; the slapstick tone for work (carrying, stacking, dropping) and its limits |
| Light | Time of day, shadows and the dome's sky |
| Sound | Ambient sound and effects, and the off switch |

The base art is CC0 low-poly packs recolored to this palette, with code-built pieces for the functional decorations
([design-technical §15](design-technical.md#15-assets)). Choosing the packs is part of this section.

## 3. Village plan

**To design.** A plan drawing goes here.

- **The dome:** its size, what is painted on it, and where the events screen is.
- **Terrain:** the small rises and dips, water if any, and paths that keep walking distances short.
- **The large feature:** what it is (a hill, a cliff, a great tree, a castle rock), on which side, and which global
  decorations it carries ([§7](#7-global-decorations)).
- **The square:** where people arrive, with the signposts, the notice board and the general meeting rooms.
- **Districts:** buildings grouped as the BOSS launcher groups its apps, so the village reads as the business does.

| District | Apps | Character |
| --- | --- | --- |
| Workspace (the square) | Commons, Comms | To design |
| Customer-facing | Sales, Marketing, Customer Service, Commerce | To design |
| Supply and operations | Products, Purchasing, Warehouse, Manufacturing, Maintenance | To design |
| Finance, people and projects | Accounting, People, Projects | To design |
| Management | Insights, Admin | To design |
| The gate | PASS Account, Members and Sites; PASS Forum on or near the square | To design: where an external visitor arrives and where the community gathers |
| The works | System, Tools, Dashboards, Dev AIDE | To design; how much of these fits is open in [concept §11](concept.md#11-open) |

Insights' business flow map already draws how the apps connect. The roads between buildings should follow it where
they can (order to cash, procure to pay), so that walking the village teaches the flow.

## 4. Building register

One row per division. **Building** is filled only where the concept names one; the rest is to design. **Back
office** is the BOSS or framework app behind the privacy glass, as a path under `/qapps`. **Front office** is what
PASS offers the public for that division, as its PASS path; the pairings are proposed from what each PASS app does
and are confirmed when the building is designed. A building with no front office has a locked front door.

| Division | Building | District | Back office | Front office (PASS) | Front meeting room |
| --- | --- | --- | --- | --- | --- |
| Commons | To design | Workspace | `Commons` | None proposed | |
| Comms | To design | Workspace | `Comms` | None proposed | |
| Sales | To design | Customer-facing | `Sales` | Sales: a customer's quotes and orders, `/sales` | |
| Marketing | To design | Customer-facing | `Marketing` | Marketing: subscriptions, forms, landing pages, event registration, `/marketing` | |
| Customer Service | To design | Customer-facing | `CustomerService` | Support: cases, returns, articles, feedback, `/support` | |
| Commerce | To design | Customer-facing | `Commerce` | Shop: the storefront, `/shop` | |
| Products | To design | Supply and operations | `Products` | None proposed | |
| Purchasing | To design | Supply and operations | `Purchasing` | Supplier: quote responses, order acknowledgements, bill upload, `/supplier` | |
| Warehouse | To design | Supply and operations | `Warehouse` | None | |
| Manufacturing | The factory | Supply and operations | `Manufacturing` | None | |
| Maintenance | The maintenance shed | Supply and operations | `Maintenance` | None | |
| Accounting | The accounting office, with old-style accounting decoration | Finance, people and projects | `Accounting` | Billing: a customer's invoices and payment, `/billing` | |
| People | To design | Finance, people and projects | `People` | None proposed | |
| Projects | To design | Finance, people and projects | `Projects` | Smithy: public repositories, issues and merge requests, `/code` (the name suggests a smithy) | |
| Insights | To design | Management | `Insights` | None | |
| Admin | To design | Management | `Admin` | None | |
| Community | To design | The gate | None | Forum, `/forum` | |
| Arrivals | To design; possibly a gatehouse | The gate | None | Account, Members and Sites, `/account`, `/members`, `/sites` | |
| System | To design | The works | `system` | None | |
| Tools | To design | The works | `tools` | None | |
| Dashboards | To design | The works | `dashboards` | None | |
| Dev AIDE | To design | The works | `dev` | None | |

IronStone HOME is a plan only; its apps join the register when they exist.

## 5. Interiors

**To design, one per building.** Every interior follows one template so they are quick to learn and to build:

- **The front office**, where the division has one: the first room inside the door, open to everyone in the
  world. It holds the division's public functions as counters, desks and books. No sound passes to or from the back
  office, so a visitor who wants a person rings for one: staff step through to the front, or a meeting is started
  on purpose (to design). Its decorations show only what the person looking may see of their own business with the
  division (their invoices, their cases), never the division's totals.
- **The privacy glass** between front and back, as [concept §5](concept.md#5-the-world) describes it: shapes and
  movement from the front, a clear but visibly tinted view from the back. To design: how big it is, where it sits,
  and what "things happening" look like through it.
- **The back office**, the app itself, behind a door that is locked to all but those authorized for the app. It
  starts with a large open center, where people arrive and can gather, with the app's Home shown as the things in
  it.
- **Bays around the walls**, one per top-level section of the app's menu, each with a sign naming the section, a
  book or desk that opens the section's main screen, and the section's own decorations.
- **Secured areas** behind doors, for sections that need more than the app's own access. A person without it sees
  the door locked ([design-technical §9](design-technical.md#9-authorization-and-locked-doors)).
- **Settings** in one consistent place in every building (a back office, a cupboard), since every app has them.
- **The way out**, and a door to the building's front meeting room.

Per interior, fill this table:

| Place | Kind | Section of the app | Opens | Secured by | Decorations |
| --- | --- | --- | --- | --- | --- |
| *example:* Cashier's window | front office | PASS Billing | `/billing` | the world grant | the visitor's own unpaid invoices as a small stack |
| *example:* Counting table | center | Accounting Home | `Accounting/Home` | the app | bank balance chest; overdue receivables stack |
| *example:* Receivables desk | bay | Receivables | `Accounting/Receivables` | the app | to design |
| *example:* Period close vault | secured area | Period close | `Accounting/PeriodClose` | the section's own group | to design |

The example rows only show the shape; the Accounting interior is not designed. Screen paths in a real row are
checked against the app's screens when the row is written.

## 6. Functional decorations

**To design, per space.** The catalog has this shape:

| Decoration | Place | Shows or opens | Source | Who sees it | States |
| --- | --- | --- | --- | --- | --- |
| *example:* Receiving crates by the barn door | Warehouse, center | The receiving queue as a pile that grows; opens Warehouse › Receiving | Warehouse Home | Warehouse users | empty, some, many, late (a red tag) |
| *example:* Approvals bell | Any building with an Approvals section | Rings and swings while approvals wait for this person; opens the approvals list | the shell's approvals count | The person themselves | still, ringing |
| *example:* Meeting status sign | Beside a meeting room door | Free, in session, how many inside; opens the room | the room's call | People who may join the room | free, in session, withheld |
| *example:* KPI billboard | The square | A dashboard panel chosen by an administrator; opens the dashboard | a dashboard panel | Whoever the dashboard is shared with | value, no data, withheld |

Rules for every row:

- **States are distinct.** Zero, missing, withheld (this person may not see it), stale and failed never look the
  same, as BOSS's dashboard widgets already require; a value shows how old it is.
- **Public decorations use public data.** A decoration everyone in a space can see shows only what everyone who can
  enter that space may read. Anything narrower is drawn per person: one sees the full crates, another a closed lid.
- **Nothing is read through the glass.** From the front office a back-office decoration is a plain shape with no
  level, color or text, and people are figures without faces or names. What moves may be seen to move.
- **Personal before aggregate.** "Your approvals", "your open cases" come first, because they are what a person
  acts on; division totals follow.
- **It opens something.** Selecting a decoration opens the screen that explains or changes what it shows.
- **No individual scoreboards.** A decoration does not rank named people, unless a later scoring design says so.

Kinds to draw from: a pile or stack (a queue's length), a gauge or level (a quantity against a limit), a flag or
banner (a status), a light or bell (something waiting for this person), a clock or calendar (a due time), a sign
(text and a number), a creature or cart in motion (work in progress), a book or desk (a working screen).

## 7. Global decorations

**To design.**

| Element | To decide |
| --- | --- |
| The dome as a screen | Which events use it (an all-hands meeting's video, an announcement); who may put something on it |
| The large terrain feature | Which global, everyone-may-see states it shows (Commons: shared calendar, announcements, the knowledge base) |
| Time of day | Whether the sky follows the installation's business hours, the person's own clock, or nothing |
| Weather | Whether it means anything; if it shows system health, only to those who may see system health |
| The notice board | Company announcements, events today, links to what is new |

## 8. Meeting rooms and gathering places

**To design.** Every enclosed space is its own sound space and its door is passed through, never opened ([concept
§5](concept.md#5-the-world)). Where the general rooms are; how a room for a particular organization is marked; the
room at each building's front; how many people a room is drawn for; what its door sign shows; where people simply
stand and talk.

## 9. Avatars and villagers

**To design.** How a person's figure is made and how far it can be customized; how name, presence (away, do not
disturb, in a call) and organization show; how staff and external visitors are told apart, and how "Visitor" and a
visitor's real name are shown (the only two choices, [concept §7](concept.md#7-people-sound-and-meetings)); how
someone who joined a meeting from the 2D screen appears to those in the room; whether Assist agents are villagers,
and where they stand.

## 10. Navigation aids

**To design.** Signposts and a map table (which also opens Maps); fast travel by selecting a building from anywhere;
how the search palette moves a person to the place of the screen it opened; a way home to the square; and what the
world shows a first-time visitor.

## 11. Open

| Question | Recommendation |
| --- | --- |
| The front office pairings of [§4](#4-building-register) | As proposed there; Smithy with Projects and Forum as its own place by the gate are the two least certain |
| A building with no front office: a locked front door, or a small lobby with glass | A locked door at first; a lobby where there is something worth glimpsing (the factory floor, the warehouse) |
| What "things happening" behind the glass may show | People and moving work as plain shapes only; an installation may set the glass opaque |
| Which PASS site the front offices belong to, where an installation has several | One site chosen by an installation setting at first; a village per site is a later question |
| Whether interiors are scaled to the app's size | No: one template size with more or fewer bays, so every interior is quick to cross |
| How much moves on its own (carts, animals, workers) | Only what stands for real work in progress, plus a little idle life near the square |
