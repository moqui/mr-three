# mr-three — Technical design

November 17, 2026 — draft; nothing built. The component holds a README and a license. This file decides how
mr-three is built on MR Moqui as it is today, and lists what the framework and Mantle must gain. The
[concept](concept.md) is its fixed input; the [world design](design-world.md) decides what the spaces contain.
Names of files, services and entities are checked against the tree unless marked *proposed*; behavior marked
*unverified* has not been tried.

Sections: [1 decisions](#1-scope-and-decisions) · [2 foundations](#2-existing-foundations) ·
[3 component](#3-component-layout) · [4 libraries](#4-libraries-and-vendoring) · [5 rendering](#5-rendering-core) ·
[6 inline 3D](#6-inline-3d) · [7 shell layer](#7-the-village-as-a-shell-layer) · [8 world data](#8-world-data) ·
[9 authorization](#9-authorization-and-locked-doors) · [10 decorations](#10-functional-decorations) ·
[11 screens](#11-screens-in-the-world) · [12 movement](#12-movement-camera-and-physics) ·
[13 presence](#13-presence) · [14 audio](#14-audio-and-meetings) · [15 assets](#15-assets) ·
[16 clients](#16-clients) · [17 testing](#17-testing) · [18 needs](#18-framework-and-data-needs) ·
[19 phases](#19-phases) · [20 decided](#20-decided) · [21 open](#21-open)

---

## 1. Scope and decisions

- **One component, two consumers of one core.** A rendering core (`moqui.three`) serves both the inline viewers
  and the village. Neither loads until a page uses it.
- **three.js on WebGL2.** WebGPU is a later option, not a baseline ([§4](#4-libraries-and-vendoring)).
- **The village is a layer of the shell, not a screen and not a render mode.** The 3D canvas sits behind the qvt
  page; the page becomes a framed window over it. Every 2D screen is the real one at its real URL
  ([§7](#7-the-village-as-a-shell-layer)).
- **The world is data.** A space is a layout document naming kit pieces, doors, places and decoration bindings.
  The engine draws any layout; [design-world](design-world.md) decides the layouts ([§8](#8-world-data)).
- **Authorization is the apps' own.** A door's lock is the answer of the same check the screen itself would make,
  and live state is fetched as the person looking at it ([§9](#9-authorization-and-locked-doors),
  [§10](#10-functional-decorations)).
- **Meetings are the existing calls.** A village meeting room is a chat room with its call, so 2D and 3D
  participants share one meeting. Distance audio is built on the same media server ([§14](#14-audio-and-meetings)).
- **Art is CC0 glTF packs** recolored to one palette, plus code-built functional decorations ([§15](#15-assets)).
- **Maps stays where [Maps](../../../../../plans/Maps.md) puts it.** mr-three adds 3D layers and the in-world map
  table only ([§6.3](#63-3d-for-maps)).
- **Nothing requires 3D.** Each viewer has a text twin, and the village has a text directory that is also its
  terminal rendering and its fallback where WebGL is missing.

## 2. Existing foundations

What is there to build on, and what each lacks for this use. Paths are from the mr-moqui root; `webroot/` is
`runtime/components/base/webroot/screen/webroot/`.

| Foundation | Reuse | Lacks |
| --- | --- | --- |
| Static files beside a screen | A component mounts a static directory under the root screen, as dev-aide mounts `devstatic` (`runtime/components/dev/dev-aide/MoquiConf.xml`). Files get ETags, a single byte range, and `immutable` caching for a `.min.` name or a `?v=` parameter (`crates/moqui-core/src/screen/static_files.rs`); the service worker caches `?v=` URLs | No media types for `.glb`, `.gltf`, `.bin`, `.ktx2`: they go out as `application/octet-stream`, uncompressed |
| Lazy libraries | `moqui.loadScript` and `moqui.loadStylesheet` load once per page (`webroot/js/MoquiLib.js`); `WebrootCrypto.js` loads a vendored ES module with WASM by dynamic `import()`; components that arrive late register with `qvt.registerLate` | No import maps and no bundler at run time; the vendored-library manifest check (`crates/moqui-core/tests/browser_libs.rs`) covers only `webroot/libs` |
| A widget in another app's screen | `screen-extend` with `widgets-extend`, and `ext-panel` / `ext-action` slots ([Qvt-Client](../../../../../docs/ui/Qvt-Client.md)); a component used through `render-mode` text with a `tui` twin, as `m-chart` is | A new XML element needs the framework (XSD, every mode's macros); an add-on cannot add one |
| The shell | `m-qapps-root` with the page in `#m-canvas`, floats (`webroot/js/WebrootFloats.js`), the tray, the palette, the Assist drawer, the live call window; `shell-providers` on an app root (`account-menu-item`, `palette-action` and others); theme hooks `STRT_SCRIPT_FOOTER` and `STRT_HEADER_NAVBAR_COMP`; AppList's `uiModes` list, which a `screen-extend` can append to | No slot for a layer behind the page; a child theme's rows of one resource type replace its parent's, so a script added by theme data can be dropped by a custom theme |
| A screen in a container | `m-dynamic-container` and `m-dynamic-dialog` load any screen into an element and give it `mScreen`; the tray's tabs are these | Whether links inside one stay inside it depends on the screen (*unverified* in general) |
| Menu and app data | `GET /shellData` (apps, menus to depth 2, providers, counts) and `GET /menuData/<path>` | Both leave out what the user may not view (`crates/moqui-core/src/screen/menu.rs`), by design; neither can say "locked" |
| Permission checks from a script | `ec.artifactExecution.isPermitted(name, artifactType, action)` ([JS-Core-API](../../../../../docs/dev/JS-Core-API.md#ecartifactexecution)) | Enough for a door manifest ([§9](#9-authorization-and-locked-doors)) |
| Live state | Each BOSS app's Home service (`ironstone.warehouse.HomeServices.get#Home`, `ironstone.accounting.HomeServices.get#Summary`, `ironstone.insights.RegistryServices.get#Attention` and the rest); `org.moqui.ui.ShellServices.get#ShellCounts` and its `shellCounts` push; `org.moqui.report.ReportServices.render#Dashboard` and `get#AlertState`, which run as the viewer ([Monitoring](../../../../../docs/tools/Monitoring.md)) | No single call for "the state of this place"; that is mr-three's to add |
| Pushes to the browser | One socket per page, `/notws`, with `moqui.NotificationClient`; a service can send to a topic of its own with `ec.makeNotificationMessage()` | Browser-to-server frames are fixed families (`collab`, `calc`, `rtc`) compiled into `crates/moqui-host/src/notws/`; cluster topics are a fixed list (`crates/moqui-core/src/notify/topic.rs`). An add-on can add neither |
| Calls | An SFU in the server on str0m (`crates/moqui-rtc`), signaling on `/notws`, the client store `moqui.call` (`webroot/js/WebrootCall.js`), rooms and sessions in `ChatEntities.xml` and `CallEntities.xml`, services in `org.moqui.call.CallServices` ([WebRTC](../../../../../docs/interfaces/WebRTC.md), [Comms](../../../../../docs/tools/Comms.md)) | Audio goes to every peer in a room (only video is subscribed per publisher); one call per page; a room's call start rings its members; 50 legs per room |
| Native code in a component | A crate beside `component.xml` registers services, service runners, tools, resources and templates ([Native-Components](../../../../../docs/dev/Native-Components.md)); a tool factory can hold shared state | No HTTP route, socket endpoint, socket frame handler or cluster topic |
| Preferences | `UserPreference` through `ec.user.getPreference` / `setPreference`, `$root.setPreference` and `config.preferences` in the client; `moqui.scopedKey()` for per-account browser storage | Nothing |

## 3. Component layout

*Proposed.* Following a component's directories in [Practices-App-Dev](../../../../../docs/dev/Practices-App-Dev.md):

| Path | Contents |
| --- | --- |
| `component.xml` | Depends on `mr-boss` (and through it Mantle). PASS is optional: its extensions load only when `mr-pass` is present |
| `MoquiConf.xml` | Mounts `threestatic` under the root screen (anonymous view, as `devstatic`) and the `village` app under the apps screen with its own launcher entry |
| `screen/village.xml`, `village.qvue`, `village.tui.json` | The village's app root: entry, the text directory, settings. Its artifact group decides who may use the village at all |
| `screen/village/` | Settings, the administrator's screens for signs and rooms, and the transitions the world calls |
| `screen/threestatic.xml`, `screen/threestatic/` | Static files: `libs/` (vendored libraries), `kits/` (processed `.glb` kits), `js/` (mr-three's own modules), `css/` |
| `screen-extend/` | The inline viewers injected into BOSS and framework screens; the AppList `uiModes` entry |
| `service/three/` | Services: doors, space state, signs, rooms; the source registry |
| `world/` | Space layout documents and the kit piece index ([§8](#8-world-data)) |
| `entity/`, `data/` | The few entities of [§8.3](#83-installation-state); security data, theme resource rows, seeded rooms |
| `src/`, `Cargo.toml` | The presence relay, when the framework offers a socket to register ([§13](#13-presence)) |
| `tools/` | Development scripts that run on Node, not at build or test time: `refresh.mjs` for libraries, `kits.mjs` for art |
| `test/`, `test/client/`, `test/acceptance/` | Specs ([§17](#17-testing)) |
| `plan/`, `docs/` | These documents; guides once something is built |

Services are XML Actions first and JavaScript where they must be, as everywhere else. Every screen works in `qvt`
and `tui`.

## 4. Libraries and vendoring

| Library | Use | License | Loaded |
| --- | --- | --- | --- |
| three.js core | Scene, WebGL2 renderer, materials, animation, positional audio | MIT | Any 3D |
| three.js addons: `GLTFLoader`, the meshopt decoder, `OrbitControls`, `CSS2DRenderer`, `BufferGeometryUtils`, `SkeletonUtils` | Models, viewer camera, labels anchored to 3D points, merging and instancing, cloning rigged figures | MIT | Any 3D |
| three-mesh-bvh | Fast picking and ray tests against merged scenery | MIT | The village; large viewers |
| Rapier (`rapier3d-compat`, Rust compiled to WASM) | Rigid bodies for props that are carried, stacked and knocked over; later a character controller | Apache-2.0 | Interiors with physical props only |
| glTF-Transform | Processing the art packs: subset, recolor, merge, compress | MIT | Development only (`tools/kits.mjs`) |
| esbuild | Making the library bundle | MIT | Development only, as `webroot/libs/refresh.mjs` uses it |

Licenses are confirmed and versions pinned when each is vendored; this plan names none.

- **Vendoring follows `webroot/libs`.** `tools/refresh.mjs` pins versions and integrity hashes and writes
  `screen/threestatic/libs/` with a `MANIFEST.json` and `NOTICES.md`; a spec fails when a file there is not in the
  manifest or its hash differs. mr-three's libraries stay in mr-three, so an installation without it carries none
  of them.
- **One library bundle.** three.js addons import `three` by bare name, and the shell has no import map, so
  esbuild makes one ES module, `three-kit.min.mjs`, exporting the core and the chosen addons from one instance.
  Rapier is a second file, because most pages never need it.
- **Loading.** `moqui.three.load()` does a dynamic `import()` of the bundle once per page and returns a promise, the
  pattern `WebrootCrypto.js` uses. Nothing 3D is fetched by a page that shows nothing 3D.
- **Not taken at first.** Draco and KTX2/Basis (the art has few textures and meshopt is enough); a text-rendering
  library (signs draw text to a canvas texture; SDF text libraries start workers from blob URLs, which the bundled
  shell's policy refuses); a navigation-mesh library (the walk grid of [§12](#12-movement-camera-and-physics) needs
  only A*); a post-processing stack; an entity-component framework.
- **WebGL2 now, WebGPU later.** WebGL2 runs on every target (current browsers, the CEF desktop shell, Android
  WebView) with one code path. three.js's WebGPU renderer is revisited once the village exists and can be measured.

## 5. Rendering core

`moqui.three` is one small classic script, present on every `/qapps` page, that defines the namespace and loads the
rest on demand: `load()`, `view(el, spec)` for a viewer, `enter()` and `leave()` for the village.

- **One WebGL context per page.** Browsers allow few contexts, and a page may show several viewers and the
  village. One renderer draws every scene: the village to its own full-window canvas, and each viewer by rendering
  to the shared canvas at the viewer's size and copying the result into the viewer's 2D canvas.
- **Render on demand.** A viewer draws when its camera or data changes, not every frame. The village draws at a
  capped rate (30 or 60 by tier), drops to a few frames a second when nothing moves, and stops while the tab is
  hidden or a window covers it entirely.
- **Quality tiers**, chosen from a short start-up measurement and changeable in settings (`THREE_QUALITY`):

  | Tier | Meant for | Resolution | Shadows | Extras |
  | --- | --- | --- | --- | --- |
  | Low | Phones, old integrated graphics | Device pixel ratio capped at 1 | Blob shadows under figures | No idle animation beyond the near area |
  | Medium | Mid-range phones, APU laptops | Capped at 1.5 | One small shadow map near the camera | Idle animation |
  | High | Discrete or recent integrated GPUs | Capped at 2 | Larger shadow map | Idle animation, soft light |

- **Budgets** (targets, to be confirmed by measurement in T1): 30 frames a second on a mid-range phone at Medium;
  under 150 draw calls and 300,000 triangles in view; under 64 MB of GPU memory; the exterior usable within 5 MB of
  transfer including libraries, an interior within 2 MB more. Scenery is merged or instanced per kit and shares
  one palette material, which is what keeps draw calls low.
- **The dome** is the far limit: the camera's far plane is the dome's width, nothing is drawn beyond it, and the
  sky is the dome's inside surface.
- **When WebGL is missing or the context is lost:** a viewer shows its text twin; the village shows its directory
  ([§7.3](#73-the-village-screen-and-the-directory)). A lost context is restored once; a second loss falls back.

## 6. Inline 3D

### 6.1 The viewer component

`m-three-view` is a Vue component registered with `qvt.registerLate`. It takes a `preset` and the preset's data,
shows a canvas with orbit, pan and zoom, and a toolbar (reset view, full screen, labels on or off). Screens use it
as they use `m-chart`: a `render-mode` section with `qvt` text holding the component and `tui` and `html` text
holding the same facts as a table or list. The data comes from the host screen's own actions and transitions, so
the host's authorization decides what a viewer can show.

| Preset | Draws | Input |
| --- | --- | --- |
| `box` | One or more boxes at true proportion, a reference figure, dimension labels | Sizes with units |
| `packing` | A container box with item boxes inside, over-size items marked | Box type and items with sizes |
| `model` | A glTF model with optional steps, part highlights and annotations | A model URL and step data |
| `racks` | Racks generated from location identifiers, colored by a value per location | Locations with positions or identifiers, values |
| `site` | Objects placed on a floor or yard plan, colored by status | Placements and statuses |

### 6.2 First viewers

mr-three injects each with a `screen-extend` on the owning screen (`widgets-extend` beside the existing form, or an
`ext-panel` where the screen offers a slot), so BOSS gains nothing it has to know about.

| Viewer | Preset | Host screen | Data today | Needs |
| --- | --- | --- | --- | --- |
| Box type | `box` | Warehouse › Locations › BoxTypes | `mantle.shipment.ShipmentBoxType` sizes | None |
| Product size | `box` | `Shared/Product360` | `mantle.product.ProductDimension` | None |
| Packing preview | `packing` | `Warehouse/Shipping/Pack`, beside its package editor | Box type, `ShipmentPackageContent`, `ProductDimension` | Placement of items is a visual check only; packing advice is later |
| Warehouse racks | `racks` | `Shared/FacilityDetail` Locations tab | `mantle.facility.FacilityLocation` area, aisle, section, level, position | Generated rows from identifiers at first; true positions need N8 |
| Product model | `model` | `Shared/Product360`; later the PASS Shop product page | None | A 3D model content type, N9; PASS delivery, [§21](#21-open) |
| Assembly | `model` | Manufacturing's AssemblyViewer ([design-manufacturing](../../mr-boss/plan/design-manufacturing.md)), when BOSS builds it | The proposed production instruction services | BOSS's own needs for that screen; mr-three supplies its canvas |
| Equipment and plant | `site` | `Shared/EquipmentDetail`, `Shared/FacilityDetail` | `mantle.product.asset.Asset` with its facility and location | Placement data, with N8 |

**PASS pages** send a strict policy (`script-src 'self'`, no WASM, no `blob:`; `mr-pass/screen/Shared/Headers.js`)
and a PASS host refuses framework routes, so the product viewer there must be served by PASS itself and use only
the core bundle (uncompressed or JavaScript-decoded models). That is a later step with its own design.

### 6.3 3D for Maps

[Maps](../../../../../plans/Maps.md) owns the Maps app, the `map-view` widget, sources and geographic data, on
OpenLayers. mr-three adds, once Maps exists:

- **A 3D view of a map extent:** terrain and extruded building footprints (Maps' optional Overture Buildings
  layer) under the same saved view, as an alternative display of that screen, not a second map system.
- **A record on a map:** a facility's `site` view placed at its `GeoPoint`.
- **The map table** in the village, which opens Maps and can show the organization's facilities as models.

None of this starts before Maps P1, and Maps needs nothing from mr-three.

## 7. The village as a shell layer

### 7.1 Why a layer

The concept asks for an overlay on the qvt UI that uses its 2D screens and swaps easily between them and the
world. If the village were a screen, opening any other screen would navigate away and destroy the 3D scene. If 2D
screens were embedded copies inside the village, their links, breadcrumbs, record context and Assist would all
need a second implementation. So the world sits behind the shell, and the shell keeps working as it does today.

### 7.2 How it sits in the page

- **The world canvas** is an element outside the routed page, behind the shell's layout, created by
  `moqui.three.enter()`. It survives navigation because the shell never reloads the page.
- **World mode** is a class on the body. In it, the shell's page area (`#m-canvas`) is drawn as a framed window
  centered over the world, with a close control; the header stays, restyled to match; the left menu drawer shows
  the current app's menu only while a window is open.
- **A screen is its URL.** Opening a door, a book or a sign calls the root's `setUrl` with the real path
  (`/qapps/Accounting/Receivables`). The page loads into the window exactly as it does without mr-three. Closing
  the window goes back to `/qapps/village`, where the window is hidden and the world has the whole view.
- **So everything in the shell still works** in world mode: the palette, quick create, the tray (Today, Chat,
  Notifications), the Assist drawer and its screen context, floats, the live call window, the browser's back
  button, a link pasted to a colleague.
- **Place follows route, both ways.** Walking to a door opens its screen; and when the route changes by any other
  means (the palette, a notification, Assist following along), the avatar is moved to the place whose door or
  bay owns the longest matching path. The palette is therefore the fast travel.
- **A reload** in world mode at an app URL shows the screen in its window at once and loads the world behind it
  afterward; world mode itself is remembered per tab.
- **Phones:** below 600 px the window is the full screen with a "back to the village" bar; there are no floats
  at that width today either.

### 7.3 The village screen and the directory

`/qapps/village` is an ordinary app root. Its artifact group (`THREE_VILLAGE`, *proposed*) decides who may use the
village; an installation that wants only the inline viewers grants it to nobody.

- **In `qvt`** its `.qvue` starts world mode and shows nothing itself; where WebGL is missing it shows the
  directory.
- **The directory** is the world as text: spaces, their places with lock state, the meeting rooms with who is
  inside, the signs with their values, and a link for each. It is what a screen reader gets, what `tui` renders
  (`village.tui.json`), and the fallback. It reads the same services the world does, so it cannot drift.
- **Entry points:** a `uiModes` entry on the app list beside Standard and Material (a `screen-extend` on
  `webroot/apps/AppList.xml`), an `account-menu-item`, the launcher tile, and the `THREE_START` preference that
  opens the village after sign-in.

### 7.4 Getting into the shell

The loader script and the world-mode styles must be on every `/qapps` page. Today the only way an add-on has is
theme data: `STRT_SCRIPT_FOOTER` and `STRT_STYLESHEET` rows on the default theme. That works, with the caveat in
[§2](#2-existing-foundations): a child theme with its own rows of that type drops them. The durable form is a
shell provider for a layer (N2); until it exists the theme rows are used and the caveat is documented for theme
authors.

## 8. World data

### 8.1 Layout documents

A space is one JSON document in `world/`, owned by the component as code is. The shape, not final:

```json
{
  "space": "accounting",
  "kind": "interior",
  "of": "Accounting",
  "kits": ["interior-common", "accounting"],
  "grid": { "cell": 1.0, "width": 40, "depth": 28 },
  "pieces": [ { "piece": "desk-large", "at": [12, 0, 6], "turn": 90 } ],
  "places": [
    { "id": "receivables", "kind": "bay", "area": [8, 2, 8, 8], "screen": "Accounting/Receivables" },
    { "id": "close", "kind": "secured", "area": [30, 2, 8, 8], "screen": "Accounting/PeriodClose",
      "door": { "at": [30, 0, 6], "turn": 0 } }
  ],
  "anchors": [ { "id": "entry", "at": [20, 0, 26] } ],
  "decorations": [
    { "id": "overdue", "kind": "stack", "at": [10, 0, 4], "source": "accounting.summary",
      "value": "receivablesOverdue", "steps": [1, 5, 20], "screen": "Accounting/Receivables" }
  ],
  "rooms": [ { "id": "front", "area": [2, 20, 6, 6], "room": "accounting-front" } ]
}
```

- **Units are meters on a grid**, so pieces from modular kits snap together and the walk grid falls out of the
  layout: a cell is walkable unless a piece or a wall covers it.
- **`screen` is a path under the apps root**, never a component location or a URL from elsewhere.
- **`source` is a name from the source registry** ([§10](#10-functional-decorations)); a layout never names a
  service, an entity or a query.
- **The exterior** is one document of kind `exterior` with terrain heights, buildings as places whose doors lead
  to interior spaces, and the dome.

### 8.2 Validation

A layout that is wrong should fail a spec, not surprise someone in the world. `three.world.LayoutServices.check#Layouts`
(*proposed*) verifies for every document: each piece exists in its kit's index; each `screen` resolves to a screen;
each `source` is registered and each `value` is one the source declares; every place and anchor is reachable on the
walk grid from the entry; areas do not overlap; identifiers are unique. One bad document is reported by name and
the others still load.

### 8.3 Installation state

Layouts are code. What an installation changes is little, and lives in entities (*proposed*, package `three.world`):

| Entity | Holds |
| --- | --- |
| `WorldSign` | A sign place bound by an administrator to a dashboard panel or a playlist: `spaceId`, `placeId`, `definitionId`, `panelId`, `title` |
| `WorldRoom` | A meeting room place bound to a chat room: `roomPlaceId`, `chatRoomId`, and optionally the organization it is for |

A person's own state is preferences: `THREE_AVATAR` (figure and colors), `THREE_PLACE` (last space and anchor),
`THREE_QUALITY`, `THREE_AUDIO`, `THREE_START`. Editing layouts in the installation (a world editor) is later; when
it comes, edited documents are stored as resources with revisions and validated by the same service.

## 9. Authorization and locked doors

- **The door manifest.** `three.world.WorldServices.get#Doors` (*proposed*) takes a space and returns, for each
  door and place with a `screen`, one of `open`, `locked` or `absent`. It asks
  `ec.artifactExecution.isPermitted` for the screen as the current user, the same question the menu asks. The
  client only draws the answer; a locked door is also refused by the server if someone forces it, because opening
  it is a request for the real screen.
- **Why a new call.** `shellData` and `menuData` leave out what a user may not view, deliberately: they do not
  reveal which screens exist. The manifest reveals exactly the doors the layout declares and nothing else.
- **What a lock reveals, and the setting for it.** A locked door tells a person that an app or a section exists.
  For a suite's own apps that is no secret: the village is the product's app map. An installation that disagrees
  sets `three_locked_doors` to `hide`, and a place the person may not open is drawn as plain wall (`absent`).
  Doors inside a building are evaluated only for someone who may enter the building.
- **Menu conditions.** Some sections are shown by an expression rather than by authorization
  (`default-menu-include` with a permission check). A place for such a section names the same permission in its
  layout, and the manifest evaluates it; otherwise its door would open onto an empty screen.
- **Interiors are spaces with a gate.** The server admits a presence connection to an interior only when the
  door's screen is permitted, and returns that space's state only then ([§13](#13-presence)).
- **Art is not secret; state is.** Kit files and layouts are static files, served to any signed-in user. They show
  a generic accounting office, not this company's accounts. Everything that reflects data comes from services
  that run as the person and apply their authorization, organization filters included.
- **Organization.** The world calls the same services the screens call, after the same
  `mantle.party.PartyServices.setup#UserOrganizationInfo`, so a person sees their organizations' state and no
  other. Rooms for an organization are offered to its members ([§14](#14-audio-and-meetings)).

## 10. Functional decorations

- **The source registry.** `service/three/world/Sources.js` (*proposed*) maps a source name to an existing service
  and the values it exposes: `accounting.summary` to `ironstone.accounting.HomeServices.get#Summary`,
  `warehouse.home` to `ironstone.warehouse.HomeServices.get#Home`, `shell.counts` to
  `org.moqui.ui.ShellServices.get#ShellCounts`, `insights.attention` to
  `ironstone.insights.RegistryServices.get#Attention`, and one generic `dashboard.panel` over
  `org.moqui.report.ReportServices.render#Dashboard` for signs. Sources are code in the component; a browser
  never supplies a service name.
- **One call per space.** `three.world.WorldServices.get#SpaceState` (*proposed*) runs each source the space uses
  once, as the current user, and returns per source a state and its values:

  | State | Meaning | Drawn as |
  | --- | --- | --- |
  | `ok` | Values, with an as-of time | The decoration at its level |
  | `zero` | The source answered and there is nothing | The decoration empty, tidy |
  | `missing` | The source has no such value for this installation | The decoration absent |
  | `withheld` | This person may not read it | Covered: a cloth, a closed lid, a shuttered sign |
  | `stale` | The last good answer is older than its limit | The last level, with a dusty or faded look |
  | `failed` | The source failed | A "being repaired" look; the error is logged, never shown as a value |

  These are the states BOSS's dashboard widgets already distinguish
  ([design-crosscut](../../mr-boss/plan/design-crosscut.md)). One failing source does not fail the call.
- **Mapping to a look** is in the layout: `steps` turn a number into a level (a stack of 0, 1, 2 or 3 crates), a
  status into a flag color, a time into a clock hand. Code-built pieces implement the kinds listed in
  [design-world §6](design-world.md#6-functional-decorations).
- **Refresh.** On entering a space, then once a minute while in it, and at once when a `shellCounts` push says
  something changed. Home services are not cheap, so each answer is cached per user and source for the interval.
- **Signs.** A sign bound in `WorldSign` shows a dashboard panel's value, title and as-of time drawn to a canvas
  texture; selecting it opens the dashboard. A panel the person may not read comes back "Not available" from the
  report service, which is the `withheld` state.
- **The dome.** For an event, the dome shows a video track of a meeting (the presenter's camera or screen share)
  as a video texture, for those who join that meeting. Dashboards on the dome are sign-style number cards only:
  the browser cannot paint a DOM dashboard onto a texture.

## 11. Screens in the world

| Distance | What is shown | How |
| --- | --- | --- |
| Far | A summary: a number, a level, a status | Canvas-texture text and code-built pieces from space state |
| Near | The object reacts: a book opens, a sign turns, a door swings | Animation, then the window |
| Open | The real screen | The page window of [§7.2](#72-how-it-sits-in-the-page), framed in the style of what was opened |

- **One mechanism.** Books, signs, desks and doors all end in the page window at a real URL. They differ in the
  path they open and the frame around it.
- **Game-like interactions** are few and explicit: ring the bell to open approvals, put a crate on the cart to
  start a receipt. Each is a transition on the village screen that calls the owning app's service, which makes
  its own permission checks. mr-three holds no business rule and bypasses none.
- **Small panels in place.** A notice board or a room's sign may show a panel screen (the tray's Today, a room's
  roster) in a float or a small anchored panel, using `m-dynamic-container` as the tray does. Whether links
  inside stay inside is the screen's behavior; mr-three uses this only with panel-archetype screens made for it.
- **Not done: DOM in 3D perspective.** Projecting live screens onto surfaces with CSS 3D transforms blurs text,
  breaks focus and input on phones, and makes a working screen worse. A screen in use is always flat and framed.

## 12. Movement, camera and physics

- **Camera.** High third person: behind and above the figure, pitched down about 50 degrees, with zoom between a
  close view and one that takes in a district. Drag or Q and E rotate. Interiors have no roof and low near walls,
  so the camera never needs to pass through anything.
- **Moving.** Click or tap a spot to walk there (A* on the walk grid, then smoothed); click a door, book or sign
  to walk to it and use it; WASD or arrow keys, a touch stick, or a gamepad for direct control. Walking is about
  4 m/s and running 8, so the village is a short walk across.
- **Fast travel.** The palette, the directory, a signpost and a map table all move the figure at once; so does
  any navigation that changes the route ([§7.2](#72-how-it-sits-in-the-page)).
- **Keys.** While the world has focus its keys are a `moqui.keymap` scope; with a window open the keys are the
  screen's, as they are today. Esc closes the window.
- **Collision** is the walk grid: a figure cannot enter a blocked cell or a locked area. No physics library is
  needed for walking.
- **Physics** (Rapier) is for the comical life of a space: props that are carried, stacked, dropped and knocked
  over without harm. It is loaded only in spaces that have such props, runs at a fixed step, and is local to each
  browser at first: what one person knocks over, others do not see fall. Shared physics is later and optional.

## 13. Presence

Seeing other people needs each browser to send its position and receive the others', several times a second.

- **Why not the notification socket as it is.** A service can push to browsers, but a browser can send only the
  fixed frame families of `/notws`. Position traffic on that socket would also share its queue with call
  signaling, and a full queue closes the socket, which would drop a call. So presence needs a path of its own
  from the framework (N1): a socket a native component can register, with its own bounded queue that drops old
  frames instead of closing.
- **Rooms.** A presence room is a space. The exterior is one room; each interior is one. A connection is admitted
  to an interior only if the door's screen is permitted for the user ([§9](#9-authorization-and-locked-doors)).
- **Who moves the figure.** The browser does, and reports; the server relays. The server sets the identity
  (user, display name, avatar from the user's preference), limits speed, and refuses positions inside an area
  whose door is locked for that user. Nothing of value depends on a position, so this is enough.
- **Rates.** A moving browser sends up to 10 times a second and nothing while still, with a heartbeat every 5
  seconds. The server sends each member one snapshot of the room up to 10 times a second: a compact array per
  figure (id, x, z, heading, animation). Browsers interpolate between snapshots.
- **Limits.** 50 figures shown per space, the nearest first, with a count of the rest; the same number as the
  media server's legs per room.
- **Status.** A person whose status is invisible is not shown. Away, do not disturb and in-a-call show on the
  figure, from the `userStatus` pushes that chat already sends.
- **Where it runs.** A Rust crate in mr-three (`src/`), as a tool holding the rooms in memory, registered on the
  socket of N1. Presence is never written to the database.
- **Cluster.** Each node relays its own members and exchanges room snapshots with the others on a cluster topic at
  a lower rate. With the Postgres `notify` transport that rate must be low enough not to load the database
  ([§21](#21-open)).

## 14. Audio and meetings

### 14.1 Meeting rooms

- **A village meeting room is a chat room.** `WorldRoom` binds a room place to a `ChatRoom`; its meeting is that
  room's `CallSession`. Someone in Comms' Calls screen and someone who walked through the door are legs of the
  same call on the same media server. Who may enter is who may join that room's call today.
- **Kinds of room.** General rooms are public chat rooms seeded by mr-three. A building's front room is a room
  for that app's people. A room for an organization is offered to its members; binding it to the organization's
  party record as a record room is the likely form (*unverified*).
- **Walking in** joins the call (`org.moqui.call.CallServices.join#Call`, or starts it when nobody is there).
  Starting a room's call today rings every member; a walk-in must not, which needs N4.
- **Who is inside.** `get#Call` lists participants. Those with a figure in the room are shown where they stand;
  those who joined from 2D or by phone are shown seated at the table, so nobody in the meeting is invisible.
- **The door sign** shows free or in session and how many are inside, to people who may join. Live call state is
  pushed only to participants today, so the sign polls `get#Call` for the rooms in view until N6.
- **One call per page.** `moqui.call` holds a single call. Entering a meeting room leaves any ambient call, and
  leaving the room returns to it. A call taken from the header (a phone call, a direct call) does the same.

### 14.2 Distance audio

- **A space call.** Each space with ambient audio has one audio-only call. Joining it is a person's choice, off by
  default; the microphone is a second choice. A figure shows whether its person is listening and speaking.
- **First form: in the browser.** The media server forwards every audio track in a call to every member. The
  browser plays each remote track through WebAudio instead of the plain audio element `m-call-audio` uses: a gain
  set from the distance between figures (full within 3 m, fading to silent at 15 m) and a stereo pan from the
  direction. Positions come from presence.
- **What that means for privacy, stated in the UI.** In this form every member's browser receives every voice in
  the space, and distance only sets the volume. A modified browser could hear the whole space. So ambient audio is
  presented as "anyone in this place may hear you", and private talk belongs in a meeting room.
- **Second form: in the media server.** With per-publisher audio subscription (N5), the server forwards a voice
  only to those within range, from positions the presence relay reports. That scales past a few dozen speakers
  and makes range a real boundary.
- **Limits.** 50 legs per call and 200 calls per node today. Silence costs little (the browsers send
  discontinuous audio), so the first form is fine for a village of tens.
- **Encrypted meetings** work unchanged: the browser decrypts before the track reaches WebAudio. Recording and
  captions follow the meeting's own rules.
- ***Unverified:*** some Chromium versions need a remote stream attached to a muted media element before WebAudio
  will play it; to be tried in T5.

## 15. Assets

- **Sources.** CC0 low-poly packs with a medieval village, interiors, props and rigged figures. Kenney, KayKit and
  Quaternius publish such packs; which ones, and each pack's license file, are settled in
  [design-world §2](design-world.md#2-art-direction) and recorded when vendored. No pack is used without a license
  text that permits redistribution without conditions.
- **The pipeline** (`tools/kits.mjs`, Node, development only, in the pattern of the library refresh):
  1. Fetch each pinned pack archive and check its hash. Source archives are not committed.
  2. Take only the pieces listed in `world/kits/<kit>.json`.
  3. Recolor to the one palette: every piece's colors are mapped to a small shared palette texture, so all
     scenery draws with one material.
  4. Normalize scale to meters, origin to the piece's grid corner, and names to piece identifiers.
  5. Merge, deduplicate and compress with meshopt into one `.glb` per kit.
  6. Write the kit's index (pieces, sizes, grid footprint) and `kits/MANIFEST.json` (pack, source, license, hash,
     pieces used), with license texts in `NOTICES.md`.
- **Kits.** `village-base` (terrain pieces, walls, roofs, roads, trees), `interior-common` (floors, walls,
  furniture, doors, books, signs), one small kit per building for what is special to it, and `figures`.
- **Figures.** A few base bodies sharing one skeleton and one set of clips (idle, walk, run, sit, wave, carry).
  A person chooses a body, colors and a hat or two. Figures are cloned with their skeleton, and far ones freeze
  their animation.
- **Code-built pieces** are the functional decorations: stacks, gauges, flags, clocks, banners and signs with
  canvas-drawn text. They use the same palette and are made from data, so no model exists for "a stack of 17".
- **Serving.** `/threestatic/kits/<kit>.glb?v=<hash>` is cached as immutable and kept by the service worker.
  `.glb` works today as `application/octet-stream`; proper media types and compression for `.gltf` and `.bin`
  are N3.
- **Budgets per kit** are part of the kit's index and checked by the pipeline: triangles per piece, total size,
  and one material.

## 16. Clients

- **Browsers.** The default policy on `/qapps` restricts neither scripts nor workers nor WASM, so the web client
  needs no policy change.
- **Desktop and Android.** The Tauri clients show the server's own `/qapps` in app windows under the server's
  policy ([Mobile-Desktop](../../../../../plans/Mobile-Desktop.md)), so the village runs there as on the web. The
  bundled shell has a strict policy and a list of libraries it may load; mr-three is not added to it, and a
  viewer on a screen the bundled shell would draw opens in an app window. WebGL in CEF and in Android WebView is
  expected and *unverified* on the target devices.
- **Phones.** Touch controls, the Low or Medium tier, the window as the full screen. The village is usable on a
  phone; long work in a screen is better outside world mode, and leaving it is one tap.
- **Terminal and print.** The directory for the village; each viewer's text twin.

## 17. Testing

Under the two suites of [Build-Test](../../../../../docs/Build-Test.md):

- **Regression specs** (server, fast): layout validation over every document; the door manifest for users with
  and without access, and with `hide`; space state for each state of the table, including a source that fails
  and one that is withheld; sign binding; room binding and who may enter. The pure parts of the engine (walk
  grid, path finding, level mapping, snapshot interpolation) are modules with no WebGL in them, tested as client
  specs without a renderer.
- **Rendering.** The village screen is rendered once in `qvt` and once in `tui`, since it has its own templates,
  with all checks on that render. Each injected viewer is checked on its host screen's existing render, not by a
  new one. No sweep.
- **Acceptance** (only when asked or when changed): one browser walk that enters the village, opens a door, sees
  the screen in its window and returns; later one with two browsers for presence and one for a meeting room.
  Whether headless Chromium gives a usable WebGL context in the test harness is *unverified*; the walk asserts on
  the directory and the DOM where it can.
- **The libraries manifest check** is a regression spec in mr-three, as `browser_libs.rs` is for `webroot/libs`.
- A regression budget for mr-three is set when the first spec lands.

## 18. Framework and data needs

| # | Need | Kind | Proposed as | Used by | Stub spec |
| --- | --- | --- | --- | --- | --- |
| N1 | A socket for a component's own messages | protocol server | A native component registers a named socket family (its own endpoint, or a family on `/notws` with a separate queue); authentication and credential recheck as `/notws`; a bounded outbound queue that drops oldest; a cluster topic per family | Presence | join, frame in, broadcast to a room's sockets, drop on full without closing; relay between nodes |
| N2 | A shell layer | shell item | A `shell-layer` provider on an app root naming a script and stylesheet the shell loads on every page, in place of theme rows | World mode | loaded once per page; not replaced by a child theme |
| N3 | Media types for model files | tool | `model/gltf-binary`, `model/gltf+json`, `image/ktx2` and compression of `.gltf` and `.bin` in `crates/moqui-core/src/screen/static_files.rs` and the host's compressible list | Assets, viewers | a `.gltf` is served typed and gzipped |
| N4 | Joining a room's call without ringing it | service type | A parameter on `org.moqui.call.CallServices.start#Call`, or a call kind for a space | Meeting rooms, space calls | a walk-in starts or joins and no member's phone or browser rings |
| N5 | Audio subscription per publisher | protocol server | An audio "wanted" set beside the video one in `crates/moqui-rtc/src/sfu/peer.rs`, driven by the `subscribe` frame or by positions | Distance audio, second form | a subscriber stops receiving a publisher's audio when unsubscribed |
| N6 | Call state for those who may join but have not | protocol server | A push of started, ended and participant count to a room's members | Door signs | a member not in the call sees the count change |
| N7 | Navigation inside an embedded screen | Vue component | To be decided after trying `m-dynamic-container` with panel screens | Small panels in place | links in the panel stay in the panel |
| N8 | Positions and sizes for facility locations and placed assets | data model | A companion entity to `mantle.facility.FacilityLocation` in Mantle with position, size and rotation; the same for an asset's place in a facility | Racks, plant view, later editing | generated defaults from identifiers; edited values kept |
| N9 | A 3D model as product content | seed data | A `ProductContent` type for a glTF model in Mantle | Product model viewer | upload, serve with the product's access rules |

N1 is the only one that blocks a phase outright (T4). The others have an interim: theme rows for N2, octet-stream
for N3, polling for N6, generated racks for N8.

## 19. Phases

| Phase | Deliverable | Done when |
| --- | --- | --- |
| T0 Foundation | `component.xml`, the static mount, vendored libraries with manifest and check, `moqui.three` with the shared renderer, `m-three-view` with the `box` preset, a gallery example | A box renders on the example screen; nothing 3D loads on other pages; the manifest spec passes |
| T1 Village, alone | The kit pipeline with a first exterior kit, layout documents and validation, world mode over the shell, camera and movement, buildings as doors with the manifest, the directory and `tui` twin, quality tiers | A person walks or clicks to a building and its app opens in the window; a locked door stays shut; the budgets of [§5](#5-rendering-core) are measured and recorded |
| T2 First viewers | Box type, product size, packing preview, generated warehouse racks, each injected with its text twin | Each shows on its host screen in BOSS and reads as text in `tui` |
| T3 Interiors and decorations | The interior template, two or three interiors from design-world, the source registry and space state, code-built decorations, signs bound to dashboards | State changes in the apps show in the world within a minute; every state of [§10](#10-functional-decorations) has a look |
| T4 People | N1, the presence relay, figures and their settings | Two browsers see each other move; an interior admits only the authorized |
| T5 Sound | Meeting rooms on existing calls (N4, N6), then distance audio in its first form | A 2D participant and a walk-in hear each other; outdoors, volume follows distance |
| Later | Distance audio in the server (N5); the dome for events; Assist agents as villagers; props with physics; editing in the viewers (N8); the product model viewer and PASS; 3D for Maps; the assembly canvas; a world editor; scoring | Each is its own design |

T2 depends only on T0 and can run beside T1. [design-world](design-world.md) must have the exterior plan before
T1 ends and the first interiors before T3.

## 20. Decided

| Decision | Notes |
| --- | --- |
| The village is a shell layer with the page as a window | Not a screen and not a render mode; real URLs and the whole shell keep working ([§7](#7-the-village-as-a-shell-layer)) |
| A working screen is always flat and framed | No DOM in 3D perspective ([§11](#11-screens-in-the-world)) |
| WebGL2 with three.js; one context per page | WebGPU revisited after T1 |
| Libraries are vendored in mr-three with their own manifest | An installation without mr-three ships none of them |
| Layouts are code in the component; installation state is two small entities and preferences | A world editor is later |
| Locks come from the screen's own authorization through one manifest call | Installations may hide instead of lock |
| Decorations read existing services through a registry, as the viewing user | No new business queries in mr-three |
| Walking uses the walk grid; physics is for props and loads only where they are | Keeps the first download small |
| Meeting rooms are chat rooms and their calls | One meeting for 2D and 3D participants |
| Distance audio starts in the browser and is described honestly as audible to the whole space | The server form follows with N5 |
| Art is CC0 packs through a recoloring pipeline; decorations are code-built | From the user's choice of November 17, 2026 |
| Maps stays in the framework | mr-three adds 3D layers after Maps P1 |

## 21. Open

| Question | Recommendation |
| --- | --- |
| How mr-three's own engine code is delivered | Plain ES modules under `threestatic/js/`, loaded by dynamic import with relative imports, revalidated by ETag in development; a release bundle made by the same tool that vendors the libraries, checked against its sources by a spec. No build step while developing |
| N1 as a general hook or a fixed family | A general hook for native components: Maps tracking and other add-ons will want the same, and it keeps mr-three out of `moqui-host` |
| Who owns location positions and sizes (N8) | Mantle, as optional data: picking routes and capacity checks can use them too, and Mantle already owns the location |
| Presence between cluster nodes on the `notify` transport | Two snapshots a second across nodes, ten within a node; revisit if a village spans nodes in practice |
| Package and artifact names (`three.world`, `THREE_*`, `threestatic`) | Keep until the product name is chosen ([concept §11](concept.md#11-open)), then rename once |
| The product viewer on PASS pages | After the BOSS viewer: served by PASS from the site's own assets, core bundle only, with PASS's policy unchanged if possible |
| Whether world mode restyles the header or replaces it | Restyle only: the header is where the palette, tray, Assist and account live, and replacing it would fork the shell |
| Order of T1 and T2 | T1 first for the fun of it, T2 beside it when someone wants a break from the village |
