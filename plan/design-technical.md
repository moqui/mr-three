# IronStone THREE — Technical design

November 17, 2026 — draft; nothing built. The `mr-three` component holds its README, license, design studies and specialist briefs. This
file decides how IronStone THREE is built on MR Moqui as it is today, and lists what the framework, Mantle and PASS
must gain. The
[concept](concept.md) is its fixed input; the [world design](design-world.md) decides what the spaces contain.
Names of files, services and entities are checked against the tree unless marked *proposed*; behavior marked
*unverified* has not been tried.

Sections: [1 decisions](#1-scope-and-decisions) · [2 foundations](#2-existing-foundations) ·
[3 component](#3-component-layout) · [4 libraries](#4-libraries-and-vendoring) · [5 rendering](#5-rendering-core) ·
[6 inline 3D](#6-inline-3d) · [7 shell layer](#7-the-village-as-a-shell-layer) · [8 world data](#8-world-data) ·
[9 authorization](#9-authorization-and-locked-doors) · [10 decorations](#10-functional-decorations) ·
[11 screens](#11-screens-in-the-world) · [12 movement](#12-movement-camera-and-physics) ·
[13 presence](#13-presence) · [14 audio](#14-audio-and-meetings) · [15 assets](#15-assets) ·
[16 theme packs](#16-theme-packs) · [17 mods](#17-mods) ·
[18 clients](#18-clients) · [19 testing](#19-testing) · [20 needs](#20-framework-and-data-needs) ·
[21 phases](#21-phases) · [22 decided](#22-decided) · [23 open](#23-open)

---

## 1. Scope and decisions

- **One component, two consumers of one core.** A rendering core (`moqui.three`) serves both the inline viewers
  and the village. Neither loads until a page uses it.
- **three.js on WebGL2.** WebGPU is a later option, not a baseline ([§4](#4-libraries-and-vendoring)).
- **The village is a layer of the shell, not a screen and not a render mode.** The 3D canvas sits behind the qvt
  page; the page becomes a framed window over it. Every 2D screen is the real one at its real URL
  ([§7](#7-the-village-as-a-shell-layer)).
- **The world is data.** A space is a layout document naming pieces, doors, places and decoration bindings.
  The engine draws any layout; [design-world](design-world.md) decides the layouts ([§8](#8-world-data)).
- **Four parts, kept apart.** The engine draws. A *world* says what exists and where. A *theme pack* says how it
  looks and sounds, as data only. A *mod* adds new kinds of thing and new behavior, as component code. Millrace,
  its look, its characters and the meeting venues are all built from the last three, the same way an add-on's
  would be ([§8.4](#84-worlds), [§16](#16-theme-packs), [§17](#17-mods)).
- **More than one world.** Millrace is the town, one per installation. Venues are small worlds for meetings,
  one instance per chat room ([§14.3](#143-venues)).
- **A HUD over the world**, as ordinary page content under the shell header, with a minimap generated from the
  layout ([§7.5](#75-the-hud-and-the-minimap)).
- **Entering the town takes its own grant**, given by an administrator in System, for internal and external
  users alike. It opens the exterior and the front offices and no app ([§9](#9-authorization-and-locked-doors)).
- **Front office, privacy glass, back office.** A building's front office holds the division's PASS functions and
  is open to everyone in the world; its back office is the BOSS app, behind a locked door and a privacy window
  that the server enforces, not only the renderer ([§9.3](#93-front-office-back-office-and-the-glass)).
- **Authorization is the apps' own.** A door's lock is the answer of the same check the screen itself would make,
  and live state is fetched as the person looking at it ([§10](#10-functional-decorations)).
- **The framework stays light.** It gains small, mostly reusable pieces ([§20](#20-framework-and-data-needs)); the
  JavaScript libraries, the art, the engine and anything else bulky live in mr-three.
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
| Finding files in every component | `ec.factory.getComponentBaseLocations()` lists components in load order, and `ec.resource.getLocationReference(loc)` gives `exists`, `getDirectoryEntries()` and `getText()` ([JS-Core-API](../../../../../docs/dev/JS-Core-API.md)); `runtime/components/base/tools/test/AdminMounts.spec.js` walks every component's `screen` this way. `component.xml` `depends-on` orders loading and leaves out a component whose dependency is missing | The framework scans a few directories of its own (`report/*.json`, `screen-extend`). No add-on defines a directory other components contribute to; `mod/` would be the first |
| A package of data, validated | The Shop theme package ([Themes](../../../../../docs/ui/Themes.md)): a `theme.json` manifest, typed settings with a schema, locales, assets by digest. Mantle reads a starter from `component://<component>/theme/<name>`, keeps drafts at `dbresource://` and pins a release by version and SHA-256 (`mr-mantle/service/mantle/public/Themes.js`) | That renderer is for pages. THREE reuses the conventions, not the code |
| Stored documents with history | `DbResource` files with versions, diffs and a change log through the resource reference ([Revision-Management](../../../../../docs/data/Revision-Management.md)) | Nothing |
| The shell's colors | `ScreenColorScheme` tokens per light and dark mode as CSS variables, the `QUASAR_DARK` preference ([Qvt-Client](../../../../../docs/ui/Qvt-Client.md)) | Nothing; world-mode chrome reads the same variables |
| Sharing a screen in a call | `getDisplayMedia` as its own track, published with `source: "screen"`; each viewer subscribes per publisher at a high, mid, low or no layer (`webroot/js/WebrootCall.js`, `crates/moqui-rtc/src/sfu/`) | Roles are Host, Member, Guest and Forward: no presenter, audience or listen-only role |
| Co-editing | The `collab` family on `/notws`: Yjs, with `yrs` on the server, for wiki page text. Marketing links an event to a call with a `CallLink`; Calendar shows a `joinUrl` | The document key is fixed to `WikiPage:<id>`, and a wiki page has no type beyond its file extension. No whiteboard or canvas document exists or is planned in the tree |
| Sending a file by location | `ec.web.sendResourceResponse(location, inline, filename)` | Its caching headers are *unverified* |

## 3. Component layout

*Proposed.* Following a component's directories in [Practices-App-Dev](../../../../../docs/dev/Practices-App-Dev.md):

| Path | Contents |
| --- | --- |
| `component.xml` | Depends on `mr-boss` (and through it Mantle). PASS is optional: its extensions load only when `mr-pass` is present |
| `MoquiConf.xml` | Mounts `threestatic` under the root screen (anonymous view, as `devstatic`) and the `village` app under the apps screen with its own launcher entry |
| `screen/village.xml`, `village.qvue`, `village.tui.json` | The village's app root, shown in the launcher as IronStone THREE: entry, the text directory, settings. Its artifact group is the world grant |
| `screen/village/` | Settings, the administrator's screens for signs and rooms, and the transitions the town calls |
| `screen/venue.xml` | Sibling screen for a meeting venue. Resolves the bound chat room and checks the signed-in user's right to join its call |
| `screen/threestatic.xml`, `screen/threestatic/` | Static files: `libs/` (vendored libraries), `js/` (the engine's modules), `css/`; and the transition that sends a registered mod's `static/` files ([§17.1](#171-the-directory)) |
| `screen-extend/` | The inline viewers injected into BOSS and framework screens; the AppList `uiModes` entry |
| `service/ironstone/three/` | Services: the mod registry, doors, space state, signs, rooms, actions, theme and layout validation |
| `service/ironstone/three/state/` | mr-three's own world-state services ([§10.1](#101-world-state-services)): what waits at each door, the handoffs of the two trade routes, the day, the ridge terrace |
| `mod/` | mr-three's own mods ([§17.5](#175-the-built-in-mods)): `core`, `millrace`, `cast`, `venues`, `walks`. The worlds, layouts, theme packs, characters and their client modules live here and nowhere else |
| `entity/`, `data/` | The few entities of [§8.3](#83-installation-state); security data (the world grant's groups), theme resource rows, seeded rooms |
| `src/`, `Cargo.toml` | The presence relay, when the framework offers a socket to register ([§13](#13-presence)) |
| `tools/` | Development scripts that run on Node, not at build or test time: `refresh.mjs` for libraries, `kits.mjs` for art |
| `test/`, `test/client/`, `test/acceptance/` | Specs ([§19](#19-testing)) |
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
rest on demand: `load()`, `view(el, spec)` for a viewer, `enter(worldId, instanceId)` and `leave()` for a world.

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
| Product model | `model` | `Shared/Product360`; later the PASS Shop product page | None | A 3D model content type, N9; PASS delivery, [§23](#23-open) |
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
  the window restores the active world's route and focus. In town that is `/qapps/village`. In a venue it is
  `/qapps/venue/<chatRoomId>` for the same call, including for someone who entered directly from a meeting link.
- **So everything in the shell still works** in world mode: the palette, quick create, the tray (Today, Chat,
  Notifications), the Assist drawer and its screen context, floats, the live call window, the browser's back
  button, a link pasted to a colleague.
- **In town, place follows route both ways.** Walking to a door opens its screen; and when the route changes by any other
  means (the palette, a notification, Assist following along), the avatar is moved to the place whose door or
  bay owns the longest matching path. The palette is therefore the fast travel.
- **A reload** in world mode at an app URL shows the screen in its window at once and loads the world behind it
  afterward; world mode itself is remembered per tab.
- **Phones:** below 600 px the window is the full screen with a return bar labeled for the current town or venue; there are no floats
  at that width today either.

### 7.3 The village screen and the directory

`/qapps/village` is an ordinary app root, titled IronStone THREE. Its artifact group is the world grant of
[§9.1](#91-the-world-grant). It gates the town entry, launcher tile and directory. The sibling venue screen
checks the named call's admission and offers that meeting's directory. Inline viewers load on their host screens.

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

### 7.5 The HUD and the minimap

What belongs to the person rather than to a place is shown in a heads-up display. [The atlas](design-world.md#the-hud-and-the-minimap)
decides its elements and layout; this section decides how it is built.

- **A DOM layer, not WebGL.** The HUD is a Vue component (`m-three-hud`, registered with `qvt.registerLate`)
  in an element above the world canvas and below the shell header and the page window. Text is the shell's own:
  sharp, localized, focusable, read by a screen reader. Pointer events pass through its empty areas to the world.
- **It does not fork the shell.** The header keeps the palette, tray, Assist and account. The work tracker shows
  three lines from the shell's counts and today items and hands off to the tray's Today. The sound panel drives
  `moqui.call`. The scope pennant opens the app's own scope selector.
- **Elements and their data:**

  | Element | Data | From |
  | --- | --- | --- |
  | Place line | World, space, nearest station | The loaded layout and the route |
  | Scope pennant | Active organization, facility, store | The scope the space state was read under ([§10](#10-functional-decorations)) |
  | Work tracker, waypoint | Counts; the first items; the tracked item's place | `org.moqui.ui.ShellServices.get#ShellCounts` and its push; `mantle.work.WorkEffortServices.get#UserTodayItems`; the place that owns the item's route ([§7.2](#72-how-it-sits-in-the-page)) |
  | Sound | Listening, microphone, the sound space's name and audience, recording | Presence and `moqui.call` ([§14](#14-audio-and-meetings)) |
  | Satchel | Up to five record references | `THREE_SATCHEL` (*proposed*): a JSON list of `{type, id, label, path}`. A station offers "open here" for a type it declares; the shell's `record-link` providers resolve the path. No server store beyond the preference |
  | Action prompt | The focused place's marks and labels | The layout and the door manifest |

- **Preferences.** `THREE_HUD` is `full`, `minimal` (minimap, action prompt, sound) or `off`.
  `THREE_MAP_ROTATE` turns the minimap with the camera. The HUD hides while the page window is open, except the
  sound panel.
- **Keys** are in the world's `moqui.keymap` scope ([§12](#12-movement-camera-and-physics)): M for the full map,
  1 to 5 for the satchel, Enter for the action prompt.
- **The minimap is generated, not drawn by hand.** A pure module turns a layout document into 2D shapes: the
  walk grid's blocked cells as footprints, roads, water, doors, station anchors. It paints them once per space
  and theme onto a 2D canvas in the pack's colors and glyphs ([§16](#16-theme-packs)), then draws markers over
  that base at most ten times a second. It makes no second 3D render and costs no WebGL context. The same module
  draws the minimap, the full map (M, or the gate's map table) and T1's village map, so none can drift from the
  world. The atlas's SVG stays an illustration.
- **What a marker may be.** The minimap draws only what this browser already holds: the door manifest (a street
  door as open or shuttered), the viewer's space state (doorstep counts, rooms in session), the tracked item,
  and presence records. Presence already sends a front-office viewer only veiled records for the back zone
  ([§13](#13-presence)); the minimap draws no veiled record at all, and leaves a back zone blank for a viewer
  the door manifest does not admit. It asks the server for nothing of its own.
- **Indoors and in a venue** the same module draws the room plan or the seating plan from that space's layout.
- **Phones.** Below 600 px the minimap is a compass button with the waypoint arrow, the tracker one line, the
  satchel a drawer.
- **Slots.** The HUD has named slots (beside the tracker, above the satchel, in the sound panel) and the
  minimap takes extra marker layers; both are how a mod adds to it ([§17.2](#172-scripts)).
- **Text twin.** Everything in the HUD is in the tray or the directory. The directory lists places nearest
  first, which is the minimap as text.

## 8. World data

### 8.1 Layout documents

A space is one JSON document in a mod's `world/<worldId>/` directory ([§17.1](#171-the-directory)), owned by
its component as code is. The shape, not final:

```json
{
  "space": "accounting",
  "kind": "interior",
  "of": "Accounting",
  "grid": { "cell": 1.0, "width": 16, "depth": 15 },
  "pieces": [ { "piece": "desk-large", "at": [6, 0, 3], "turn": 90 } ],
  "zones": [
    { "id": "front", "kind": "front", "area": [0, 11, 16, 4] },
    { "id": "back", "kind": "back", "area": [0, 0, 16, 11], "screen": "Accounting",
      "door": { "at": [8, 0, 11], "turn": 0 }, "glass": [ [2, 11, 6, 11] ] }
  ],
  "places": [
    { "id": "cashier", "zone": "front", "kind": "counter", "area": [1, 12, 4, 2], "public": "billing" },
    { "id": "receivables", "zone": "back", "kind": "station", "area": [1, 6, 4, 3],
      "screens": ["Accounting/Receivables"], "create": true },
    { "id": "index", "zone": "back", "kind": "index", "area": [12, 8, 3, 2] },
    { "id": "close", "zone": "back", "kind": "secured", "area": [0, 0, 4, 3], "screens": ["Accounting/PeriodClose"],
      "door": { "at": [4, 0, 2], "turn": 0 } }
  ],
  "anchors": [ { "id": "entry", "at": [8, 0, 14] } ],
  "decorations": [
    { "id": "overdue", "place": "receivables", "kind": "amount", "look": "ruled-book", "at": [3, 0, 7],
      "source": "accounting.summary", "value": "arOverdue", "screen": "Accounting/Receivables/Collections" },
    { "id": "unmatched", "place": "banking", "kind": "stack", "look": "coin-tray", "at": [3, 0, 4],
      "source": "accounting.summary", "value": "unmatched", "screen": "Accounting/Banking" }
  ],
  "slots": [ { "id": "wall-east", "zone": "back", "area": [13, 2, 2, 5], "takes": ["station", "decoration"] } ],
  "rooms": [ { "id": "meet", "zone": "front", "area": [12, 12, 3, 3], "room": "accounting-front" } ]
}
```

- **Units are meters on a grid**, so pieces from modular kits snap together and the walk grid falls out of the
  layout: a cell is walkable unless a piece or a wall covers it. A room is sized from its stations: about
  16 × 12 for a staff house, 16 × 15 for a paired house with a 4 m front strip, and never the size of the house
  drawn on the exterior. A door is a portal, so the two need not agree.
- **A layout names no file.** `piece` is an identifier from the piece vocabulary and `kind` and `look` are a
  decoration kind and one of its named looks ([§10](#10-functional-decorations)). The active theme pack maps
  each to a model, a material and a motion ([§16](#16-theme-packs)), so the same layout draws in any pack that
  fits its world.
- **Stations.** A place of kind `station` is a piece of furniture for one step of the work. It lists the
  screens whose books it holds, may offer the app's quick-create entries (`create`), and owns the decorations
  that name it. A place of kind `index` is the cabinet with a book for every menu section, filled from
  `menuData` for the viewer.
- **Slots** are named areas where another mod's patch may add a station or a decoration ([§17.3](#173-lots-slots-and-generated-rooms)).
  Nothing is added outside a slot.
- **Zones** divide an interior into its front office and back office. A back zone names the app's root screen,
  its door and the runs of wall that are privacy glass. Every place, decoration and room belongs to a zone.
- **Enclosures.** A zone, a secured place and a meeting room are enclosures: walled, entered only by a door, and
  each its own sound space ([§14.2](#142-distance-audio)). The exterior is the one open space.
- **A door is a portal.** It has a spot on each side and is never drawn open. Using it moves the figure from one
  spot to the other ([§12](#12-movement-camera-and-physics)).
- **`screen` is a path under the apps root**, never a component location or a URL from elsewhere. **`public`** is
  the name of a PASS app (`billing`, `support`), resolved to the site's own path for it.
- **`source` is a name from the source registry** ([§10](#10-functional-decorations)); a layout never names a
  service, an entity or a query.
- **The exterior** is one document of kind `exterior` with terrain heights, buildings as places whose doors lead
  to interior spaces, the lots held for buildings that arrive later, and the dome.

### 8.2 Validation

A layout that is wrong should fail a spec, not surprise someone in the world.
`ironstone.three.LayoutServices.check#Layouts` (*proposed*) verifies for every document: each piece is in the
vocabulary and every pack that fits the world maps it; each decoration kind and look is defined, with a
directory sentence; each `screen` resolves to a screen and each `public` to a PASS app; every item's zone exists
and contains it; each `source` is registered and each `value` is one the source declares; every place and anchor
is reachable on the walk grid from the entry; areas do not overlap; a patch adds only inside a slot that takes
its kind; identifiers are unique. One bad document, patch or mod is reported by name and the others still load.

### 8.3 Installation state

Layouts are code. What an installation changes is little, and lives in entities (*proposed*, package
`ironstone.three`):

| Entity | Holds |
| --- | --- |
| `WorldSign` | A sign place bound by an administrator to a dashboard panel or a playlist: `spaceId`, `placeId`, `definitionId`, `panelId`, `title` |
| `WorldRoom` | A chat room bound to a place to meet: `chatRoomId`, then either `roomPlaceId` (a meeting room place in the town) or `worldId` (a venue to instantiate for it, [§14.3](#143-venues)), an optional `themeId`, and optionally the organization it is for. A place with no row has no meeting door |
| `WorldModSetting` | Whether an installed mod is enabled, and its installation settings as JSON: `modId`, `enabled`, `settings` |
| `WorldModState` | A small key-value store for mods that need no entity of their own: `modId`, `scopeEnumId` (world, space or user), `scopeId`, `stateKey`, `stateValue` (JSON, size-limited) |
| `WorldSourceNormal` | The normal range an administrator sets for one reading, from which its levels are stepped: `sourceName`, `valueName`, scope fields, `lowValue`, `highValue` |

A person's own state is preferences: `THREE_AVATAR` (figure and colors), `THREE_PLACE` (last world, space and
anchor), `THREE_QUALITY`, `THREE_AUDIO`, `THREE_START`, `THREE_THEME`, `THREE_HUD`, `THREE_MAP_ROTATE`,
`THREE_SATCHEL`, and `THREE_MODS` for the optional mods a person has turned off or on.

An installation's settings for a theme pack (accent color, banner image, town name) are a document at
`dbresource://three/theme/<themeId>/settings.json`, with the store's own revisions.

Editing layouts in the installation (a world editor) is later. When it comes it writes an *installation mod*:
the same directory shape as any mod, kept under `dbresource://three/mod/` with revisions, holding patches and
layouts and no scripts, and validated by the same service. There is no second storage design.

### 8.4 Worlds

A world is a set of spaces with one way in. It is declared by `world.json` beside its layouts:

```json
{
  "world": "millrace",
  "title": "Millrace",
  "kind": "town",
  "entry": { "space": "exterior", "anchor": "gate" },
  "spaces": ["exterior", "accounting", "warehouse", "projects"],
  "themes": ["millrace", "millrace-plain"],
  "sound": "ambient",
  "capacity": 50
}
```

| Kind | Instances | Bound to | Entered with |
| --- | --- | --- | --- |
| `town` | One per installation; its instance is its world | The apps, through places with screens | The world grant ([§9.1](#91-the-world-grant)) |
| `venue` | One per chat room that names it in `WorldRoom`; the instance is the `chatRoomId` | That room's call | The right to join that call ([§14.3](#143-venues)) |

- **The engine holds one world at a time.** Entering another disposes the scene and keeps the cached kits and
  libraries. `moqui.three.enter(worldId, instanceId)` replaces the argument-less form of [§5](#5-rendering-core).
- **A world's URL.** `/qapps/village` is the town. `/qapps/venue/<chatRoomId>` is a venue instance.
  The two sibling screens share the page window, HUD and directory components. The active world supplies their data.
- **Presence and sound are keyed by world instance and space**, so two meetings in the same venue template
  never see or hear each other.
- **Themes.** A world lists the packs that fit it. A pack fits when it maps every piece and look the world's
  layouts use; validation checks it.
- **Where a person was.** `THREE_PLACE` records the last place per world. Leaving a venue returns to the place
  in the town, or to the screen, that the person came from.
- **Which worlds exist** is whatever the enabled mods declare. mr-three ships `millrace` and four venues
  ([design-world-venues](design-world-venues.md)).

## 9. Authorization and locked doors

Three layers, each the answer of a check that exists or is ordinary security data:

| Layer | Opens | Decided by |
| --- | --- | --- |
| The world grant | The exterior, every front office, the general meeting rooms | Membership of the world's user group |
| An app's authorization | That building's back office | The app root screen's own artifact authorization, as in the 2D apps |
| A section's authorization | A secured area inside a back office | The section screen's authorization or permission |

### 9.1 The world grant

- **A user group of its own**, `THREE_WORLD_USERS` (*proposed*), with an artifact group `THREE_WORLD` over the
  village screen and the town's services. Nobody is in it by default except administrators. An administrator
  adds people in System › Access (Groups), the same screens that manage every other group.
- **Internal and external users alike.** A member of staff needs the grant to enter. So does a PASS public
  account (a customer, a supplier contact, a community member), which otherwise holds only the all-users group.
  The grant is a framework user group, not a PASS site role, because the world is not a site.
- **It opens no app.** With only the grant a person can walk the exterior, enter front offices, use general
  meeting rooms they are a member of, and see locked doors. Everything else needs what it needed before.
- **Checked by the server**, on the village screen, on town services and on joining town presence. The venue screen, venue services and venue presence check admission to the named call.

### 9.2 Doors

- **The door manifest.** `ironstone.three.WorldServices.get#Doors` (*proposed*) takes a space and returns, for each
  zone, door and place with a `screen`, one of `open`, `locked` or `absent`. It asks
  `ec.artifactExecution.isPermitted` for the screen as the current user, the same question the menu asks. The
  client only draws the answer; a locked door is also refused by the server if someone forces it, because opening
  it is a request for the real screen. A place with a `public` app is `open` when the installation's PASS site
  offers that app and `absent` otherwise.
- **Why a new call.** `shellData` and `menuData` leave out what a user may not view, deliberately: they do not
  reveal which screens exist. The manifest reveals exactly the doors the layout declares and nothing else.
- **What a lock reveals, and the setting for it.** A locked door tells a person that an app or a section exists.
  The back-office door of a building is always drawn: the building itself already says the division exists. For
  secured areas inside a back office, an installation that prefers silence sets `three_locked_doors` to `hide`,
  and an area the person may not open is drawn as plain wall (`absent`). Doors inside a back office are evaluated
  only for someone who may enter it.
- **Menu conditions.** Some sections are shown by an expression rather than by authorization (a `menu-include`
  with a permission check, as Sales' Settings has). A place for such a section names the same permission in its
  layout, and the manifest evaluates it; otherwise its door would open onto an empty screen.
- **Three looks from three answers.** The manifest's answers do not change; what is drawn does. A building's
  street door that is `open` has open shutters and a painted door. One that is `locked` is *shuttered*: closed
  shutters, a plain door, and on selection the app's name and who grants access. Only a secured area inside a
  back office the person has entered is drawn with a lock. So someone with one app sees one open house and a
  quiet street. `absent` is plain wall, as before.
- **The works wall** is a place of the exterior with an arch on each lane that enters it. The arches are
  `open` together when any building inside is, and shuttered otherwise; the wall has no authorization of its
  own.
- **Stations.** A station lists several screens. Each of its books is `open`, `locked` or `absent` on its own,
  and a station with no open book is drawn covered.

### 9.3 Front office, back office and the glass

The privacy window is a rule about data first and a material second. What a browser is not sent, it cannot show.

| | Seen from the front office | Seen from the back office |
| --- | --- | --- |
| People in the back | Plain figures: a position, moving or still. No identity, figure style, name or status | In full |
| People in the front | In full, under the identity rules of [§13](#13-presence) | In full, through a tinted pane |
| Back-office decorations | The static piece as a plain shape. No level, color or text, because no state is sent | In full |
| Back-office screens and signs | Nothing | In full |
| Sound | None passes | None passes ([§14.2](#142-distance-audio)) |

- **The server keeps the glass.** The presence relay sends a front-office member only veiled records for
  back-office figures, and space state returns nothing for back-zone sources to someone not authorized for the
  app. A modified browser sees shapes behind the glass and nothing more.
- **The renderer draws it.** For a viewer in the front, the glass is a frosted pane and what is behind it is
  drawn in one flat tone at reduced contrast. For a viewer in the back, it is a mostly clear pane with a visible
  tint and pattern, so it reads as a privacy window from both sides. Which look is used follows the viewer's own
  authorization, not where their camera is.
- **"Things happening"** behind the glass are veiled people and static furnishings. Data-driven movement is visible only inside its authorized zone. An installation may set the glass opaque.
- **A back office with no front** has no glass unless its layout gives it a lobby; its front door is the locked
  one.

### 9.4 What is secret and what is not

- **Art is not secret; state is.** Kit files and layouts are static files, served to any signed-in user. They show
  a generic accounting office, not this company's accounts. Everything that reflects data comes from services
  that run as the person and apply their authorization, organization filters included.
- **Front offices show a person their own business.** A front-office decoration ("your unpaid invoices") reads
  the same Mantle services the PASS page reads, as the person, so it shows what that page would show them.
- **Organization.** The world calls the same services the screens call, after the same
  `mantle.party.PartyServices.setup#UserOrganizationInfo`, so a member of staff sees their organizations' state
  and no other. Rooms for an organization are offered to its members ([§14](#14-audio-and-meetings)).

## 10. Functional decorations

- **The source registry.** A source name maps to a service and the values it exposes. Sources are of two
  sorts. A room's readings come from its app's own Home service: `accounting.summary` is
  `ironstone.accounting.HomeServices.get#Summary`, `warehouse.home` is
  `ironstone.warehouse.HomeServices.get#Home`, `insights.attention` is the Survey loft's own
  `ironstone.insights.RegistryServices.get#Attention`. Readings that belong to the world rather than to one
  room come from services mr-three writes for the purpose ([§10.1](#101-world-state-services)). Beside them are
  `shell.counts` (`org.moqui.ui.ShellServices.get#ShellCounts`) and one generic `dashboard.panel` over
  `org.moqui.report.ReportServices.render#Dashboard` for signs. The registry is assembled from the `sources` of
  every enabled mod's `mod.json` ([§17.2](#172-scripts)): mr-three's own mods declare the ones above, and a
  component with an app of its own declares its Home service and its world-state services the same way. An entry names the
  service, the values it may expose with their units, the scope it is read under, the screen whose
  authorization guards it, and the zone kind it belongs to. Sources are declared in component files; a browser
  never supplies a service name.
- **Decoration kinds.** A decoration binds a value to a *kind*, which is a data contract and a way of drawing:
  stack, amount, level, balance, progress, time left, trend, flag, slips, pegs, tray, bell and model
  ([the atlas](design-world.md#decoration-kinds)). A kind is defined once, by `mod/core` or by another mod, as
  a declaration (the values it takes, its states, its directory sentence as a template) and a client renderer.
  The server needs only the declaration, so the directory and `tui` render a kind they have never drawn. The
  theme pack supplies each kind's looks: which pieces make a "sling" or a "coin tray".
- **One call per space.** `ironstone.three.WorldServices.get#SpaceState` (*proposed*) runs each source the space uses
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
- The accounting summary binding projects `summary.arOverdue` and its currency for the selected organization and the Home AR-reader condition. PASS inserts use the same site authority, session and operation as the actual public page. Until that adapter is available the place opens the real page.
- **State follows the zone.** A source used only by back-zone decorations is not run for someone who is not
  authorized for the app, and nothing is returned for it, not even `withheld`: through the glass there is no
  state to draw ([§9.3](#93-front-office-back-office-and-the-glass)).
- **Mapping to a level.** `steps` turn a number into a level (a stack of 0, 1, 2 or 3 crates), a status into a
  flag, a date into an hourglass. The steps for a count come, in order, from the source's own condition where
  it returns one, from the `WorldSourceNormal` range an administrator set for that reading and scope, and from
  the kind's defaults in the theme pack. The exact value is always on the label.
- **Two marks belong to the data, never to the business.** Stale and failed describe the *reading*. A business
  failure (a failed batch, a failed check, a bounced delivery) is an `ok` reading with its own value and look.
  The age of work is a time-left decoration. Validation refuses a look that reuses the stale or failed marks.
- **Street readings are per viewer.** Each browser draws its own scene, so an exterior decoration is bound and
  read exactly as an interior one is: as the viewer, under their scope, with `withheld` or nothing for everyone
  else. The exterior is read with `shell.counts` and one call to mr-three's own street service
  ([§10.1](#101-world-state-services)). No app's Home service runs because someone is standing in the street,
  and nothing on the street depends on a person having access to Insights.
- **Refresh.** On entering a space, then once a minute while in it, and at once when a `shellCounts` push says
  something changed. Home services are not cheap, so each answer is cached for the interval under the viewing user, source and its effective scope (organization, site, facility, store or selected record). Discard an old reading on a scope or permission change.
- **Signs.** A sign bound in `WorldSign` shows a dashboard panel's value, title and as-of time drawn to a canvas
  texture; selecting it opens the dashboard. A panel the person may not read comes back "Not available" from the
  report service, which is the `withheld` state.
- **The dome** in the town is the sky and the far limit and carries no reading. A presentation is shown on the
  screens of a venue ([§14.3](#143-venues)), to people who joined that meeting. A dashboard anywhere in the
  world is sign-style number cards only: the browser cannot paint a DOM dashboard onto a texture.

### 10.1 World-state services

Some state belongs to the world, not to one room: what waits at each door, how much stands at each handoff of a
trade route, what day it is in town, the few readings on the ridge. No app's Home service answers these, and
the nearest existing answers are Insights' attention registry and flow maps. Those are one app's internals:
they need Insights access, they do more than the street needs, and the street should not stop working for
someone who has one app. So mr-three has services of its own for world state that comes from the database.

- **One call for the street.** `ironstone.three.WorldStateServices.get#Street` (*proposed*) returns, for the
  viewer in their scope, the five parts below. Each part and each entry in it has its own state, and one that
  fails leaves the rest in place.

  | Part | Holds | Read from |
  | --- | --- | --- |
  | `doorsteps` | Per building whose door is open to the viewer: a count of what waits for them there, an optional overdue part, and the screen and parameters that list exactly those rows | One doorstep service per building |
  | `handoffs` | Per stop of the two trade routes the viewer may read: a count and its list | One handoff service per stop, named in the `millrace` world's route file |
  | `jobs` | The first few unassigned items in the viewer's own queues, each with its record link | One job-board service per app that has queues: Customer Service and Maintenance at first |
  | `day` | Whether today is a holiday at the viewer's facility, and the events of today they may read | Mantle's calendar and work-effort entities |
  | `terrace` | The active organization; the open period's checks passed and blocking, and its end date; the readiness checks not ready | Scope selection; `mantle.ledger.CloseServices.get#Checklist`; `org.moqui.admin.ReadinessServices.get#Overview`, each only for a viewer it answers |

- **A doorstep service** implements `ironstone.three.WorldStateInterfaces.get#Doorstep` (*proposed*): in, the
  scope (organization, facility, store as the app uses them); out, `count`, `overdueCount`, `screen`,
  `parameters`, `asOf`. mr-three writes one for each BOSS and framework app, in
  `service/ironstone/three/state/`. A component with an app of its own writes its own and names it on its lot
  ([§17.2](#172-scripts)). What each one counts is in the [building sheets](design-world-buildings.md#what-waits-at-each-door).
- **A handoff service** has the same shape for one stop of a route: orders approved and waiting to be picked,
  shipments packed and not yet invoiced, and so on. The stops are data in the `millrace` world, so a route can
  gain a stop without engine work.
- **The rules every world-state service keeps:**
  1. *It reads as the viewer.* After `mantle.party.PartyServices.setup#UserOrganizationInfo`, with the same
     organization, facility and store filters the app applies. It is not called for a building whose door the
     manifest does not open, and it checks the owning screen's authorization itself as well.
  2. *It counts and nothing else.* No write, no status change, no business rule. It decides nothing an app
     decides.
  3. *It mirrors a named list.* Its description names the app screen and filter whose rows it counts, and it
     returns that screen and those parameters, so selecting the basket opens the same rows. A count that cannot
     be opened as a list is not offered.
  4. *It is checked against that list.* A regression spec asserts, on the demo data, that the count equals the
     list's row count for the same user and scope ([§19](#19-testing)). This is what keeps a query owned by
     mr-three from drifting away from the app it describes.
  5. *It says why a value is absent.* It returns one of the six states with its scope and as-of time, never a
     bare null, which the adapted Home services cannot always do ([the atlas](design-world.md#6-functional-decorations)).
  6. *It is cheap.* An indexed count through an entity or a view, with no rendering and no per-row work. The
     street call has a time budget; a part that exceeds it answers `stale` from the cache or `failed`, alone.
  7. *It depends on Mantle and the framework, not on Insights.* The Survey loft still reads Insights' own Home,
     as any room reads its app.
- **Caching and refresh** are those of every source: per viewer, source and scope, for the interval, dropped
  on a scope or permission change, and refreshed at once when a `shellCounts` push arrives.
- **Where a custom service is right in a room too.** The default for a station stays its app's Home service,
  which keeps the room and the app's own Home in step. A world-state service replaces it for a reading only
  where the Home service is too heavy for a once-a-minute refresh, has the wrong shape, or cannot say why a
  value is missing. The same seven rules apply.

## 11. Screens in the world

| Distance | What is shown | How |
| --- | --- | --- |
| Far | A summary: a number, a level, a status | Canvas-texture text and code-built pieces from space state |
| Near | The object reacts: a book opens, a sign turns | Animation, then the window |
| Open | The real screen | The page window of [§7.2](#72-how-it-sits-in-the-page), framed in the style of what was opened |

- **One mechanism.** Books, signs, desks and doors all end in the page window at a real URL. They differ in the
  path they open and the emblem on their shared brass-cornered frame.
- **Front-office places open PASS pages.** PASS screens are server-rendered pages in the public site's own shell,
  not screens of the `/qapps` client, so they cannot load into the page as a BOSS screen does. The window shows
  them in a frame of the PASS page itself, with the same border. PASS pages refuse framing today
  (`frame-ancestors 'none'`), so PASS must allow the village's own origin for them (N10). N10 is a
  response-header change and ships with the front offices in T3, so the first front office does not throw its
  visitor out of the world. A site whose policy forbids framing opens in a new tab.
- **Acts with a real effect** are few and explicit. One rule governs them: an act in the world may travel, open
  a screen, start a draft, or change the person's own reversible state. Anything that posts, pays, approves,
  receives or ships is finished in the real form. Each act is an entry in the `actions` of a mod's `mod.json`
  ([§17.2](#172-scripts)), naming the owning app's service, the place kinds that may offer it and whether it
  asks for confirmation. The village screen's one `run#Action` transition (*proposed*) looks the act up by
  name, checks the place is one the viewer may use, and calls the service as the viewer, which makes its own
  permission checks. mr-three holds no business rule and bypasses none. The first acts are in
  [the atlas](design-world.md#acts-with-a-real-effect).
- **Create.** A station with `create` shows a blank slip for each `quick-create` entry of the app's shell
  providers that `org.moqui.ui.ShellServices.offer#QuickCreate` offers the viewer. Selecting one opens that
  entry's own screen in the window.
- **Small panels in place.** A notice board or a room's sign may show a panel screen (the tray's Today, a room's
  roster) in a float or a small anchored panel, using `m-dynamic-container` as the tray does. Whether links
  inside stay inside is the screen's behavior; mr-three uses this only with panel-archetype screens made for it.
- **Not done: DOM in 3D perspective.** Projecting live screens onto surfaces with CSS 3D transforms blurs text,
  breaks focus and input on phones, and makes a working screen worse. A screen in use is always flat and framed.

### 11.1 Specialist stations and Assist

A specialist's station is a place with a proposed `agentId` binding and a separate ordinary screen opener. The directory and the world resolve **Ask** through the same authorized place. Reuse Assist’s offered-agent list and new-conversation agent selection. A small shell hook must select an offered specialist and open the drawer with the authorized station context. The mod loader recognizes a specialist by a `station.json` directly inside a named child directory of the mod. It describes the place, body piece and poses. The role brief is the sibling `AGENT.md`. Each definition points its `instructionsLocation` at that file, `component://mr-three/mod/cast/<name>/AGENT.md` for the seven mr-three ships. Specialist files are server-side. Assist resolves the requester's Constitution for each new run and retains its snapshot through a pause. Read/navigation tools run as the viewer. Recheck zone and source grants when resuming away from the station. A PASS record needs a site-scoped read adapter or a link into the real page. Local speech poses reflect the viewer’s conversation only. [Cast, bindings and limitations](design-world-agents.md)

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
  screen's, as they are today. Esc closes the window. The scope also holds the HUD's keys
  ([§7.5](#75-the-hud-and-the-minimap)).
- **The guide line.** Tracking an item in the HUD draws the walk-grid path from the figure to the item's door
  or station as a dotted ribbon on the ground, redrawn when the figure leaves it. A guided walk uses the same
  ribbon in another color, with a numbered post at each stop.
- **Doors.** Walls and doors are always closed cells of the walk grid. A figure that reaches a door it may use
  is moved to the door's far side with a short fade; the door itself does not animate open, so there is never an
  open doorway to draw, see through or hear through. A path that crosses a door walks to it, passes, and walks
  on. Entering a building from the exterior is the same act, with the interior loaded during the fade.
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
- **Rooms.** A presence room is a space of a world instance ([§8.4](#84-worlds)). The town's exterior is one
  room and each interior is one; each venue instance has its own. Joining a room of the town needs the world
  grant ([§9.1](#91-the-world-grant)); joining a venue's needs the right to join its call.
- **Short messages between people.** Besides positions the relay carries small typed messages to the others in
  a room (N11): the built-in ones are *point* (a marker on a place for a few seconds), *follow* (an offer to
  walk together and to open the same screen), *knock* (a request to the members of a meeting room), an emote,
  and the counter bell's flag. A mod sends its own through `api.net`. The server stamps the sender, limits
  size and rate, and delivers by zone exactly as it delivers positions: a message sent in a back zone reaches
  only those authorized for the app. The counter flag is the one message that crosses the glass, front to
  back, and it carries no identity.
- **Two audiences in an interior.** Each member is in the front zone or the back zone, and the server admits a
  position in the back zone only from someone authorized for the app. A member authorized for the app receives
  everyone in full. Anyone else receives front-zone figures in full and back-zone figures veiled: a position and
  a moving flag under an identifier that changes with each visit, at a lower rate, with no user, name, figure or
  status ([§9.3](#93-front-office-back-office-and-the-glass)).
- **Identity.** Staff appear to everyone under their display name. An external user appears to everyone as
  "Visitor" unless they turn on `THREE_SHOW_NAME` (*proposed*), which shows the name registered on their account.
  There is no other name to enter. The server writes the name into each record; a browser never supplies one, and
  a private visitor's user is not in the records others receive.
- **Who moves the figure.** The browser does, and reports; the server relays. The server sets the identity
  (user, display name, avatar from the user's preference), limits speed, and refuses positions inside a zone or
  area whose door is locked for that user. Nothing of value depends on a position, so this is enough.
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
  ([§23](#23-open)).

## 14. Audio and meetings

### 14.1 Meeting rooms

- **A village meeting room is a chat room.** `WorldRoom` binds a room place to a `ChatRoom`; its meeting is that
  room's `CallSession`. Someone in Comms' Calls screen and someone who walked through the door are legs of the
  same call on the same media server. Who may enter is who may join that room's call today.
- **Only bound rooms have doors.** A layout may give every building a room place. A place with no `WorldRoom`
  row draws no bracket and no door. mr-three seeds the two general rooms; an administrator binds the rest.
- **Kinds of room.** General rooms are chat rooms seeded by mr-three whose members are those with the world grant;
  how a room open to "all users" treats external accounts today is *unverified*, so membership is explicit. A
  building's front room is a room for that app's people. A room for an organization is offered to its members;
  binding it to the organization's party record as a record room is the likely form (*unverified*).
- **Walking in** joins the call (`org.moqui.call.CallServices.join#Call`, or starts it when nobody is there).
  Starting a room's call today rings every member; a walk-in must not, which needs N4.
- **Who is inside.** `get#Call` lists participants. Those with a figure in the room are shown where they stand;
  those who joined from 2D or by phone have a seated place marker and a roster entry, so nobody in the meeting is invisible.
- **The door sign** shows free or in session and how many are inside, to people who may join. Live call state is
  pushed only to participants today, so the sign polls `get#Call` for the rooms in view until N6.
- **One call per page.** `moqui.call` holds a single call. Entering a meeting room leaves any ambient call, and
  leaving the room returns to it. A call taken from the header (a phone call, a direct call) does the same.

### 14.2 Distance audio

- **Every enclosure is its own sound space, completely.** The exterior has one audio-only call. Each enclosure
  (a front office, a back office, a secured area, a meeting room) has its own, joined only by those inside it and
  so only by those allowed in. No audio is forwarded between calls, so nothing passes a wall, a door or the glass
  in either direction and in either form below; there is no muffling or partial sound to compute. Passing a door
  leaves one call and joins the next, which is also what the one-call-per-page rule requires.
- **Calls exist only while used.** An enclosure's call starts when the first person there turns sound on and ends
  when the last leaves, so empty rooms cost nothing against the media server's limit of calls per node.
- **Inside an enclosure** distance shapes volume as it does outdoors, except in a meeting room, where everyone
  hears everyone as in any meeting.
- **A person's choice.** Listening is off by default and the microphone is a second choice. A figure shows whether
  its person is listening and speaking.
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

### 14.3 Venues

A venue is a world of kind `venue` ([§8.4](#84-worlds)), instantiated for one chat room. Its meeting is that
room's call, so everything in [§14.1](#141-meeting-rooms) holds: 2D and 3D participants are one meeting, and
those without a figure have a plaque. [The atlas](design-world-venues.md) describes the four that ship.

- **Binding.** A `WorldRoom` row with a `worldId` in place of a room place. A door in the town bound to such a
  room travels to the venue. So does "Join in 3D", an `ext-action` mr-three adds to the call window and to a
  calendar event's join action through the shell's `meetings-source`.
- **Admission is the call's.** Any signed-in user who may join the call may enter its venue; the world grant is
  not asked, because a venue shows no app and no town. Its sibling screen, services and presence check the
  named room's venue binding and call admission. A guest who joined by `CallLink` has no `/qapps` session and stays in the ordinary call
  page, shown in the venue as a plaque.
- **Sound** is a meeting room's: everyone hears everyone. A venue is one enclosure.
- **Screens.** A place of kind `screen` shows a video track of the call as a video texture: the first
  participant publishing `source: "screen"`, else the active speaker's camera, else a title card drawn to a
  canvas. All screen places of a venue show the same track.
  - The browser already receives the track as a call member. The screen's subscription layer follows its size
    in the view: `low` across a hall, `mid` when near, `none` when no screen is in view, using the existing
    per-publisher `subscribe` frame.
  - Selecting a screen opens the same track flat in the page window at the `high` layer. That is where it is
    read; the texture on the wall is a sign ([§11](#11-screens-in-the-world)).
  - Presenting is the call's own screen share. Nothing new is captured, and the presenter keeps working in
    their page window.
  - *Follow the presenter:* the presenter may publish their current route as a short message (N11). A member
    who takes it navigates their own window to that URL, under their own authorization.
- **Meeting furniture** (roster, recording, agenda, time left, speaking order, notes) reads `get#Call`, the
  call's recording state, and the calendar event or `CallLink` bound to the room. The speaking order is the
  one new piece of state: a list kept by the presence relay for the life of the call, never stored.
- **Limits.** `max_legs_per_room` is 50 and there is no listen-only role, so a hall or theatre holds 50 in all.
  An audience beyond that needs a receive-only role and a higher limit for it (N12).
- **The board, later.** A place of kind `board` binds to a document. mr-three builds no whiteboard; when one
  exists as a wiki document type, a board needs three things from it (N13):
  1. *A document type.* A wiki page has no type today beyond its file extension.
  2. *Co-editing under its own key.* `collab` keys are fixed to `WikiPage:<id>` in `crates/moqui-collab`.
  3. *A way to draw into a canvas it is given.* A canvas document can then be a `CanvasTexture` on the wall and
     update live, which a DOM screen cannot ([§11](#11-screens-in-the-world)). Editing is in the page window
     first; drawing on the wall itself is a later step.
- **The dome.** A town-wide presentation on the dome is replaced by the cliff theatre, whose audience chose to
  be there. In the town the dome is the sky and the far limit.

## 15. Assets

- **Sources.** CC0 low-poly packs with a medieval village, interiors, props and rigged figures. Kenney, KayKit and
  Quaternius publish such packs; the selected source files and their included licenses are recorded in
  [the atlas kit selection](design-world.md#kit-selection). Record archive hashes and converted kits when vendored. No pack is used without a license
  text that permits redistribution without conditions.
- **The pipeline** (`tools/kits.mjs`, Node, development only, in the pattern of the library refresh):
  1. Fetch each pinned pack archive and check its hash. Source archives are not committed.
  2. Take only the pieces listed in the theme pack's `kits/<kit>.json`.
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
- **Serving.** `/threestatic/mod/<modId>/kits/<kit>.glb?v=<hash>` is cached as immutable and kept by the service worker.
  `.glb` works today as `application/octet-stream`; proper media types and compression for `.gltf` and `.bin`
  are N3.
- **Budgets per kit** are part of the kit's index and checked by the pipeline: triangles per piece, total size,
  and one material.
- **Kits belong to theme packs.** The pipeline's piece lists are a pack's `kits/<kit>.json`, and its output goes
  to that pack's mod, under `static/kits/`. Layouts never name a kit ([§16](#16-theme-packs)).

## 16. Theme packs

A theme pack is how a world looks and sounds, as data. It holds no script. A pack lives in a mod, at
`mod/<modId>/theme/<themeId>/`, and follows the conventions of the Shop theme package
([Themes](../../../../../docs/ui/Themes.md)): a manifest, typed settings with a schema, locales, and assets
named by digest.

| File | Holds |
| --- | --- |
| `theme.json` | `{theme, title, schemaVersion, vocabulary, extends, kits, tiers}`: the pack's identifier, the piece vocabulary version it maps, the pack it extends, its kits, and what each quality tier drops |
| `palette.json` | Named color roles (stone, wash, wood, water, ink, roofs by district), the state colors and the two route colors. The palette texture is generated from this file, so a recolor is one small strip and no kit is reprocessed |
| `materials.json` | Per role: flat or textured, a pattern, roughness. The whole of "plain" is this file with everything flat |
| `pieces.json` | Vocabulary piece → kit and node, with a fallback piece |
| `marks.json` | The emblem glyph of each building, the seven action marks, the six state looks, as sanitized SVG paths drawn to canvas textures and reused by the HUD and the directory |
| `decor.json` | Per decoration kind and look: the unit piece, the container, default steps, the cap, the motion on change |
| `light.json` | Sun direction and color, ambient, fog, the dome's treatment (painted, gradient), per tier |
| `sound.json` | Ambience loops and a cue per event name. Every entry is optional |
| `figures.json` | Bodies, the clip map onto the shared skeleton, cosmetics |
| `chrome.css` | World-mode CSS variables: the window frame, header tint, label face, the HUD, and the minimap's colors. It reads the shell's own scheme variables, so the framed screen keeps the person's light or dark choice |
| `settings.schema.json`, `settings.json` | What an installation may adjust, typed as Shop settings are (`color`, `image`, `text`, `enum`, `boolean`, `number`), and the defaults |
| `locales/<locale>.json` | Texts the pack owns |
| `kits/<kit>.json` | The pipeline's piece list for each kit ([§15](#15-assets)); the `.glb` files are in the mod's `static/kits/` |

- **The vocabulary** is the list of piece identifiers and decoration looks that layouts may name. `mod/core`
  owns the common ones (walls, doors, desks, books, the kinds' default looks); a world's mod adds its own
  (`sling`, `ruled-book`). A pack *fits* a world when it maps everything that world's layouts name.
- **`extends`.** A pack may extend one other and replace whole files or single entries, to a depth of four.
- **Two packs ship**, both in `mod/millrace`:
  - `millrace`, the art direction of [the atlas](design-world.md#2-art-direction).
  - `millrace-plain`, which extends it and replaces `materials.json` (all flat), `light.json` (a gradient dome,
    no painted cloud), `sound.json` (empty) and part of `chrome.css` (no ornament). It has no texture but the
    generated palette strip and the text drawn for labels. It is the default on the Low tier.
- **Plain is the conformance test.** Validation requires that every state, mark and look differs from every
  other by geometry or text, never by texture or color alone, and that the state and route colors stay distinct
  in grayscale. A pack that passes in plain reads for anyone.
- **Settings an installation changes** (accent color, a banner or logo image, the town's name, the clock face)
  are values against the pack's schema, kept at `dbresource://three/theme/<themeId>/settings.json` with
  revisions. An uploaded image is an asset by digest, as in the Shop package. The art itself is not edited in
  the installation.
- **Choosing.** A world lists the packs that fit it and names its default. An installation may set another
  default. A person chooses with `THREE_THEME` among those that fit; the Low tier starts in the plain pack.
- **Validation.** `ironstone.three.ThemeServices.check#Themes` (*proposed*): the manifest and every file against
  their schemas; every vocabulary entry mapped or given a fallback; each kit within its budgets; assets present
  at their digests; SVG marks cleaned with `ec.resource.theme.sanitizeSvg`; the plain rules above. One bad
  pack is reported by name and the others load.
- **Served** as any art is ([§9.4](#94-what-is-secret-and-what-is-not)): to signed-in users, with no state in
  it.
- **Later.** Uploading a whole pack into an installation, with draft and pinned release as Mantle keeps Shop
  themes. Nothing in the format prevents it, since a pack is only data.

## 17. Mods

A mod adds to the world: a building, a venue, a character, a kind of decoration, a walk, a look, an act. It is
a directory in a component, read by mr-three. mr-three is built from mods of its own, so the interface an
add-on uses is the one the town runs on.

### 17.1 The directory

```text
mod/<modId>/
  mod.json            the manifest
  world/<worldId>/    world.json and one layout per space        (§8)
  patch/<space>.json  additions to another mod's space, in its slots
  room/<app>.json     station groupings for a generated room     (§17.3)
  theme/<themeId>/    a theme pack                               (§16)
  <specialist>/       AGENT.md and station.json                  (§11.1)
  walk/<walkId>.json  a guided walk: stops, sentences, places
  static/             all that a browser may fetch: js/, kits/, audio/, img/
```

- **Found by scanning.** At start, and when a component is deployed, `ironstone.three.ModServices.load#Mods`
  (*proposed*) walks `ec.factory.getComponentBaseLocations()` and reads each component's `mod/*/mod.json`
  through `ec.resource.getLocationReference`. Components load in dependency order, so a mod sees the mods it
  requires. A manifest that does not parse, requires something absent, or names an unknown API version is
  logged by name and left out; the rest load.
- **Inert without mr-three.** A `mod/` directory is files nobody else reads. A component may ship one without
  depending on mr-three, as long as its services do not call mr-three's. One that does declares the
  dependency in `component.xml`.
- **Only `static/` reaches a browser.** A transition on `threestatic` sends
  `/threestatic/mod/<modId>/<path>?v=<hash>` from `component://<component>/mod/<modId>/static/<path>` with
  `ec.web.sendResourceResponse`, for a registered and enabled mod, a path with no `..`, and an extension on a
  short list. Manifests, layouts and packs are read by services and returned as data. The mod loader reads the station files on the server.
  Assist reads each role brief through its definition. Immutable caching for these URLs is *unverified*; a component may instead mount its own static
  directory as `devstatic` is mounted.

The manifest:

```json
{
  "mod": "acme-fleet",
  "title": "Fleet yard",
  "version": "1.0.0",
  "api": 1,
  "requires": { "mods": ["core", "millrace"], "components": ["acme-fleet"] },
  "optional": false,
  "sources": {
    "fleet.home": { "service": "acme.fleet.HomeServices.get#Home", "screen": "Fleet", "zone": "back",
                    "scope": ["organization"], "values": { "dueCount": "count", "downCount": "count" } }
  },
  "actions": {
    "fleet.claim": { "service": "acme.fleet.WorkServices.assign#ToMe", "places": ["station"], "confirm": false }
  },
  "lots": [ { "world": "millrace", "district": "mill-yard", "house": "staff", "app": "Fleet", "space": "fleet",
              "doorstep": "acme.fleet.WorldServices.get#Doorstep" } ],
  "client": ["js/fleet.mjs"]
}
```

### 17.2 Scripts

**On the server a mod is its component.** It has services, entities, SECA and EECA rules, scheduled jobs and
the whole of `ec`, in the component's ordinary directories. The manifest only names what the world may reach:

- `sources`: readings for decorations, added to the registry of [§10](#10-functional-decorations). mr-three
  calls the service as the viewer, guarded by the named screen's authorization and delivered by zone.
- `doorstep`, on a lot: the component's own world-state service for what waits at that building's door
  ([§10.1](#101-world-state-services)). A building without one has an empty step.
- `actions`: services an act in the world may call ([§11](#11-screens-in-the-world)). The browser sends the
  action's name and the place; the server finds the service.
- A mod that needs to remember a little without an entity of its own uses `WorldModState`
  ([§8.3](#83-installation-state)) through `ironstone.three.ModServices.get#State` and `store#State`.

**In the browser a mod is an ES module** listed in `client`, loaded when a world that uses the mod is entered.
It exports one function and receives the API:

```js
export default function (api) {
  api.decor.define("fleet.keys", {
    kind: "pegs",
    create(node, look) { /* build three.js objects under node */ },
    update(reading) { /* {state, values, asOf, scope} */ }
  })
  api.on("place.select", "fleet-board", place => api.act("fleet.claim", { place: place.id }))
}
```

| Part of the API | Gives a mod |
| --- | --- |
| `api.THREE`, `api.node(space)` | three.js itself, and a scene node of its own in each space. A mod builds what it likes under its node |
| `api.decor.define(look, def)` | A renderer for a decoration kind or a new look of one. Its declaration (values, states, directory sentence) is in the manifest, so the directory can read it |
| `api.on(event, [place], fn)` | `world.enter`, `space.enter`, `space.leave`, `place.near`, `place.focus`, `place.select`, `reading` (a source changed), `route`, `presence.join`, `presence.leave`, `call.state`, `tick` (budgeted, and skipped when the world is idle) |
| `api.source(name)` | The viewer's latest reading of a registered source, with its state |
| `api.act(name, params)` | Runs a registered action on the server as the viewer |
| `api.open(path, params)`, `api.travel(place)` | The page window at a screen under the apps root; fast travel |
| `api.label`, `api.panel`, `api.toast` | A focus label, a small anchored panel holding a panel-archetype screen, a brief notice |
| `api.hud.slot(name, component)`, `api.hud.marker(layer, items)` | A component in a named HUD slot; a marker layer on the minimap ([§7.5](#75-the-hud-and-the-minimap)) |
| `api.ask(agent, context)` | Opens Assist at one of the mod's specialists |
| `api.prefs`, `api.state` | The person's preferences in the mod's own namespace; `WorldModState` |
| `api.net.send(type, data)`, `api.net.on(type, fn)` | Short messages to the others in the space, by zone (N11) |
| `api.figure` | Emotes, poses and cosmetics registered for figures |
| `api.audio.cue(name)`, `api.physics` | A sound cue from the pack; the physics world, in spaces that load it |

- **The glass holds.** A mod gets readings only through registered sources, which carry a zone, and its
  messages are relayed by zone. So a mod cannot leak a back office by accident. Its own service can return
  anything to its own module, as any component's can; that is the component's responsibility
  ([§17.4](#174-trust-enabling-and-versions)).
- **Every kind has a text twin.** A decoration kind without a directory sentence fails validation, a walk is a
  list in the directory, and a HUD slot's content must also be reachable from the tray or the directory.
- **One mod's failure is its own.** A client module that throws while loading or in a handler is logged with
  the mod's name and turned off for that page; the world goes on without it. A source or action that fails
  affects its own decoration or act.
- **Budgets.** A mod's kits and modules count against the budgets of the spaces they appear in
  ([§5](#5-rendering-core)); validation adds them up.

### 17.3 Lots, slots and generated rooms

- **Lots.** The town's exterior declares spare lots, each with a district, the house types it takes and a door
  anchor. A mod's `lots` entry asks for a district and a house type for one app. `load#Mods` assigns the first
  free lot that fits, in mod order, and an administrator may pin an assignment in the mod's settings. A mod
  with no free lot is listed in the directory's Other apps and logged. A building is drawn from the house type's
  standard shell in the active pack, with the mod's emblem glyph and the app's title.
- **Conditional buildings.** A mod that `requires` a component is left out when the component is. The Drawing
  office, the Model shed and Marble's coach house are such mods in mr-three, replacing the "removable lot" and
  "painted coach" special cases. The first two name their own numbered places on the plan, which are lots
  reserved for them; the coach house takes the first market lot.
- **Slots.** A space declares the areas where others may add ([§8.1](#81-layout-documents)). A `patch` adds a
  station or a decoration to a named slot. Two patches wanting one slot are placed in mod order, and the one
  that does not fit is logged and left out.
- **Generated rooms.** `ironstone.three.LayoutServices.generate#Room` (*proposed*) makes an interior for an app
  that has none. It reads the app's menu as `menuData` would give it to an administrator, the optional
  `room/<app>.json` (stations, the sections each holds, the readings bound to each, in work order), and the
  house type. It places the stations clockwise on the shared floor, the Home desk at the centre, the index
  cabinet and Settings by the exit, and a front strip when the app has a PASS counter. With no grouping file
  it makes one station per top-level section. The result is an ordinary layout document, cached and validated
  like a written one. The [building sheets](design-world-buildings.md) are the grouping files for the twenty
  rooms nobody has furnished by hand.

### 17.4 Trust, enabling and versions

- **A mod is component code.** It is installed by whoever installs components, runs with what any component
  runs with, and is not sandboxed on the server or in the browser: `/qapps` restricts no script today, and a
  component's client code is already fully trusted. A user never uploads a mod. This is the same trust as
  `screen-extend` or a theme's script row, stated plainly.
- **A theme pack is not code**, which is why an installation may adjust one and may later upload one.
- **Enabling.** Village settings, for an administrator, list each found mod with its component, version, state
  and any load error, and enable or disable it (`WorldModSetting`). mr-three's own mods are on by default;
  `core` cannot be turned off. A mod that declares `"optional": "user"` (a pet, an alternate ambience) also
  appears in each person's village settings (`THREE_MODS`).
- **Versions.** `api` is the interface version a mod was written for. The interface is version 0 and internal
  while mr-three's own mods are its only users; it is frozen as version 1, documented under `docs/`, and
  offered to other components once T3 has shipped on it ([§21](#21-phases)). A mod naming a version the engine
  does not offer is left out with a logged reason.
- **Tests.** A mod's layouts, patches, packs and manifest are checked by the same validation specs as
  mr-three's, in the mod's own component.

### 17.5 The built-in mods

| Mod | Holds |
| --- | --- |
| `core` | The piece vocabulary, the seven marks and six states, the decoration kinds and their default renderers, the common fittings (approvals bell, room sign, blank slip, counter flag, notice board, map table), the meeting furniture, the HUD's standard slots |
| `millrace` | The town: its exterior, the three furnished interiors, the room groupings for the rest, the packs `millrace` and `millrace-plain`, the sources and acts for the BOSS and framework apps |
| `cast` | The seven specialists |
| `venues` | The four meeting worlds and their packs |
| `walks` | The four guided walks |
| `lot-dev-aide`, `lot-gallery`, `lot-marble` | One building each, present with its component |

## 18. Clients

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
- **External users.** A PASS public account with the world grant signs in at the village on a host that serves
  `/qapps`, the static mount, the presence socket and call signaling. PASS hosts refuse framework routes, so an
  installation that exposes only PASS hosts must also expose the village's, and a village at an edge node needs
  presence and media there ([§23](#23-open)). What the `/qapps` shell shows a user whose only app is the village
  (an empty tray, no palette sources) is *unverified* and is checked before any external grant is given.

## 19. Testing

Under the two suites of [Build-Test](../../../../../docs/Build-Test.md):

- **Regression specs** (server, fast): layout validation over every document; the world grant (a user without it,
  internal or external, cannot enter the town, call its services or join its presence rooms); the door manifest for users with and without
  access, and with `hide`; the glass (a front-office user's space state has no back-zone values, and their presence
  snapshot has only veiled records for the back); space state for each state of the table, including a source that
  fails and one that is withheld; sign binding; room binding and who may enter. The pure parts of the engine (walk
  grid, path finding, level mapping, snapshot interpolation, the minimap's shapes from a layout) are modules with
  no WebGL in them, tested as client specs without a renderer.
- **Mods, packs and worlds** (server, fast): the scan finds a mod in another component and leaves out one with a
  bad manifest or a missing requirement, by name; only `static/` is served and a specialist's role brief, station file or mod manifest is
  refused; a source or action not in an enabled mod's manifest cannot be called; a patch outside a slot is
  refused; lot assignment is stable; a generated room validates for an app with and without a grouping file;
  both packs validate, and the plain rules catch a look that differs by color alone; a venue admits a call
  member without the world grant and refuses a non-member with it; closing a page in the meeting returns a
  venue-only member to that same venue and leaves the town inaccessible; the minimap's markers for a front-office
  viewer hold nothing of the back zone.
- **World-state services** (server, fast): for each, on the demo data, its count equals the row count of the
  list it names for the same user and scope; a user whose door is shuttered gets no entry for that building; a
  user of another organization gets that organization's count and not this one's; one service that throws
  leaves the others' answers in place and its own `failed`; the street call makes no call into Insights.
- **Rendering.** The village screen is rendered once in `qvt` and once in `tui`, since it has its own templates,
  with all checks on that render. Each injected viewer is checked on its host screen's existing render, not by a
  new one. No sweep.
- **Acceptance** (only when asked or when changed): one browser walk that enters the village, opens a door, sees
  the screen in its window and returns; later one with two browsers for presence and one for a meeting room.
  Whether headless Chromium gives a usable WebGL context in the test harness is *unverified*; the walk asserts on
  the directory and the DOM where it can.
- **The libraries manifest check** is a regression spec in mr-three, as `browser_libs.rs` is for `webroot/libs`.
- A regression budget for mr-three is set when the first spec lands.

## 20. Framework and data needs

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
| N10 | PASS pages framed by the village | response policy (in mr-pass) | `frame-ancestors` of the village's origin in `mr-pass/screen/Shared/Headers.js`, for a signed-in user with the world grant | Front offices, from T3 | a PASS page loads in the window for such a user and refuses every other framer |
| N11 | Short messages on the presence socket | part of N1's family (in mr-three's relay) | A typed message frame beside positions, stamped with the sender, limited in size and rate, delivered by zone | Point, follow, knock, the counter flag, speaking order, `api.net` | a back-zone message reaches no front-zone socket; an oversize or too-frequent one is dropped without closing |
| N12 | An audience in a call | protocol server | A receive-only participant role beside Host, Member, Guest and Forward, with its own higher leg limit | A hall or theatre above 50 | an audience leg receives and cannot publish; the member limit is unchanged |
| N13 | A canvas document to bind a board to | wiki and `collab` (not mr-three's to build) | A document type on wiki pages; a `collab` key for it beside `WikiPage:<id>`; an entry that draws the document into a canvas it is given | Board walls | a board place shows the document and changes as it is edited |

By the direction of November 17, 2026 the framework gains what THREE needs where the piece is small, and reusable
where it can be: N1 (any add-on's live messages), N2 and N3 are general; N4 to N6 and N12 are small changes to
calls that help Comms too. N8 and N9 are Mantle's, N10 is PASS's and N13 belongs to the wiki's whiteboard work.
The libraries, art, engine, presence relay and world-state services stay in mr-three. Mods and theme packs need nothing
from the framework: scanning components, reading their files and sending a file by location all exist
([§2](#2-existing-foundations)).

N1 is the only one that blocks a phase outright (T4). The others have an interim: theme rows for N2, octet-stream
for N3, polling for N6, generated racks for N8, a limit of 50 for N12, and a notes page on the board wall for
N13.

## 21. Phases

| Phase | Deliverable | Done when |
| --- | --- | --- |
| T0 Foundation | `component.xml`, the static mount, vendored libraries with manifest and check, `moqui.three` with the shared renderer, `m-three-view` with the `box` preset, a gallery example | A box renders on the example screen; nothing 3D loads on other pages; the manifest spec passes |
| T1 Village, alone | The mod scan with mr-three's own mods, the `millrace` world and both theme packs loaded through it, the kit pipeline with a first exterior kit, layout documents and validation, world mode over the shell, camera and movement, buildings as doors with the manifest and the three door looks, the first HUD (place line, generated minimap and full map with travel, action prompt, travel buttons), the directory and `tui` twin, quality tiers | A person walks or clicks to a building and its app opens in the window; a door that is not theirs stays shuttered; the town draws in both packs; the minimap matches the layout; the budgets of [§5](#5-rendering-core) are measured and recorded |
| T2 First viewers | Box type, product size, packing preview, generated warehouse racks, each injected with its text twin | Each shows on its host screen in BOSS and reads as text in `tui` |
| T3 Interiors and decorations | The interior template with zones, stations and the glass; the receiving barn, counting house and proof yard from the world atlas and a generated room for every other building; the source registry and space state; the decoration kinds; the world-state services and the street readings they supply; signs bound to dashboards; acts and blank slips; front-office places opening PASS pages in the window (N10); the HUD's scope pennant, work tracker, waypoint and satchel, and the room plan on the minimap; guided walks. All of it through mod API version 0 | State changes in the apps show in the world within a minute; every state of [§10](#10-functional-decorations) has a look in both packs; every building has a room; a user with only the world grant gets no back-zone state; an internal user with the grant can open a configured PASS counter in the window |
| T4 People | N1 and N11, the presence relay with its two audiences, figures and their settings, people on the minimap, point, follow, knock and the counter flag | Two browsers see each other move; from the front office a back-office figure is a veiled shape and is absent from the minimap, and the back zone admits only the authorized |
| T5 Sound and venues | Meeting rooms on existing calls (N4, N6), then distance audio in its first form, a call per enclosure; the HUD's sound panel; the four venues with their meeting furniture and big screens on the call's screen share | A 2D participant and a walk-in hear each other; outdoors, volume follows distance; nothing is heard through the glass; a shared screen shows on a venue's wall and opens flat; a call member without the world grant enters a venue and not the town |
| T6 External visitors | The identity rules for external users, verification of T3's PASS framing with external accounts, the check of what the shell shows them, the deployment notes for hosts | A PASS public account with the grant walks the exterior and a front office, opens its own PASS pages in the window, and learns nothing of another visitor or of a back office |
| T7 Mods opened | The mod interface frozen as version 1 and documented; the administrator's list of mods and theme settings; optional per-person mods; an example mod in the development component set that adds a building on a lot, a source, a decoration look, a walk and a specialist station | Enabling the mod installs the building and live reading along with the specialist's station, body, server-side brief and ordinary screen link. The screen opens from the room and directory. Disabling the mod withdraws its contributions, and the other mods still work |
| Later · Assist binding | Bind the curated role files to Assist definitions and offer private **Ask** at each authorized station and in the directory ([§11.1](#111-specialist-stations-and-assist)) | A viewer receives a first private answer from the chosen specialist through either entry. A new run picks up an organization rule edit and a paused run keeps its snapshot ([binding checks](design-world-agents.md#assist-binding)) |
| Later | Distance audio in the server (N5); an audience role for large venues (N12); board walls on a canvas document (N13); props with physics; editing in the viewers (N8); the product model viewer and PASS; 3D for Maps; the assembly canvas; a world editor that writes an installation mod; uploaded theme packs; scoring | Each is its own design |

The world grant exists from T1 and is given only to internal users until T6. T2 depends only on T0 and can run
beside T1. The specialists' bodies are placed with their rooms from the `cast` mod; their conversations wait for
the Assist binding. [design-world](design-world.md) must have the exterior plan before
T1 ends and the first interiors before T3.

## 22. Decided

| Decision | Notes |
| --- | --- |
| The suite is IronStone THREE (Three-d Hosted-Roleplay Enterprise Ensuite) in the `mr-three` component | Services and entities in `ironstone.three`, preferences and groups `THREE_*`, the static mount `threestatic`; "the village" is the world mode's everyday name |
| Entering the town needs the world grant, for internal and external users, given in System | It opens the exterior and front offices and no app ([§9.1](#91-the-world-grant)) |
| A building is a PASS front office open to the world and a BOSS back office behind a lock and privacy glass | The glass is enforced by what the server sends ([§9.3](#93-front-office-back-office-and-the-glass)) |
| The framework gains small, reusable pieces as needed; libraries, art and anything bulky stay in mr-three | [§20](#20-framework-and-data-needs) |
| An external user is "Visitor" or their registered real name, by their own choice; nothing else | Decided November 17, 2026 ([§13](#13-presence)) |
| Enclosed spaces are separate sound spaces, and doors are portals that never open | No sound or sight through a doorway; one call per enclosure ([§12](#12-movement-camera-and-physics), [§14.2](#142-distance-audio)) |
| The village is a shell layer with the page as a window | Not a screen and not a render mode; real URLs and the whole shell keep working ([§7](#7-the-village-as-a-shell-layer)) |
| A working screen is always flat and framed | No DOM in 3D perspective ([§11](#11-screens-in-the-world)) |
| WebGL2 with three.js; one context per page | WebGPU revisited after T1 |
| Libraries are vendored in mr-three with their own manifest | An installation without mr-three ships none of them |
| Layouts are code in a component's mod; installation state is a few small entities and preferences | A world editor is later and writes an installation mod ([§8.3](#83-installation-state)) |
| Engine, world, theme pack and mod are separate; mr-three's own town, looks, cast and venues are mods | Layouts name vocabulary pieces and decoration kinds, never files ([§8](#8-world-data), [§16](#16-theme-packs), [§17](#17-mods)) |
| One scanned directory, `mod/<modId>/`, in any component | The first add-on-owned convention; only its `static/` is served ([§17.1](#171-the-directory)) |
| A theme pack is data only; two ship, `millrace` and the texture-free `millrace-plain` | Plain is the Low-tier default and the conformance test ([§16](#16-theme-packs)) |
| Mod client code gets three.js unsandboxed | It is component code like any other; a user never uploads one. Decided November 17, 2026 ([§17.4](#174-trust-enabling-and-versions)) |
| Worlds are a town and venues; a venue is instantiated per chat room | A venue admits any signed-in user who may join its call, without the world grant. Decided November 17, 2026 ([§8.4](#84-worlds), [§14.3](#143-venues)) |
| Venues are modeled on natural and ancient landmarks only | Anything modern is a generic look-alike without its name. Decided November 17, 2026 |
| Big screens show the call's own screen share as a video texture, and open flat | No DOM on a surface; the board wall waits for a canvas document ([§14.3](#143-venues)) |
| A HUD as DOM under the shell header | Its work tracker is three lines that hand off to the tray's Today, so the shell is not forked. Decided November 17, 2026 ([§7.5](#75-the-hud-and-the-minimap)) |
| The minimap is generated from the layout | The hand-drawn SVG stays an illustration. Decided November 17, 2026 |
| Rooms are stations in work order with one index cabinet, generated where not hand-made | Sized from their stations, larger than the house on the plan ([§17.3](#173-lots-slots-and-generated-rooms)) |
| Street readings are per viewer | Each browser draws its own town; one street call and the shell's counts supply them ([§10.1](#101-world-state-services)) |
| A street door is open or shuttered; a lock is drawn only inside | [§9.2](#92-doors) |
| The built-in cast has seven specialists at local stations | The [cast register](design-world-agents.md#the-cast) names each place, body and brief |
| Door states come from the screen's own authorization through one manifest call | Installations may hide secured areas instead of locking them |
| A room's decorations read its app's own Home service; the street and anything across apps read mr-three's own world-state services | Both through the registry, as the viewing user. The world-state services are counts over the database, each mirroring a named list in its app and checked against it, with no dependency on Insights. Direction of November 17, 2026, replacing "no new business queries in mr-three" ([§10.1](#101-world-state-services)) |
| Walking uses the walk grid; physics is for props and loads only where they are | Keeps the first download small |
| Meeting rooms are chat rooms and their calls | One meeting for 2D and 3D participants |
| Distance audio starts in the browser and is described honestly as audible to the whole space | The server form follows with N5 |
| Art is CC0 packs through a recoloring pipeline; decorations are code-built | From the user's choice of November 17, 2026 |
| Maps stays in the framework | mr-three adds 3D layers after Maps P1 |

## 23. Open

| Question | Recommendation |
| --- | --- |
| How mr-three's own engine code is delivered | Plain ES modules under `threestatic/js/`, loaded by dynamic import with relative imports, revalidated by ETag in development; a release bundle made by the same tool that vendors the libraries, checked against its sources by a spec. No build step while developing |
| N1 as a general hook or a fixed family | A general hook for native components: it is the reusable kind, Maps tracking and other add-ons will want the same, and it keeps mr-three out of `moqui-host` |
| Who owns location positions and sizes (N8) | Mantle, as optional data: picking routes and capacity checks can use them too, and Mantle already owns the location |
| Presence between cluster nodes on the `notify` transport | Two snapshots a second across nodes, ten within a node; revisit if a village spans nodes in practice |
| How external users reach the village | On the installation's main host at first, signing in with their PASS account; a village served at an edge node or under a PASS host is a later design |
| A PASS page in the window, or in a new tab | In the window, framed, from T3 with N10; a new tab wherever a site's policy forbids framing |
| Caching of a mod's static files sent by location | Try `sendResourceResponse` with a `?v=` URL in T1; if it cannot be made immutable, mods mount their own static directory |
| The exterior kit | Test one hexagon-pack building at the close zoom beside a figure before T1 commits; a modular wall, roof and door kit otherwise ([the atlas](design-world.md#kit-selection)) |
| Whether a call guest without an account can enter a venue | Not at first: guests stay in the ordinary call page and show as plaques. Revisit with T6's external visitors |
| How lots are assigned when more mods ask than there are lots | In mod order, the rest in Other apps; a second district of lots is a layout change, not an engine one |
| Where the satchel's record types come from | The shell's `record-link` providers; a station declares the types it takes. Confirm the providers cover the BOSS record kinds before T3 |
| Which PASS site the front offices use | One site named by an installation setting ([world thresholds](design-world.md#3-buildings-and-thresholds)) |
| The product viewer on PASS pages | After the BOSS viewer: served by PASS from the site's own assets, core bundle only, with PASS's policy unchanged if possible |
| Whether world mode restyles the header or replaces it | Restyle only: the header is where the palette, tray, Assist and account live, and replacing it would fork the shell |
| Order of T1 and T2 | T1 first for the fun of it, T2 beside it when someone wants a break from the village |
