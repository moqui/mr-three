# Millrace building sheets

Use the [site plan](design-world.md#1-town-plan) numbers for wayfinding. Each house below has a face, an arrival object, a front meeting place and a set of stations. Section names in code are exact path segments beneath the linked root. In the room use the screen’s localized menu title. Each section has a labeled book opening it. For a detail screen that requires a record, the book first opens its authorized selector. A saved Dashboard comes from Home and a Playlist from Playlists, each with its definitionId. Its source is that section’s menu entry as resolved under the viewer’s grants and menu conditions. A changing object has its source in the sheet. The seating recess, street meeting entrance and return portal use the [shared floor plan](design-world.md#3-buildings-and-thresholds).

## Street directory

| Plan | Street sign | Emblem | Staff app under /qapps | PASS counter on the configured site | Meeting place |
| ---: | --- | --- | --- | --- | --- |
| 01 | Gatehouse | Painted lintel and fingerpost | — | Account, Members, Sites | Travellers’ table |
| 02 | Forum steps | Stone semicircle | — | Forum | Speakers’ room |
| 03 | Clock house | Clock face and day tiles | [Commons](../../mr-boss/screen/Commons.xml) | Staff entrance | Bell loft |
| 04 | Post loft | Dovecote | [Comms](../../mr-boss/screen/Comms.xml) | Staff entrance | Pigeon loft |
| 05 | Market hall | Awning on timber posts | [Commerce](../../mr-boss/screen/Commerce.xml) | Shop | Market chamber |
| 06 | Counting house | Brass coin and black shutters | [Accounting](../../mr-boss/screen/Accounting.xml) | Billing | Reckoning room |
| 07 | Bargain hall | Two facing gables | [Sales](../../mr-boss/screen/Sales.xml) | Sales | Handshake room |
| 08 | Print yard | Drying broadsides | [Marketing](../../mr-boss/screen/Marketing.xml) | Marketing | Paper room |
| 09 | Mending porch | Darned awning | [CustomerService](../../mr-boss/screen/CustomerService.xml) | Support | Quiet parlour |
| 10 | Pattern store | Sawtooth ridge and blank tag | [Products](../../mr-boss/screen/Products.xml) | Staff entrance | Sample room |
| 11 | Factors’ scales | Scale beam | [Purchasing](../../mr-boss/screen/Purchasing.xml) | Supplier | Weigh room |
| 12 | Receiving barn | Hoist jib | [Warehouse](../../mr-boss/screen/Warehouse.xml) | Staff entrance | Sling room |
| 13 | Wheel mill | Millwheel | [Manufacturing](../../mr-boss/screen/Manufacturing.xml) | Staff entrance | Mill table |
| 14 | Repair shed | Ladder and mallet | [Maintenance](../../mr-boss/screen/Maintenance.xml) | Staff entrance | Joiner’s bench |
| 15 | Guild kitchen | Low chimney and long table | [People](../../mr-boss/screen/People.xml) | Staff entrance | Fireside room |
| 16 | Proof yard | Double chimney and anvil | [Projects](../../mr-boss/screen/Projects.xml) | Smithy | Proof room |
| 17 | Survey loft | Sighting frame | [Insights](../../mr-boss/screen/Insights.xml) | Staff entrance | Map room |
| 18 | Seal house | Key finial | [Admin](../../mr-boss/screen/Admin.xml) | Staff entrance | Charter room |
| 19 | Winding house | Copper governor | [system](../../../base/tools/screen/System.xml) | Staff entrance | Watch room |
| 20 | Instrument shop | Set square | [tools](../../../base/tools/screen/Tools.xml) | Staff entrance | Instrument room |
| 21 | Picture gallery | Three gilt frames | [dashboards](../../../base/tools/screen/Dashboards.xml) | Staff entrance | Hanging room |
| 22 | Drawing office | Plumb line | [dev](../../../dev/dev-aide/screen/DevAide.xml) | Staff entrance | Tracing room |
| 23 | Model shed | Dollhouse facade | [gallery](../../../dev/example/screen/Gallery.xml) | Staff entrance | Model room |

Houses 19 to 23 stand behind the works wall, whose arches open together. A meeting place is drawn only where an administrator has bound a room to it. The names above are the ones to use when that happens.

### What waits at each door

Each building has a doorstep basket, filled for the viewer alone by a small service THREE writes for that building ([world-state services](design-technical.md#101-world-state-services)). The service counts the rows of one named list in the app, for this person in their current scope, and selecting the basket opens that list. A person with no open door at a building has no basket there.

| Building | What the basket counts | The list it opens |
| --- | --- | --- |
| Clock house | The viewer's tasks and events overdue or due today | Commons › Tasks, due |
| Post loft | Unread email and chat, missed calls, unheard voicemail | Comms › Inbox |
| Market hall | Orders waiting for this person's approval in their stores | Commerce › Orders, to approve |
| Counting house | Bills and journals waiting for this person's approval | Accounting › Approvals |
| Bargain hall | Opportunities, quotes and orders assigned to this person and open | Sales › Pipeline, mine |
| Print yard | Sends and content waiting for this person's approval | Marketing › Sends, to approve |
| Mending porch | Open cases assigned to this person | Customer Service › Cases, mine |
| Pattern store | Products this person is finishing that still have required fields missing | Products › Products, incomplete and mine |
| Factors’ scales | Requisitions and orders waiting for this person as buyer or approver | Purchasing › Approvals and Requests, mine |
| Receiving barn | Warehouse work assigned to this person at the selected facility | Warehouse › the queue of the assigned work |
| Wheel mill | Work orders and operations assigned to this person or their work center | Manufacturing › Shop floor, mine |
| Repair shed | Maintenance work orders assigned to this person | Maintenance › Work orders, mine |
| Guild kitchen | Decisions waiting for this person, and their own requests and unsubmitted weeks | People › Approvals, or My team |
| Proof yard | Open work items assigned to this person, and reviews waiting on them | Projects › Work items, mine |
| Survey loft | Alerts firing on series this person follows | Insights › Alerts, firing |
| Seal house | Readiness checks not ready, for an administrator | Admin › Operations |
| Works houses | Nothing at first. Their steps stay empty | — |

Each row is a definition to implement and check against the list it names. Where an app has no list filtered that way yet, the basket waits for it, because a count that cannot be opened is not shown.

### Emblem register

The emblem column is a register. An emblem belongs to one building. No other building carries it on the street, and no character in a neighboring house is shaped like it. Wheels, scales and presses are the ones to watch.

| Shape | Belongs to | Kept away from |
| --- | --- | --- |
| Wheel | Wheel mill alone | The Repair shed takes a ladder and mallet. The barn's specialist is a tally stick. The Winding house shows a governor's spinning balls and no wheel rim. |
| Scale beam | Factors’ scales alone | The barn weighs nothing on the street. Inside the counting house a balance is a decoration kind, never a sign. |
| Press | Inside the Print yard only | The Smithy's specialist is a proof stamp on an anvil. |
| Bird | The Post loft's dovecote, and the brass swallow at the gate | The counting-house pigeon is scenery and never carries a message. Messenger pigeons fly from the dovecote. |
| Lantern | A meeting door | Never a door's state, a waiting count or a guide. |

## Staff rooms

At each threshold the visitor sees the arrival object on the clear centre floor. Stations stand clockwise from the left of the entrance in the order the work passes through them. Each table below lists a station, the menu sections whose books it holds, and its live readings with the [kind](design-world.md#decoration-kinds) of each in italics. A reading is drawn only for a viewer the owning service answers. The index cabinet by the exit holds every section in menu order, and a labeled cupboard beside it holds Settings where present. Grant-sensitive stations take shutters or a locked portal according to the real section policy. The menu includes subsections registered by other components only when installed. Home has the centre desk except where the sheet names a different default. A blank slip lies on a station for each quick-create entry the app offers there.

**A generated room.** A building with no hand-made interior gets its room from this sheet: the stations and their sections as listed, placed on the shared floor, with the readings bound by name. An app with no sheet at all gets one station per top-level menu section and its Home on the centre desk. Hand placement is an improvement on a generated room, never a precondition for one.

### 03 · Clock house / Commons

A strip of blue day tiles wraps the tower. Inside, broad shallow drawers fit under a calendar drum.

Draw out today’s leaf. The desk opens [Calendar](../../mr-boss/screen/Commons/Calendar.xml) and shows only events the viewer may read, from Home’s mantle.work.EventServices.get#FullCalendarData with the same range and filters. The exterior clock carries the date alone. Counts come from ironstone.commons.CommonsServices.get#TodayView. [Home source](../../mr-boss/screen/Commons/Home.xml)

| Station | Sections | Readings |
| --- | --- | --- |
| Day desk | `Calendar` | Today's leaf, one line per event of `eventsToday` · *slips* |
| Task pegs | `Tasks`, `Requests` | `dueToday` on the pegs, `overdue` on a separate hourglass tag · *pegs*, *time left* |
| Bell desk | `Notifications` | The approvals bell and a wax seal for `approvalCount` · *bell* |
| Reading shelf | `Knowledge`, `Notes`, `Files` | — |
| Address cabinet | `Contacts` | — |

### 04 · Post loft / Comms

A dovecote roof stands over sorting holes. The first thing within reach is the incoming letter tray.

The slotted tray opens [Inbox](../../../base/basic-comms/screen/BasicComms/Inbox.xml), the default entry. For the personal overview use the Home book, read by org.moqui.comms.InboxServices.run#Operation (get#CommsHome) and get#HomeSources. BOSS extends the BasicComms Home. [Home source](../../../base/basic-comms/screen/BasicComms/Home.xml)

| Station | Sections | Readings |
| --- | --- | --- |
| Letter tray | `Inbox` | Letters for the shell's `email` count · *tray* |
| Pigeonholes | `Chat` | One hole per pinned room with its unread and mention marks, from org.moqui.chat.ChatServices.get#UnreadCounts · *tray* |
| Bell-pull and speaking tube | `Calls` | Missed calls (count#MissedCalls) on the pull, a sealed tube for unheard voicemail (count#UnheardVoicemails) · *tray* |
| Warden's shelf | `Consent`, `Moderation` | — |

### 05 · Market hall / Commerce

Limewashed upper chamber on open timber posts. The public stall has an awning. The Orders desk faces the staff threshold.

A tied purse shows payment holds in the viewer’s stores. mantle.order.StoreOperationsServices.get#StoreWorkSummary counts.paymentHolds opens [Orders/Orders](../../mr-boss/screen/Commerce/Orders/Orders.xml) with attention=paymentHold and store scope when can.orders. [Home source](../../mr-boss/screen/Commerce/Home.xml)

| Station | Sections | Readings |
| --- | --- | --- |
| Orders desk | `Orders` | The tied purse. Baskets to pack for `toFulfill`. A wax seal for `toApprove`. A knotted bundle for `stuckPlacements` · *stack* |
| Till | `Reports` | A slate with net sales for the period and its arrow against the period before, from ironstone.commerce.ReportingServices.get#HomePeriod · *trend* |
| Display stall | `Merchandising`, `Promotions`, `Website` | — |
| Channel post | `Stores`, `Integrations` | A flag per channel from mantle.product.StoreChannelServices.get#ChannelHealth · *flag* |
| Customer book | `Customers` | Abandoned baskets for `openCarts` · *stack* |

### 06 · Counting house / Accounting

Black shutters and a copper coin on a square roof. At the street counter a hinged writing slope meets the visitor’s hand.

The ruled staff ledger reads ironstone.accounting.HomeServices.get#Summary, summary.arOverdue with its currency, for the selected organization and AR capability. Its ribbon opens [Receivables/Collections](../../mr-boss/screen/Accounting/Receivables/Collections.xml). The room is [furnished by hand](design-world-interiors.md#06--counting-house). [Home source](../../mr-boss/screen/Accounting/Home.xml)

| Station | Sections | Readings |
| --- | --- | --- |
| Receivables desk | `Receivables` | The ruled book for `arOverdue`, a second ribbon for `arDue` · *amount* |
| Payables desk | `Payables`, `Approvals` | Bills under a weight for the work rows of bills awaiting approval and bills on hold, a ribbon for `apDue` · *stack*, *amount* |
| Banking desk | `Banking`, `Payments` | The straightedge for `bankBalance`, a loose-coin tray for `unmatched` bank lines · *amount*, *stack* |
| Ledger table | `GeneralLedger`, `Tax`, `Budgets`, `Reports` | Unruled pages for `unposted`, a blotted page for journal batches that failed to post · *stack* |
| Stores shelf | `Inventory`, `FixedAssets` | — |
| Period Close bay, secured | `PeriodClose` | Close pegs, one per check of mantle.ledger.CloseServices.get#Checklist, and an hourglass to the period's end · *progress*, *time left* |

### 07 · Bargain hall / Sales

Two facing gables shelter the offer desk. A customer can spread a quote on its sloping counter.

Inside, sealed offer tubes show Quotes to send. mantle.sales.SalesWorkspaceServices.get#SalesHome supplies the quotesToSend counter. Open its ready listPath with its parameters as Home does. Scope to the selected organization and Sales capabilities. Every counter below comes from the same service with its own listPath. [Home source](../../mr-boss/screen/Sales/Home.xml)

| Station | Sections | Readings |
| --- | --- | --- |
| Lead bench | `Accounts` | Calling cards for `leads` · *stack* |
| Handshake board | `Pipeline` | A slip per opportunity of `myOpportunities`, candle stubs for `pastClose` · *slips*, *time left* |
| Offer desk | `Quotes` | The sealed tubes. A request tray for `quoteRequests`. Candle stubs for `quotesExpiring` · *stack*, *time left* |
| Order rail | `Orders`, `Agreements` | A tied bundle for `ordersBlocked` · *stack* |
| Tally shelf | `Reports` | — |

### 08 · Print yard / Marketing

A rack of drying broadsides hangs above the porch. A press bed and calendar rail sit in the back.

Hang one dated broadside per visible upcoming row from mantle.marketing.MarketingWorkspaceServices.get#Upcoming. Use the route, recordParameter and recordId resolved by Home. The press book opens [Content/BlogPosts](../../mr-boss/screen/Marketing/Content/BlogPosts.xml) to write or review permitted WikiBlog articles. The cards of get#Home supply the counts below, each with its own available, not available or failed state. [Home source](../../mr-boss/screen/Marketing/Home.xml)

| Station | Sections | Readings |
| --- | --- | --- |
| Drying rack | `Campaigns`, `Promotions` | The dated broadsides. A count of `campaigns` running · *slips* |
| Press | `Content` | A wax seal for `approvals` |
| Post table | `Sends`, `Automations` | Letters ready for `dueSends`, a returned-letter basket for `failedDeliveries` · *stack* |
| Ticket desk | `Events`, `Surveys` | A ticket roll filled to `registrations` against the event's capacity · *level* |
| Roll shelf | `Audiences`, `Leads` | A marker for `audienceChanges` · *flag* |
| Tally shelf | `Reports` | — |

### 09 · Mending porch / CustomerService

A darned canvas awning marks the help counter. Shallow cubbies just inside hold individual case slips.

The owner’s slip rack shows the cases returned in cases by ironstone.customerservice.CaseServices.find#Cases using the Home My cases filter. Follow the selected case link in Home. The front help index opens SupportHome. Queue counts come from get#QueueCounts in the same file. [Home source](../../mr-boss/screen/CustomerService/Home.xml)

| Station | Sections | Readings |
| --- | --- | --- |
| Slip rack | `Cases` | The viewer's own cases · *slips* |
| Unclaimed basket | `Cases`, by queue | A pile for `unassignedCount` with “take this one”. A candle for `breachedCount` and a tag for `overdueCount` · *stack*, *time left* |
| Returns shelf | `Returns`, `Orders` | Parcels from ironstone.customerservice.ReturnServices.get#AwaitingReturns · *stack* |
| Pattern book | `Knowledge` | — |
| Rota board | `Availability`, `Approvals` | — |
| Customer book | `Customers`, `Reports` | — |

### 10 · Pattern store / Products

Stepped sample drawers under a sawtooth ridge. One conspicuously blank price tag hangs from the central cabinet.

The blank-tag rail uses ironstone.products.CatalogServices.get#CatalogSummary, summary.missingPrice, when supplied. It opens [Products](../../mr-boss/screen/Products/Products.xml) with list=missingPrice. A sample book on the same cabinet opens a chosen Product360 through the catalog. [Home source](../../mr-boss/screen/Products/Home.xml)

| Station | Sections | Readings |
| --- | --- | --- |
| Sample cabinet | `Products`, `Categories` | Half-finished patterns for `incomplete`, fading samples for `discontinuingSoon`. The chosen product at true size beside a reference figure · *stack*, *model* |
| Tag rail | `Prices`, `SupplierPrices` | The blank tags for `missingPrice` · *stack* |
| Measure drawer | `Units`, `Features` | — |
| Paper drawer | `Documents` | — |

### 11 · Factors’ scales / Purchasing

A gable cut like a scale beam. On the public side the supplier has a writing ledge. Inside, two quote trays balance.

Unsigned requisition slips use ironstone.purchasing.BuyerServices.get#Home, counters.unsourcedCount, opening [Requests](../../mr-boss/screen/Purchasing/Requests.xml). The quote tray opens [Sourcing/QuoteRequests](../../mr-boss/screen/Purchasing/Sourcing/QuoteRequests.xml) with view=due from counters.quoteDueCount when can.listRead. The other counters of the same service follow, each with the link its Home card uses. [Home source](../../mr-boss/screen/Purchasing/Home.xml)

| Station | Sections | Readings |
| --- | --- | --- |
| Requisition spike | `Requests`, `Replenishment` | The unsigned slips. Empty measures for `shortfallCount` · *stack* |
| Quote trays | `Sourcing` | The due tray. The beam tips for `quoteCompareCount`. Candle stubs for `quoteExpiringCount` · *balance*, *time left* |
| Order desk | `Orders`, `Approvals`, `Agreements` | A wax seal for `approvalCount`. Unsent letters for `unsentCount` and `draftCount`. Letters with no return seal for `unacknowledgedCount` · *stack* |
| Landing window | `Receiving`, `Returns` | An hourglass tag for `lateCount` · *time left* |
| Bill chute | `Bills` | Bills under a weight for `heldCount` · *stack* |
| Supplier roll | `Suppliers`, `Performance` | — |

### 12 · Receiving barn / Warehouse

A cantilevered jib at the landing, a broad staff threshold on the street. From the entry the sling hangs north of the open floor.

The working sling uses ironstone.warehouse.HomeServices.get#Home, the receiving queue row and its waitingCount at the selected facility. Open [Receiving/Receipts](../../mr-boss/screen/Warehouse/Receiving/Receipts.xml). Every queue of queueRows has its own station in the order goods move, each with separate ready, waiting and late labels. The room is [furnished by hand](design-world-interiors.md#12--receiving-barn) and is the one room with more than seven stations. [Home source](../../mr-boss/screen/Warehouse/Home.xml)

| Station | Sections | Readings |
| --- | --- | --- |
| Receiving stand | `Receiving` | The working sling, with its notched late tag · *stack* |
| Inspection table | `Inspection` | The inspection queue. A roped cage for `quarantineCount` and `onHoldCount` · *stack* |
| Putaway barrow | `Receiving`, putaway | The putaway queue · *stack* |
| Rack wall | `Locations`, `Inventory`, `Transfers` | A tabletop miniature of the facility's racks. The transfers queue · *model*, *stack* |
| Picking rails | `Picking` | The picking queue, with its backorder tag · *stack* |
| Packing trestle | `Shipping`, pack | Flat cartons for the packing queue. The packed-box preview · *stack*, *model* |
| Shipping desk | `Shipping` | The shipping queue · *stack* |
| Returns bay | `Returns` | The returns queue · *stack* |
| Counts locker | `Counts`, `Reports` | The counts queue · *stack* |
| Handheld cradle | `Handheld` | — |

### 13 · Wheel mill / Manufacturing

Wheel rim in profile above a little spillway. Through the staff portal the centre is a bench with jig blocks.

Jigs on the bench stand for work in progress in the selected plant. ironstone.manufacturing.HomeServices.get#Home, summary.inProgressCount, opens [WorkOrders/Find](../../mr-boss/screen/Manufacturing/WorkOrders/Find.xml) with statusId=WeInProgress. The outside wheel turns at the same pace on a holiday. [Home source](../../mr-boss/screen/Manufacturing/Home.xml)

| Station | Sections | Readings |
| --- | --- | --- |
| Planning table | `Planning`, `Approvals` | — |
| Pattern wall | `BillsOfMaterials`, `Routings` | — |
| Jig bench | `WorkOrders`, `ShopFloor` | The jigs. An hourglass tag for `lateCount`. Empty outlines on a shadow board for `shortCount` · *stack*, *time left*, *pegs* |
| Line shaft | `WorkCenters` | Two gauges on the shaft for the `utilization` and `oee` of kpis, covered when kpiStatus withholds them · *level* |
| Proving bench | `Quality` | A scrap bin filled to `scrapRate` · *level* |
| Out-work door | `Subcontracting` | — |
| Finished shelf | `Reports` | Finished pieces for `completedCount` this month · *stack* |

### 14 · Repair shed / Maintenance

A patched half roof with a ladder against it and an outsized mallet over the door share the mill yard. On arrival an empty tool hook catches the light.

Waiting hooks show unassigned work orders for an authorized organization and facility. mantle.work.MaintenanceReportServices.get#MaintenanceHome, counters.unassignedCount, uses the unassigned link from Home into [WorkOrders/Find](../../mr-boss/screen/Maintenance/WorkOrders/Find.xml). [Home source](../../mr-boss/screen/Maintenance/Home.xml)

| Station | Sections | Readings |
| --- | --- | --- |
| Request hatch | `Requests` | — |
| Hook board | `WorkOrders`, `Scheduling` | The waiting hooks. Red-rag tools for `highPriorityCount`. A candle for `overdueCount` · *pegs*, *time left* |
| Round board | `Preventive`, `Procedures` | A turning board with a candle for `overduePmCount` and blank dials for `unknownMeterCount` · *time left* |
| Plant table | `Equipment`, `Facilities` | The facility's equipment in miniature, with an out-of-order tag for each of `downResourceCount` · *model*, *pegs* |
| Tally shelf | `Reports` | — |

### 15 · Guild kitchen / People

A low chimney and a long table with a place left clear near the door. A hinged screen shields desks on the far side.

The decisions folio opens the Home Decisions waiting destination, [MyTeam](../../mr-boss/screen/People/MyTeam.xml) or [Approvals](../../mr-boss/screen/People/Approvals.xml) according to capability. For the manager with request approval and no staff summary, use mantle.humanres.PeopleServices.find#DueWork with kinds=decision. The staff version uses the Home summary’s decisions count and Approvals. Private payslips stay on their owning screens. [Home source](../../mr-boss/screen/People/Home.xml)

| Station | Sections | Readings |
| --- | --- | --- |
| Coat pegs, by the door | `Time` | The viewer's own peg, coat on or off, from mantle.humanres.AttendanceServices.get#MyClock. A tag for timesheet weeks not submitted · *pegs* |
| Long table | `Employees`, `Staffing` | Empty chairs for `vacancies`, new mugs for `joins` · *pegs* |
| Decisions folio | `MyTeam`, `Approvals` | The folio and a wax seal for `decisions` |
| Purse cupboard, behind the screen | `Payroll`, `Expenses` | — |
| Growth shelf | `Development`, `Performance` | — |
| Tally shelf | `Reports` | — |

### 16 · Proof yard / Projects

Two squat chimneys enclose a bright side court with an anvil at its centre. The public smithy faces a proof bench, the staff door a trestle and work board.

Waiting review slips use ironstone.projects.ProjectServices.get#ProjectsHome, home.counts.reviewsWaiting, when Home exposes hosting. Open [Code/MergeRequests](../../mr-boss/screen/Projects/Code/MergeRequests.xml) with list=waiting. A client gets the client Home branch. The room is [furnished by hand](design-world-interiors.md#16--proof-yard). [Home source](../../mr-boss/screen/Projects/Home.xml)

| Station | Sections | Readings |
| --- | --- | --- |
| Work board | `WorkItems`, `Requests` | A slip per open item of `myOpen`, a candle for `overdue`, a tag for `dueWeek` · *slips*, *time left* |
| Trestle | `Projects`, `Resources` | Milestone posts for `milestonesSoon`, a flag per project of `projectsAtRisk`, for managers · *flag* |
| Code rack | `Code` | The review slips · *stack* |
| Time desk | `Time`, `Billing` | An hour candle burned to `hoursWeek` of `expectedWeek`. A sandglass that runs while the viewer's timer runs (mantle.work.TimesheetServices.get#Timer). A wax seal for `timesheetsToApprove` · *level*, *flag* |
| Reports room | `Reports` | — |

### 17 · Survey loft / Insights

A sighting frame cuts a notch in the roof. Inside, a long sighting rule points across cards laid out by owner.

Turn the attention rack to see one group from ironstone.insights.RegistryServices.get#Attention at a time. Keep scope, as-of time and card.valueText. Select the owner-provided card.url. Each group retains its own failure state. [Home source](../../mr-boss/screen/Insights/Home.xml)

| Station | Sections | Readings |
| --- | --- | --- |
| Attention rack | Home | The owners' cards · *slips* |
| Signal mast | `Alerts` | One flag per firing series of org.moqui.report.ReportServices.get#AlertState, and one ring when a series starts firing · *flag*, *bell* |
| Chart table | `Dashboards`, `Panels`, `Playlists` | — |
| Route table | `Flows` | The two trade routes in miniature, with the same handcarts as the street |
| Report press | `Reports`, `ReportBuilder`, `SavedReports`, `ScheduledReports`, `ManagementDigest` | — |
| Reckoning grid | `CalcGrid` | — |

### 18 · Seal house / Admin

A key-shaped finial and a shallow tray of seal dies inside. Every die has its own impression.

The cracked die shows card.failedJobs.value from org.moqui.admin.ReadinessServices.get#Overview through AdminRegistry.overview. Follow the permitted card URL, preserving its owning mount. System and Tools each require their own grant. [Home source](../../mr-boss/screen/Admin/Home.xml)

| Station | Sections | Readings |
| --- | --- | --- |
| Die tray | `Operations` | The cracked die, and one die for each other readiness card: `transports`, `backup`, `signIn` · *flag* |
| Key board | `Users`, `Access`, `Security` | — |
| Record chest | `Audit`, `Data` | — |
| Courier desk | `Integrations`, `Protocols` | — |
| Perch | `Agents` | — |
| Works door | `System` | — |

### 19 · Winding house / system

A copper governor under a low cupola. The first instrument is an engraved clockwork inspection plate.

Static inspection book opens [Monitor/Metrics](../../../base/tools/screen/System/Monitor/Metrics.xml). Home is the runtime dashboard. SYSTEM_APP and the actual section authorization control the door and the book. Keep operational state inside. [Home source](../../../base/tools/screen/System/Home.xml)

| Station | Sections |
| --- | --- |
| Inspection plate | `Monitor`, `ThreadList`, `Visit`, `Cache`, `PushHealth` |
| Key cabinet | `Access`, `Security`, `Certificates`, `Audit`, `Constitution` |
| Governor | `Instance`, `Conf`, `Components`, `Resource`, `Operations` |
| Record vault | `Data`, `EntitySync` |
| Courier rack | `Integrations`, `Protocols`, `EdgeClients`, `EdgeNode`, `ChatBridges`, `Print` |
| Speaking engines | `Llm`, `Voice`, `TerminalSessions` |

### 20 · Instrument shop / tools

Set square on the lintel. A level drafting table opens on a plain grid of drawers.

The gridded rule opens [Database/Overview](../../../base/tools/screen/Tools/Database/Overview.xml) from the same source as the Home shortcut, with TOOLS_APP. The Load book follows its extra LOAD_RUNNER condition. [Home source](../../../base/tools/screen/Tools/Home.xml)

| Station | Sections |
| --- | --- |
| Drafting table | `Database`, `Schema`, `Sql` |
| Sample drawers | `Data`, `Etl` |
| Test bench | `Service`, `RestApi`, `Load` |
| Gauge wall | `ArtifactStats`, `StatusFlows` |

### 21 · Picture gallery / dashboards

Three empty gilt frames in the pediment. Inside, easels face away from the street.

The catalog easel opens [Home](../../../base/tools/screen/Dashboards/Home.xml) for DASHBOARDS_APP. Its source is the Home screen. A bound sign reads org.moqui.report.ReportServices.render#Dashboard only for a viewer entitled to the selected definition. [Home source](../../../base/tools/screen/Dashboards/Home.xml)

| Station | Sections |
| --- | --- |
| Catalog easel | `Dashboard`, `Playlist`, `Playlists` |
| Framing bench | `Panels`, `Annotations`, `CalcGrid` |
| Pigment shelf | `Datasets`, `Explore`, `Analysis` |
| Hanging schedule | `Alerts`, `Schedules`, `Reports` |
| Store room | `GitSync` |

### 22 · Drawing office / dev

A plumb line hangs from an unfinished gable. A tracing board is the first table inside.

The tracing frame opens [Artifacts/Search](../../../dev/dev-aide/screen/DevAide/Artifacts/Search.xml) from Dev AIDE Home. Keep the dev component and Dev AIDE screen grants on the entrance. All edits use the ordinary IDE. The house is placed only when the dev-aide component is installed. Its place is a garden otherwise. [Home source](../../../dev/dev-aide/screen/DevAide/Home.xml)

| Station | Sections |
| --- | --- |
| Tracing board | `Artifacts`, `Workspace` |
| Plan chest | `Entities`, `Services`, `Screens`, `Eca`, `Data` |
| Roll rack | `Repositories` |
| Ticket spike | `Tasks` |

### 23 · Model shed / gallery

A dollhouse-size facade sits above the door. Inside there is an architectural pattern case.

A sample card opens [Landing](../../../dev/example/screen/Gallery/Landing.xml), the reference gallery’s actual default. The other pattern drawers open the matching archetype. Load on development instances with the gallery’s ADMIN grant. The house is placed only when the example component is installed. Its place is a garden otherwise. [Home source](../../../dev/example/screen/Gallery/Landing.xml)

| Station | Sections |
| --- | --- |
| Page drawers | `Landing`, `List`, `Detail`, `Record360`, `Report`, `Board`, `Calendar` |
| Work drawers | `Task`, `Wizard`, `Editor`, `Handheld`, `Split` |
| Part drawers | `Dialog`, `Panel`, `Message`, `NavCards`, `Views`, `Widgets` |

## Front counters and civic rooms

The public key is the PASS app, resolved against the installation's chosen site. The owning screen constructs deeper links with the site's mount, feature flags, visible records and current identity. Keep invitation tokens in the page that received them. A book can ship as a direct opener at T3, in the framed window. A live insert on a public counter requires a viewer-scoped PASS source adapter, including its authenticated/partial/error states. Until that exists, use the engraved label alone. Each counter has a bell, and ringing it raises the flag behind the pane. [Public app surfaces](../../mr-pass/README.md) · [Transport operation to service mapping](../../mr-pass/service/pass/Schema.js)

| Place and arrival | Furniture, sections and destination | Audience and source |
| --- | --- | --- |
| **01 Gatehouse** · the painted lintel faces the square, a low map desk immediately inside | Account book for sign-in, account home, preferences and Git access. A calling-card stand opens a selected Members profile, and a second leaf lists Sites. A blank calling card invites the visitor to select a member on a page that offers one. The Travellers’ table is at the street end. | Each installed mount decides what can be visited. Account uses its own signed-in hub, Members uses an eligible selected member, Sites its own listing policy. The public directory carries no roll of people. [Account](../../mr-pass/screen/Account/Home.xml) · [Members](../../mr-pass/screen/Members/MemberDetail.xml) · [Sites](../../mr-pass/screen/Sites/List.xml) |
| **02 Forum steps** · a low semicircle opens off the gate road, beside a roofed reading desk | A broadsheet offers **Open Forum** at Topics. The reading desk under the Ceiba offers **Ask Hai Ikthiss**. A filing rail offers Categories, Tags, Search and Guidelines. Personal bookmarks, drafts, messages, compose and moderator tools are in the real forum under its grants. Return through the lower porch or enter the Speakers’ room. | Published site and category visibility, membership and moderation apply. Desk source is the ordinary forum Topics view. [Topics](../../mr-pass/screen/Forum/Topics/List.xml) |
| **05 Market stall** · pick a sample off a folded awning | Shop book opens published categories, products and search. Basket, checkout, orders, payment methods, addresses, downloads and subscriptions belong to the shopper in the real site. | Only published store catalog on a public placard. Personal cart and payment pages stay with the session. [Shop Home](../../mr-pass/screen/Shop/Home.xml) |
| **06 Cashier’s slope** · green-ribboned invoice book | “My invoices” opens the list. A customer follows the invoice’s own pay link. Counterfoil occupies the far end of the slope. | Signed-in site contact for the customer(s) they were invited to represent. The list uses findInvoices. [Invoices](../../mr-pass/screen/Billing/Invoices.xml) |
| **07 Quote counter** · one clean sheet under a brass weight | “My quotes” opens quotes, then the chosen offer and the site's response form. Orders and delivery use the customer’s Orders page or the supplied invitation. | findSalesQuotes for this signed-in contact and site. Token-specific offers require the actual invitation. [Customer quotes](../../mr-pass/screen/Sales/CustomerQuotes.xml) |
| **08 Broadside rack** · a printed invitation facing the gate | Subscribe, published landing pages, forms and event registration from the selected publication or offer. Email preferences belong at Account. The staff press upstairs has the current Blog posts desk. Reserve its public Blog hatch for a future mount. | getMarketingPublication for the site and chosen list. Exposure of an event or form follows its own publication. [Subscribe](../../mr-pass/screen/Marketing/Subscribe.xml) · [BOSS blog desk](../../mr-boss/screen/Marketing/Content/BlogPosts.xml) · [PASS publishing design](../../mr-pass/plan/design-forum.md) |
| **09 Help counter** · a rack of mending patterns | Beside the pattern rack, **Ask Grok the Rock** opens a private conversation. **Open Support** leads to Support Home for articles, My cases, My returns and the site's case/return forms. An invitation to rate a case goes straight to its real Feedback page. | getSupportHome for the current site and reader. The street sign is a stitched sleeve with a plain label. [Support Home](../../mr-pass/screen/Support/SupportHome.xml) |
| **11 Supplier ledge** · scale beam over a writing slope | Supplier Home takes the supplier to quote response, order acknowledgement or bill upload. A small bill chute stays here even though Accounts later processes the bill. | getSupplierHome under the current site, contact or valid token. Only the real form submits. [Supplier Home](../../mr-pass/screen/Supplier/SupplierHome.xml) |
| **16 Public smithy** · issue slips hang beside the two-leaf proof book | Select a visible repository. Issues, merge requests, files/history, checks/pipelines, releases and Docs belong to its own rack. Group and repository features, account actions, settings and moderation open from the real Smithy. | Selected site, feature, repository and record grants. See the [proof yard](design-world-interiors.md#16--proof-yard) for source operations and case-specific links. [Repository screens](../../mr-pass/screen/Smithy/RepositoryDetail.xml) |

## Lots and other installed entries

The plan holds six spare lots, A to F. A building arrives on one as a mod: a component with an app of its own names the district and house type it wants, and Millrace gives it the first free lot that fits ([lots](design-technical.md#173-lots-slots-and-generated-rooms)). With no sheet of its own the app still gets a generated room. Three buildings are conditional from the start, each drawn only when its component is installed: the Drawing office and the Model shed at their numbered places inside the works wall, and a coach house for Marble ERP on the first market lot.

At the gate table a separate leaf, **Other apps**, lists the ordinary launcher entries available to this viewer that have no building. Marble ERP and its My Account are optional neighbors outside THREE's division scheme. Select Marble ERP, at its coach house or on the leaf, to open its dashboard, whose paths cover search, accounting, assets, catalog, customers, facilities, HR, manufacturing, orders, parties, projects, stores, requests, returns, shipments, shipping, suppliers, surveys, tasks, wiki and reports. My Account opens its User section with Expense beside it. It remains distinct from the PASS Account counter. The standalone SimpleScreens mount is commented out. [Marble mounts](../../mr-marble/MoquiConf.xml) · [Marble bays](../../mr-marble/screen/marble.xml) · [My Account](../../mr-marble/screen/MyAccount.xml)

BOSS mounts BasicComms screens in Comms and suppresses the second launcher tile. The post loft therefore offers one Comms entrance. Framework AppList, ScreenTree and Terminal remain reachable through the shell to users who have them. A demo deployment follows its effective menu restrictions. HOME has no app mount in this checkout, and Mantle and localization supply services/data to other apps. [BOSS mounts](../../mr-boss/MoquiConf.xml) · [BasicComms mounts](../../../base/basic-comms/MoquiConf.xml) · [Shell screens](../../../base/webroot/screen/webroot/apps) · [Development set](../../../dev/README.md) · [HOME](../../mr-home/README.md)
