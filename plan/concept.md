# mr-three — Concept

November 17, 2026 — concept draft; nothing built. This states what mr-three is for and what it should feel like.
The world layout is [design-world.md](design-world.md) (a skeleton for a later pass) and the engineering is
[design-technical.md](design-technical.md).

Sections: [1 purpose](#1-purpose) · [2 serious 3D](#2-serious-3d-in-the-business-apps) ·
[3 the village](#3-the-village-what-it-is-and-is-not) · [4 principles](#4-design-principles) ·
[5 the world](#5-the-world) · [6 screens](#6-screens-in-the-world) · [7 people](#7-people-sound-and-meetings) ·
[8 references](#8-reference-games) · [9 not goals](#9-not-goals) · [10 documents and order](#10-documents-and-order) ·
[11 open](#11-open)

---

## 1. Purpose

mr-three brings 3D to MR Moqui with three.js and the libraries around it, physics included. It is an add-on
component that depends on the apps it decorates and injects itself into them (screen extensions and the shell's
plugin points), so BOSS, PASS and the framework apps gain 3D without depending on it.

It has two sides that share one foundation:

1. **Serious 3D for business applications.** Previews first, editing later, placed inline in the screens where the
   third dimension says something a table cannot.
2. **The village.** An alternative, game-like way to use the whole system: a small medieval village where each
   application is a building, the state of the business is visible in the things lying around, and people can meet
   and talk as they walk. It is an experiment in making business systems less intimidating.

The first is the reason the component is worth having in a business installation. The second is the reason it is
fun to build, and the place where the idea is new.

## 2. Serious 3D in the business apps

**Rule: 3D only where the third dimension carries information.** A chart, an org chart or a Gantt chart does not
become better by gaining depth. These do, and each rests on data the apps already hold:

| Use | Where it appears | What it shows | Data it starts from |
| --- | --- | --- | --- |
| Warehouse racks | Warehouse › Locations, the facility record | Aisles, sections, levels and positions as racks; stock, holds and pick density as color | Mantle `FacilityLocation` area, aisle, section, level and position; coordinates and bin sizes are new data |
| Packing preview | Warehouse › Shipping, the package editor | A box type with the items in it, to see that it fits before taping it shut | `ShipmentBoxType` sizes, `ProductDimension` |
| Product size and model | Products › Product 360; later the PASS Shop product page | A product's box at true proportion beside a reference object; its 3D model when one is on file | `ProductDimension`; a 3D model content type is new |
| Assembly instructions | Manufacturing › Shop floor | The AssemblyViewer BOSS already designs: a model, steps, parts and annotations, always with a text equivalent | [design-manufacturing](../../mr-boss/plan/design-manufacturing.md) AssemblyViewer |
| Equipment and plant | Maintenance › Equipment and Facilities | Assets placed in their facility, colored by status and open work | `Asset`, `Facility`, maintenance work orders |
| Maps in 3D | Tools › Maps (planned) | Terrain and extruded buildings under the same layers; a facility or yard placed on the map | [Maps](../../../../../plans/Maps.md) keeps the 2D app and its data; mr-three adds only this |

Previews come first because they are safe: they read what the app already reads and change nothing. Editing (drag a
rack, rearrange a yard, place equipment) follows once a preview has proved useful and the data to edit exists. Every
viewer has a text or table equivalent, so nothing a person must do depends on a 3D canvas.

## 3. The village: what it is and is not

It is not exactly a game and not exactly a render mode. It is an alternative UI laid over the qvt UI that uses the
real 2D screens whenever it needs them: shown inside the 3D world, or opened in a window over it for full use, with
an easy swap back into the "game".

It is "gamification" in one sense only: the UI resembles a modern resource-management game and feels like a video
game to use. It does not try to turn ERP work into a game with cheap scores and prize animations. Some scoring will
probably come later, and when it does it follows the caution IronStone HOME already states about leaderboards,
penalties and streaks. The central point is the game-like UI, not the points.

Each application in BOSS and PASS is more or less a business division that could be a small business of its own, so
each is a building. Someone who wants to make something goes to the factory. Someone who wants to fix something goes
to the maintenance shed. There is a little accounting office with all the old-style, stereotypical accounting
decoration. And so on, for every app.

**Why medieval.** Business systems are intimidating, and a modern or science-fiction setting would make them more
so. A low-tech village shows that even complex modern business ideas fit in a world anyone understands: a ledger is
a ledger, a warehouse is a barn with shelves, a purchase order is a letter to a supplier. Easy to understand and
easy to fit into is the goal.

## 4. Design principles

- **Functional decoration is where the teeth are.** As many decorations as possible represent access to something
  or the state of something: in the system or in the business. The design question for every space is how much of
  the existing functionality it can show. A pile of crates is the receiving queue; a bell rings for approvals.
- **Walking is optional.** People can walk around, and it should be pleasant, but nobody has to. Everything can be
  reached by clicking it, and fast navigation is always at hand.
- **Low stress.** Soft angles, comical style, amusing animation. Sloppy but lenient: things wobble and fall over
  without damage, success is easy, and the work still gets done.
- **Beautiful, not fancy.** Space is well filled where nobody walks and open where they do. Stylized decoration and
  lettering throughout. Simple textures, and none where none is needed. Low polygon counts, with art made for low
  poly rather than cut down to it.
- **Real screens look different on purpose.** A 2D screen shown in the world has a decorated border, so the change
  from world to working screen is obvious and the screen stays as functional as it was designed to be.
- **Authorization is visible.** What a person may not open looks locked, as a disabled control does in a 2D screen.
  Nobody wanders into a room and then gets an error.
- **It runs on ordinary hardware.** A mid-range modern phone, and a laptop or desktop with a small integrated GPU,
  get a reasonable frame rate. This limits the art before it limits anything else.
- **Familiar, not original.** Controls and conventions are whatever has become the norm across popular online 3D
  games. The aim is not to find unique mechanics; real business domains will need plenty of adaptation as it is.

## 5. The world

- **A dome.** The whole world sits inside a big dome, as in "Under the Dome" or "The Truman Show". It bounds what is
  drawn at a distance and makes the sky simple. It can also be a huge screen for special events.
- **Terrain.** Minor variations for variety and a sense of place. On one side a large terrain feature that cannot be
  walked on, for decoration and for more room to hold functional decorations of the global kind (the Commons app is
  the first candidate).
- **Buildings.** One per application. The outside says what the division does; the door is the way into the app.
- **Interiors.** Each building's interior is a separate space, open only to people authorized for that app. To keep
  things simple an interior has a large open center, with the parts of the application laid out around it. Secured
  areas have doors, and a person without the authorization sees them clearly locked.
- **Meeting rooms.** Spread around the village and reachable from the front of each building: general ones, and
  ones plainly meant for a particular organization.
- **A high third-person view.** The world is 3D, seen from above and behind, suited to walking around a village.

## 6. Screens in the world

What is simple enough to do as a game-like interaction is done in the 3D world. The rest uses the real screens:

- **Magic books** hold a working screen: pick one up and the 2D screen opens.
- **Magic signs** show something openly: a meeting room's status by its door, a KPI billboard in the square, a
  dashboard on a wall. Selecting one opens the real 2D screen behind it.
- **The overlay window** shows any screen at full size over the world, and closes back to where the person stood.

How much of System, Tools and Dev AIDE fits in the village is an open question. The same rule applies as for every
app: what is simple enough to surface in the world is surfaced there, and the rest is a book or a sign.

## 7. People, sound and meetings

- **Avatars.** People in the village see each other walking about.
- **Distance audio.** Outdoors, people hear those near them, louder when closer.
- **Private audio in meeting rooms.** The village's meeting rooms are the system's existing meeting rooms on the
  same backplane, so someone who joined from the 2D meeting screen and someone who walked in are in one meeting.
- **Helpers.** Assist agents already have identities in chat. They could later be villagers one can walk up to.

## 8. Reference games

Job-like games with functional-looking UIs are what people already know. None is a model to copy; the aim is the
generic and familiar.

| Game | What is interesting |
| --- | --- |
| Job Simulator | The classic. Style more than domain: everyday work as cheerful, exaggerated objects |
| Office Simulator | Cleaner and more serious in look; task-oriented but amusing, with art to match |
| Carry On Together | Physics-based co-op moving and delivery. Slapstick accidents without damage: a good tone for animating business activities (sloppy but lenient, easy success, still productive) |
| Human Resource Machine | Programming as a puzzle; a possible pattern for the service editor in Dev AIDE |
| Database Detective | Teaches SQL by use. Its look is far too old-Windows for this, but the teaching idea could serve built-in help in Tools and Dev AIDE |

## 9. Not goals

- A game engine, or a game with a win condition.
- A general virtual world or user-built spaces.
- A replacement for the qvt UI. Every screen keeps working without mr-three, and the village leans on those screens.
- Any business task that can only be done in 3D.
- A modern or science-fiction theme.
- Photorealism, heavy textures or effects that need a discrete GPU.

## 10. Documents and order

| Document | Holds |
| --- | --- |
| [design-world.md](design-world.md) | The village plan, the building for each app, each interior, and the catalog of functional decorations. A skeleton now; filling it is a design effort of its own |
| [design-technical.md](design-technical.md) | Libraries, rendering, how the village sits over the qvt shell, authorization, live state, presence, audio, assets, and what the framework must gain |

Suggested order, detailed in [design-technical §19](design-technical.md#19-phases): the shared foundation; the
village's exterior for one person, with buildings as doors into the apps; the first inline viewers; interiors and
functional decorations; other people; meetings, then distance audio; and after those the dome events, helpers,
editing and any scoring.

## 11. Open

| Question | Recommendation |
| --- | --- |
| What the village mode and the component are called beside BOSS, PASS and HOME | Decide before anything user-visible is built; the documents say "the village" until then |
| Who is in the world | Internal users first (those with BOSS access). PASS's public users get their own quarter and rules later, if at all; a public visitor never shares a space with staff by default |
| One village per installation, or one per organization | One per installation, with organization shown by the rooms and banners a person sees; the active organization scopes data as it does in the apps |
| How much of System, Tools and Dev AIDE | A building each with signs and books at first; game-like interactions only where one is obviously simple (a failed job to retry, a service to restart) |
| Scoring | Later, opt-in, and never by default for a person's individual activity |
