# Millrace building sheets

Use the [site plan](design-world.md#1-town-plan) numbers for wayfinding. Each house below has a face, an arrival object, a front meeting place and a ring of wall bays. Section names in code are exact path segments beneath the linked root. In the room use the screen’s localized menu title. Each bay has a labeled book opening its section. For a detail screen that requires a record, the shelf first opens its authorized selector. A saved Dashboard comes from Home and a Playlist from Playlists, each with its definitionId. Its source is that section’s menu entry as resolved under the viewer’s grants and menu conditions. A changing object has its additional source in the sheet. The seating recess, street meeting entrance and return portal use the [shared floor plan](design-world.md#3-buildings-and-thresholds).

## Street directory

| Plan | Street sign | Staff app under /qapps | PASS counter on the configured site | Meeting place |
| ---: | --- | --- | --- | --- |
| 01 | Gatehouse | — | Account, Members, Sites | Travellers’ table |
| 02 | Forum steps | — | Forum | Speakers’ room |
| 03 | Clock house | [Commons](../../mr-boss/screen/Commons.xml) | Staff entrance | Bell loft |
| 04 | Post loft | [Comms](../../mr-boss/screen/Comms.xml) | Staff entrance | Pigeon loft |
| 05 | Market hall | [Commerce](../../mr-boss/screen/Commerce.xml) | Shop | Market chamber |
| 06 | Counting house | [Accounting](../../mr-boss/screen/Accounting.xml) | Billing | Reckoning room |
| 07 | Bargain hall | [Sales](../../mr-boss/screen/Sales.xml) | Sales | Handshake room |
| 08 | Print yard | [Marketing](../../mr-boss/screen/Marketing.xml) | Marketing | Paper room |
| 09 | Mending porch | [CustomerService](../../mr-boss/screen/CustomerService.xml) | Support | Quiet parlour |
| 10 | Pattern store | [Products](../../mr-boss/screen/Products.xml) | Staff entrance | Sample room |
| 11 | Factors’ scales | [Purchasing](../../mr-boss/screen/Purchasing.xml) | Supplier | Weigh room |
| 12 | Receiving barn | [Warehouse](../../mr-boss/screen/Warehouse.xml) | Staff entrance | Sling room |
| 13 | Wheel mill | [Manufacturing](../../mr-boss/screen/Manufacturing.xml) | Staff entrance | Mill table |
| 14 | Repair shed | [Maintenance](../../mr-boss/screen/Maintenance.xml) | Staff entrance | Wheelwright’s bench |
| 15 | Guild kitchen | [People](../../mr-boss/screen/People.xml) | Staff entrance | Fireside room |
| 16 | Proof yard | [Projects](../../mr-boss/screen/Projects.xml) | Smithy | Proof room |
| 17 | Survey loft | [Insights](../../mr-boss/screen/Insights.xml) | Staff entrance | Map room |
| 18 | Seal house | [Admin](../../mr-boss/screen/Admin.xml) | Staff entrance | Charter room |
| 19 | Winding house | [system](../../../base/tools/screen/System.xml) | Staff entrance | Watch room |
| 20 | Instrument shop | [tools](../../../base/tools/screen/Tools.xml) | Staff entrance | Instrument room |
| 21 | Picture gallery | [dashboards](../../../base/tools/screen/Dashboards.xml) | Staff entrance | Hanging room |
| 22 | Drawing office | [dev](../../../dev/dev-aide/screen/DevAide.xml) | Staff entrance | Tracing room |
| 23 | Model shed | [gallery](../../../dev/example/screen/Gallery.xml) | Staff entrance | Model room |

## Staff rooms

At each threshold the visitor sees the arrival object on the clear centre floor. Start clockwise on the left wall and wrap shelves around the back and right walls in the listed order. Where many sections share a wall, group three or four books to a fitted desk. A labeled cupboard by the exit holds Settings where present. Grant-sensitive bays take shutters or a locked portal according to the real section policy. The menu includes subsections registered by other components only when installed. Home has the centre desk except where the sheet names a different default.

### 03 · Clock house / Commons

A strip of blue day tiles wraps the tower. Inside, broad shallow drawers fit under a calendar drum.

Draw out today’s leaf. The desk opens [Calendar](../../mr-boss/screen/Commons/Calendar.xml) and shows only events the viewer may read, from Home’s mantle.work.EventServices.get#FullCalendarData with the same range and filters. The exterior clock carries the date alone. [Home source](../../mr-boss/screen/Commons/Home.xml)

**Wall bays** · `Calendar`, `Contacts`, `Files`, `Knowledge`, `Notes`, `Notifications`, `Requests`, `Tasks`.

### 04 · Post loft / Comms

A dovecote roof stands over sorting holes. The first thing within reach is the incoming letter tray.

The slotted tray opens [Inbox](../../../base/basic-comms/screen/BasicComms/Inbox.xml), the default entry. Its carved label is static. For the personal overview use the Home book, read by org.moqui.comms.InboxServices.run#Operation (get#CommsHome) and get#HomeSources. BOSS extends the BasicComms Home. [Home source](../../../base/basic-comms/screen/BasicComms/Home.xml)

**Wall bays** · `Calls`, `Chat`, `Consent`, `Inbox`, `Moderation`.

### 05 · Market hall / Commerce

Limewashed upper chamber on open timber posts. The public stall has an awning. The Orders desk faces the staff threshold.

A tied purse shows payment holds in the viewer’s stores. mantle.order.StoreOperationsServices.get#StoreWorkSummary counts.paymentHolds opens [Orders/Orders](../../mr-boss/screen/Commerce/Orders/Orders.xml) with attention=paymentHold and store scope when can.orders. [Home source](../../mr-boss/screen/Commerce/Home.xml)

**Wall bays** · `Customers`, `Integrations`, `Merchandising`, `Orders`, `Promotions`, `Reports`, `Stores`, `Website`.

### 06 · Counting house / Accounting

Black shutters and a copper coin on a square roof. At the street counter a hinged writing slope meets the visitor’s hand.

The ruled staff ledger reads ironstone.accounting.HomeServices.get#Summary, summary.arOverdue with its currency, for the selected organization and AR capability. Its ribbon opens [Receivables/Collections](../../mr-boss/screen/Accounting/Receivables/Collections.xml). [Home source](../../mr-boss/screen/Accounting/Home.xml)

**Wall bays** · `Approvals`, `Banking`, `Budgets`, `FixedAssets`, `GeneralLedger`, `Inventory`, `Payables`, `Payments`, `PeriodClose`, `Receivables`, `Reports`, `Tax`.

### 07 · Bargain hall / Sales

Two facing gables shelter the offer desk. A customer can spread a quote on its sloping counter.

Inside, sealed offer tubes show Quotes to send. mantle.sales.SalesWorkspaceServices.get#SalesHome supplies the quotesToSend counter. Open its ready listPath with its parameters as Home does. Scope to the selected organization and Sales capabilities. [Home source](../../mr-boss/screen/Sales/Home.xml)

**Wall bays** · `Accounts`, `Agreements`, `Orders`, `Pipeline`, `Quotes`, `Reports`.

### 08 · Print yard / Marketing

A rack of drying broadsides hangs above the porch. A press bed and calendar rail sit in the back.

Hang one dated broadside per visible upcoming row from mantle.marketing.MarketingWorkspaceServices.get#Upcoming. Use the route, recordParameter and recordId resolved by Home. The press book opens [Content/BlogPosts](../../mr-boss/screen/Marketing/Content/BlogPosts.xml) to write or review permitted WikiBlog articles. [Home source](../../mr-boss/screen/Marketing/Home.xml)

**Wall bays** · `Audiences`, `Automations`, `Campaigns`, `Content`, `Events`, `Leads`, `Promotions`, `Reports`, `Sends`, `Surveys`.

### 09 · Mending porch / CustomerService

A darned canvas awning marks the help counter. Shallow cubbies just inside hold individual case slips.

The owner’s slip rack shows the cases returned in cases by ironstone.customerservice.CaseServices.find#Cases using the Home My cases filter. Follow the selected case link in Home. The front help index opens SupportHome. [Home source](../../mr-boss/screen/CustomerService/Home.xml)

**Wall bays** · `Approvals`, `Availability`, `Cases`, `Customers`, `Knowledge`, `Orders`, `Reports`, `Returns`.

### 10 · Pattern store / Products

Stepped sample drawers under a sawtooth ridge. One conspicuously blank price tag hangs from the central cabinet.

The blank-tag rail uses ironstone.products.CatalogServices.get#CatalogSummary, summary.missingPrice, when supplied. It opens [Products](../../mr-boss/screen/Products/Products.xml) with list=missingPrice. A sample book on the same cabinet opens a chosen Product360 through the catalog. [Home source](../../mr-boss/screen/Products/Home.xml)

**Wall bays** · `Categories`, `Documents`, `Features`, `Prices`, `Products`, `SupplierPrices`, `Units`.

### 11 · Factors’ scales / Purchasing

A gable cut like a scale beam. On the public side the supplier has a writing ledge. Inside, two quote trays balance.

Unsigned requisition slips use ironstone.purchasing.BuyerServices.get#Home, counters.unsourcedCount, opening [Requests](../../mr-boss/screen/Purchasing/Requests.xml). The quote tray opens [Sourcing/QuoteRequests](../../mr-boss/screen/Purchasing/Sourcing/QuoteRequests.xml) with view=due from counters.quoteDueCount when can.listRead. [Home source](../../mr-boss/screen/Purchasing/Home.xml)

**Wall bays** · `Agreements`, `Approvals`, `Bills`, `Orders`, `Performance`, `Receiving`, `Replenishment`, `Requests`, `Returns`, `Sourcing`, `Suppliers`.

### 12 · Receiving barn / Warehouse

A cantilevered jib at the landing, a broad staff threshold on the street. From the entry the sling hangs north of the open floor.

The working sling uses ironstone.warehouse.HomeServices.get#Home, the receiving queue row and its waitingCount at the selected facility. Open [Receiving/Receipts](../../mr-boss/screen/Warehouse/Receiving/Receipts.xml). Picking, packing and exceptions have separate labels. [Home source](../../mr-boss/screen/Warehouse/Home.xml)

**Wall bays** · `Counts`, `Handheld`, `Inspection`, `Inventory`, `Locations`, `Picking`, `Receiving`, `Reports`, `Returns`, `Shipping`, `Transfers`.

### 13 · Wheel mill / Manufacturing

Wheel rim in profile above a little spillway. Through the staff portal the centre is a bench with jig blocks.

Jigs on the bench stand for work in progress in the selected plant. ironstone.manufacturing.HomeServices.get#Home, summary.inProgressCount, opens [WorkOrders/Find](../../mr-boss/screen/Manufacturing/WorkOrders/Find.xml) with statusId=WeInProgress. The outside wheel turns at the same pace on a holiday. [Home source](../../mr-boss/screen/Manufacturing/Home.xml)

**Wall bays** · `Approvals`, `BillsOfMaterials`, `Planning`, `Quality`, `Reports`, `Routings`, `ShopFloor`, `Subcontracting`, `WorkCenters`, `WorkOrders`.

### 14 · Repair shed / Maintenance

A patched half roof and broad wheelwright’s hoop share the mill yard. On arrival an empty tool hook catches the light.

Waiting hooks show unassigned work orders for an authorized organization and facility. mantle.work.MaintenanceReportServices.get#MaintenanceHome, counters.unassignedCount, uses the unassigned link from Home into [WorkOrders/Find](../../mr-boss/screen/Maintenance/WorkOrders/Find.xml). [Home source](../../mr-boss/screen/Maintenance/Home.xml)

**Wall bays** · `Equipment`, `Facilities`, `Preventive`, `Procedures`, `Reports`, `Requests`, `Scheduling`, `WorkOrders`.

### 15 · Guild kitchen / People

A low chimney and a long table with a place left clear near the door. A hinged screen shields desks on the far side.

The decisions folio opens the Home Decisions waiting destination, [MyTeam](../../mr-boss/screen/People/MyTeam.xml) or [Approvals](../../mr-boss/screen/People/Approvals.xml) according to capability. For the manager with request approval and no staff summary, use mantle.humanres.PeopleServices.find#DueWork with kinds=decision. The staff version uses the Home summary’s decisions count and Approvals. Private payslips stay on their owning screens. [Home source](../../mr-boss/screen/People/Home.xml)

**Wall bays** · `Approvals`, `Development`, `Employees`, `Expenses`, `MyTeam`, `Payroll`, `Performance`, `Reports`, `Staffing`, `Time`.

### 16 · Proof yard / Projects

Two squat chimneys enclose a bright side court. The public smithy faces a proof bench, the staff door a trestle and work board.

Waiting review slips use ironstone.projects.ProjectServices.get#ProjectsHome, home.counts.reviewsWaiting, when Home exposes hosting. Open [Code/MergeRequests](../../mr-boss/screen/Projects/Code/MergeRequests.xml) with list=waiting. A client gets the client Home branch. [Home source](../../mr-boss/screen/Projects/Home.xml)

**Wall bays** · `Billing`, `Code`, `Projects`, `Reports`, `Requests`, `Resources`, `Time`, `WorkItems`.

### 17 · Survey loft / Insights

A sighting frame cuts a notch in the roof. Inside, a long sighting rule points across cards laid out by owner.

Turn the attention rack to see one group from ironstone.insights.RegistryServices.get#Attention at a time. Keep scope, as-of time and card.valueText. Select the owner-provided card.url. Each group retains its own failure state. [Home source](../../mr-boss/screen/Insights/Home.xml)

**Wall bays** · `Alerts`, `CalcGrid`, `Dashboards`, `Flows`, `ManagementDigest`, `Panels`, `Playlists`, `ReportBuilder`, `Reports`, `SavedReports`, `ScheduledReports`.

### 18 · Seal house / Admin

A key-shaped finial and a shallow tray of seal dies inside. Every die has its own impression.

The cracked die shows card.failedJobs.value from org.moqui.admin.ReadinessServices.get#Overview through AdminRegistry.overview. Follow the permitted card URL, preserving its owning mount. System and Tools each require their own grant. [Home source](../../mr-boss/screen/Admin/Home.xml)

**Wall bays** · `Access`, `Agents`, `Audit`, `Data`, `Integrations`, `Operations`, `Protocols`, `Security`, `System`, `Users`.

### 19 · Winding house / system

A copper governor under a low cupola. The first instrument is an engraved clockwork inspection plate.

Static inspection book opens [Monitor/Metrics](../../../base/tools/screen/System/Monitor/Metrics.xml). Home is the runtime dashboard. SYSTEM_APP and the actual section authorization control the door and the book. Keep operational state inside. [Home source](../../../base/tools/screen/System/Home.xml)

**Wall bays** · `Access`, `Audit`, `Cache`, `Certificates`, `Components`, `Conf`, `Constitution`, `Data`, `EdgeClients`, `EdgeNode`, `EntitySync`, `Instance`, `Integrations`, `Llm`, `Monitor`, `Operations`, `Print`, `Protocols`, `Resource`, `Security`, `TerminalSessions`, `ThreadList`, `Visit`, `Voice`, `ChatBridges`, `PushHealth`.

### 20 · Instrument shop / tools

Set square on the lintel. A level drafting table opens on a plain grid of drawers.

The gridded rule opens [Database/Overview](../../../base/tools/screen/Tools/Database/Overview.xml) from the same source as the Home shortcut, with TOOLS_APP. The Load bay follows its extra LOAD_RUNNER condition. [Home source](../../../base/tools/screen/Tools/Home.xml)

**Wall bays** · `ArtifactStats`, `Data`, `Database`, `Etl`, `Load`, `RestApi`, `Schema`, `Service`, `Sql`, `StatusFlows`.

### 21 · Picture gallery / dashboards

Three empty gilt frames in the pediment. Inside, easels face away from the street.

The catalog easel opens [Home](../../../base/tools/screen/Dashboards/Home.xml) for DASHBOARDS_APP. Its source is the Home screen. A bound sign reads org.moqui.report.ReportServices.render#Dashboard only for a viewer entitled to the selected definition. [Home source](../../../base/tools/screen/Dashboards/Home.xml)

**Wall bays** · `Alerts`, `Analysis`, `Annotations`, `CalcGrid`, `Dashboard`, `Datasets`, `Explore`, `GitSync`, `Panels`, `Playlist`, `Playlists`, `Reports`, `Schedules`.

### 22 · Drawing office / dev

A plumb line hangs from an unfinished gable. A tracing board is the first table inside.

The tracing frame opens [Artifacts/Search](../../../dev/dev-aide/screen/DevAide/Artifacts/Search.xml) from Dev AIDE Home. Keep the dev component and Dev AIDE screen grants on the entrance. All edits use the ordinary IDE. [Home source](../../../dev/dev-aide/screen/DevAide/Home.xml)

**Wall bays** · `Artifacts`, `Data`, `Eca`, `Entities`, `Repositories`, `Screens`, `Services`, `Tasks`, `Workspace`.

### 23 · Model shed / gallery

A dollhouse-size facade sits above the door. Inside there is an architectural pattern case.

A sample card opens [Landing](../../../dev/example/screen/Gallery/Landing.xml), the reference gallery’s actual default. The other pattern drawers open the matching archetype. Load on development instances with the gallery’s ADMIN grant. [Home source](../../../dev/example/screen/Gallery/Landing.xml)

**Wall bays** · `Board`, `Calendar`, `Detail`, `Dialog`, `Editor`, `Handheld`, `Landing`, `List`, `Message`, `NavCards`, `Panel`, `Record360`, `Report`, `Split`, `Task`, `Views`, `Widgets`, `Wizard`.

## Front counters and civic rooms

The public key is the PASS app, resolved against the installation's chosen site. The owning screen constructs deeper links with the site's mount, feature flags, visible records and current identity. Keep invitation tokens in the page that received them. A book can ship as a direct opener at T3. A live insert on a public counter requires a viewer-scoped PASS source adapter, including its authenticated/partial/error states. Until that exists, use the engraved label alone. [Public app surfaces](../../mr-pass/README.md) · [Transport operation to service mapping](../../mr-pass/service/pass/Schema.js)

| Place and arrival | Furniture, sections and destination | Audience and source |
| --- | --- | --- |
| **01 Gatehouse** · the painted lintel faces the square, a low map desk immediately inside | Account book for sign-in, account home, preferences and Git access. A calling-card stand opens a selected Members profile, and a second leaf lists Sites. A blank calling card invites the visitor to select a member on a page that offers one. The Travellers’ table is at the street end. | Each installed mount decides what can be visited. Account uses its own signed-in hub, Members uses an eligible selected member, Sites its own listing policy. The public directory carries no roll of people. [Account](../../mr-pass/screen/Account/Home.xml) · [Members](../../mr-pass/screen/Members/MemberDetail.xml) · [Sites](../../mr-pass/screen/Sites/List.xml) |
| **02 Forum steps** · a low semicircle opens off the gate road, beside a roofed reading desk | A broadsheet opens Topics. A filing rail offers Categories, Tags, Search and Guidelines. Personal bookmarks, drafts, messages, compose and moderator tools are in the real forum under its grants. Return through the lower porch or enter the Speakers’ room. | Published site and category visibility, membership and moderation apply. Desk source is the ordinary forum Topics view. [Topics](../../mr-pass/screen/Forum/Topics/List.xml) |
| **05 Market stall** · pick a sample off a folded awning | Shop book opens published categories, products and search. Basket, checkout, orders, payment methods, addresses, downloads and subscriptions belong to the shopper in the real site. | Only published store catalog on a public placard. Personal cart and payment pages stay with the session. [Shop Home](../../mr-pass/screen/Shop/Home.xml) |
| **06 Cashier’s slope** · green-ribboned invoice book | “My invoices” opens the list. A customer follows the invoice’s own pay link. Counterfoil occupies the far end of the slope. | Signed-in site contact for the customer(s) they were invited to represent. The list uses findInvoices. [Invoices](../../mr-pass/screen/Billing/Invoices.xml) |
| **07 Quote counter** · one clean sheet under a brass weight | “My quotes” opens quotes, then the chosen offer and the site's response form. Orders and delivery use the customer’s Orders page or the supplied invitation. | findSalesQuotes for this signed-in contact and site. Token-specific offers require the actual invitation. [Customer quotes](../../mr-pass/screen/Sales/CustomerQuotes.xml) |
| **08 Broadside rack** · a printed invitation facing the gate | Subscribe, published landing pages, forms and event registration from the selected publication or offer. Email preferences belong at Account. The staff press upstairs has the current Blog posts desk. Reserve its public Blog hatch for a future mount. | getMarketingPublication for the site and chosen list. Exposure of an event or form follows its own publication. [Subscribe](../../mr-pass/screen/Marketing/Subscribe.xml) · [BOSS blog desk](../../mr-boss/screen/Marketing/Content/BlogPosts.xml) · [PASS publishing design](../../mr-pass/plan/design-forum.md) |
| **09 Help counter** · a rack of mending patterns | Support Home offers articles, My cases, My returns and the site's case/return forms. An invitation to rate a case goes straight to its real Feedback page. | getSupportHome for the current site and reader. The street sign is a stitched sleeve with a plain label. [Support Home](../../mr-pass/screen/Support/SupportHome.xml) |
| **11 Supplier ledge** · scale beam over a writing slope | Supplier Home takes the supplier to quote response, order acknowledgement or bill upload. A small bill chute stays here even though Accounts later processes the bill. | getSupplierHome under the current site, contact or valid token. Only the real form submits. [Supplier Home](../../mr-pass/screen/Supplier/SupplierHome.xml) |
| **16 Public smithy** · issue slips hang beside the two-leaf proof book | Select a visible repository. Issues, merge requests, files/history, checks/pipelines, releases and Docs belong to its own rack. Group and repository features, account actions, settings and moderation open from the real Smithy. | Selected site, feature, repository and record grants. See the [proof yard](design-world-interiors.md#16--proof-yard) for source operations and case-specific links. [Repository screens](../../mr-pass/screen/Smithy/RepositoryDetail.xml) |

## Other installed entries

At the gate table a separate leaf, **Other apps**, lists the ordinary launcher entries available to this viewer. Marble ERP and its My Account are optional neighbors outside THREE's division scheme. Their door is a painted coach on the gate map. Select Marble ERP to open its dashboard, whose paths cover search, accounting, assets, catalog, customers, facilities, HR, manufacturing, orders, parties, projects, stores, requests, returns, shipments, shipping, suppliers, surveys, tasks, wiki and reports. My Account opens its User section with Expense beside it. It remains distinct from the PASS Account counter. The standalone SimpleScreens mount is commented out. [Marble mounts](../../mr-marble/MoquiConf.xml) · [Marble bays](../../mr-marble/screen/marble.xml) · [My Account](../../mr-marble/screen/MyAccount.xml)

BOSS mounts BasicComms screens in Comms and suppresses the second launcher tile. The post loft therefore offers one Comms entrance. Framework AppList, ScreenTree and Terminal remain reachable through the shell to users who have them. Dev AIDE and Gallery are removable lots. A demo deployment follows its effective menu restrictions. HOME has no app mount in this checkout, and Mantle and localization supply services/data to other apps. [BOSS mounts](../../mr-boss/MoquiConf.xml) · [BasicComms mounts](../../../base/basic-comms/MoquiConf.xml) · [Shell screens](../../../base/webroot/screen/webroot/apps) · [Development set](../../../dev/README.md) · [HOME](../../mr-home/README.md)
