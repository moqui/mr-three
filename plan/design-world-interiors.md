# Three rooms at Millrace

These are the first three furnished interiors. North is up on each floor sketch. Rooms each have their own local axes. The street portal connects to the door marked **IN** on the plate regardless of the orientation of the exterior model. Each paired house is 32 × 24 m. Allow 3 m around a central table and a 2 m clear line through the front strip. Furniture symbols mark anchors. Every line of wall, including a glass partition, runs solid across the walk grid except at a portal. Doors pass one person through on selection and stay visually closed. The long screen names on bay books appear at focus distance. [Building register and full bay lists](design-world-buildings.md)

## 12 · Receiving barn

The big sling at the quay casts a fishhook shadow on the wall. Through the staff threshold there is a **second** sling, low enough to touch. A folded canvas hangs from the weighing wheel to its left. The receipt book is on a waist-high stand to the right. Leave the middle of the floor clear enough to turn a handcart around.

```text
                    NORTH · rack wall
┌─────────────────────────────────────────────────────┐
│ Locations · Inventory · Transfers     Counts locker │
│                                                     │
│ Receiving   working sling      Putaway → Inspection │
│ book        tallywheel                              │
│             ○ clear turning floor    Picking rails  │
│ Returns                              Shipping desk  │
│ Handheld cradle      Home · facility     Reports    │
│ Settings cupboard         staff IN / OUT            │
└───────────────────────────────╥─────────────────────┘
                          street porch    ⚿ Sling room
```

A 28 × 20 m staff hall is sufficient. The separate porch door goes straight into the room's call space. A visitor may use that door when a member. They see the static quay jib and a locked staff threshold. There is no front-office glass here.

| Object | Reading and working screen |
| --- | --- |
| **Working sling** | In the facility’s receiving row, waitingCount means open receipts less overdue receipts. Use an empty cradle at zero, one bale for 1–4, two for 5–19, three for 20+. A separate square, notched tag carries lateCount. Either selects Receiving/Receipts, the same unfiltered queue destination as Home. Keep each reading’s unavailable state separate. |
| **Weighing wheel** | A small speaking pan identifies the Tallywheel Assist specialist. “Why are these different?” opens its curated private conversation with this visible facility context. The nearby plain tally plate always opens the real Home. |
| **Packing trestle** | Show the readyCount of operation packing when provided, capped visually at three flat folded cartons. Choose Shipping/Pack. The shop-floor packing page carries the work and any future box preview. |
| **Facility board** | Home shows the selected facility using the ordinary facility selection. A scope change unloads former counts and labels before repaint. Give the handheld cradle a fixed book for the real Handheld/Home. |

For a stale reading show the previous permitted sling with the clock sleeve. Missing takes the canvas insert off the hook. Withheld laces the cover shut. Failure stands the little repair trestle beside it. One source failure leaves the screen books usable. Returning from a receipt restores the receiving stand and its focus. [Warehouse Home and route map](../../mr-boss/screen/Warehouse/Home.xml) · [Counts and capability rules](../../mr-boss/service/ironstone/warehouse/HomeServices.xml) · [Receipts](../../mr-boss/screen/Warehouse/Receiving/Receipts.xml) · [Packing](../../mr-boss/screen/Warehouse/Shipping/Pack.xml)

## 06 · Counting house

The roof pigeon has learned that the chimney on the left stays warm. Inside, the visitor's writing slope faces a green ribbon long enough to pull one small book within reach. Through the stippled pane the staff room has the outline of a long table. A staff member entering it can finally see the red ruling on its pages.

```text
┌───────────────┬─────────────────────────────────┐
│ Period Close  ⚿  General Ledger · Tax · Reports │
├───────────────┘                                 │
│ Banking · Payments    ruled AR book     Budgets │
│ Payables · Approvals     clear floor    Assets  │
│ Receivables desk   Home · organization Inventory│
│ Settings            STAFF PORTAL                │
├──── stippled glass ──────╥───── solid wall ─────┤
│ cashier · Counterfoil    ║       ⚿ Reckoning rm │
│           Billing counter      public IN / OUT  │
└─────────────────────────────────────────────────┘
```

Assets is the **Fixed Assets** bay. The Period Close latch follows the section check. Its individual books follow their own screen conditions. For example, Home offers the Fiscal periods card only for glPost or fiscalClose. A desk or mark never authorizes posting, closing or payment.

| Object | Reading and working screen |
| --- | --- |
| **Cashier’s invoice book** | The ordinary opener is Billing/Invoices. A proposed PASS insert can show a folded corner for each of the first three returned invoice rows visible to this signed-in contact at this site. Label “Invoices shown” and tell the actual list length. A successfully empty list lays the ribbon flat. A site visitor lacking invoice context sees a sign-in/invitation cue. A lookup error is unavailable. No claim that every invoice is unpaid. |
| **Counterfoil** | The tethered intelligent book explains what this customer can do at Billing. A small conversation mark distinguishes its Ask action from opening My invoices. It can point to the invoice's existing pay link, whose screen handles payment. |
| **Ruled receivables book** | With a selected organization and the Home AR capability, summary.arOverdue is a monetary amount. At zero the book closes on an embossed zero. A positive amount raises a single tall ribbon, with the localized amount and currency on focus. A signed credit, if supplied, uses its own marked tab and signed label. No “number of debts” implied by the drawing. Select Accounting/Receivables/Collections with organizationPartyId. |
| **Banker's straightedge** | For the Home bank-reader capability the exact summary.bankBalance and currency are on a personal card. Choose Accounting/Banking/BankAccounts in the same organization. No currency comparisons or division totals at the public desk. |

Stale keeps the amount and as-of under a dated slip. Restricted closes the leather cover. Missing leaves the fixed lectern and no reading. Failure changes the reading plate to the repair mark. On return from Collections, focus the ribbon at the staff table. On return from the PASS invoice list, focus the front counter, including when the page opened in another tab. [Accounting Home](../../mr-boss/screen/Accounting/Home.xml) · [Collections](../../mr-boss/screen/Accounting/Receivables/Collections.xml) · [PASS invoices](../../mr-pass/screen/Billing/Invoices.xml)

## 16 · Proof yard

The public Smithy is a place for comparing two versions of a pattern. One leaf holds the old cut, one the new. A third hook along the same bench holds check tags. Set all three within arm's reach and put the bench against the side wall so someone can read without other visitors walking through the page.

```text
┌──────────────────────────────────────────────────┐
│ Projects · Requests · Resources    Reports room  │
│ Work Items board       trestle       Time, Billing│
│ Code rack          Home · my work    ⚿ Settings   │
│                    STAFF PORTAL                  │
├── stippled glass ───────╥─────── solid wall ─────┤
│ issue slips → two-leaf proof → check tags        │
│                 Proof Press       ⚿ Proof room   │
│ site / repository shelf          public IN / OUT │
└──────────────────────────────────────────────────┘
```

The selected repository and request belong to the viewer. The public shelf has permanent engraved nouns, with private overlays after selecting an authorized record. A shutter covers disabled issue or review features where policy permits disclosure. An undiscoverable repository has no label, shelf entry or object reading. Behind the glass the Projects Home table has a separate staff stack. **Reviews waiting on me** comes from home.counts.reviewsWaiting when home.hosting is available. It opens Projects/Code/MergeRequests with list=waiting. Projects' client Home gets its existing client view and personal work, with staff indicators absent. [Staff Home](../../mr-boss/screen/Projects/Home.xml) · [Waiting review list](../../mr-boss/screen/Projects/Code/MergeRequests.xml)

### The 0.1.0 review walk

The route is available to a repository that hosts this work in Smithy. “0.1.0” is the release the person chooses, using its real milestone or issue where one exists. The room does not assume the MR Moqui GitHub project, a CI service or an external tracker has been imported. Mounted page addresses come from Smithy. For example, within the selected repository, the request suffix is `/-/merge-requests/<number>/changes`.

| Stop and object | Evidence available at the bench | Action in the real screen |
| --- | --- | --- |
| **1 · Issue slip** | Open the repository's Issues, select the release issue and read its status, discussion and workItemKey if supplied. The listing uses findIssues, with the same filters and confidentiality rules. A blank board means no visible slips only after the list succeeds. | Read or update the issue under ordinary capabilities. A Smithy issue represents its project's task. Keep its actual key for follow-up. [Issue list](../../mr-pass/screen/Smithy/RepositoryDetail/Issues.xml) · [Issue detail](../../mr-pass/screen/Smithy/IssueDetail.xml) · [Linked changes](../../mr-pass/screen/Smithy/SmithyIssues.js) |
| **2 · Two-leaf proof** | Use the issue’s Linked section when linkedMergeRequests contains the relevant request, following its actual repository path. Otherwise the contributor selects it from the repository’s real list. Do not infer an issue/request relationship from similar titles. The proof book remembers this request for the current visitor. | Open Changes to inspect the diff and line threads. A permitted reviewer may leave a line comment on its existing form. Overview holds the review summary and offers the allowed decisions. [Merge requests](../../mr-pass/screen/Smithy/RepositoryDetail/MergeRequests.xml) · [Changes](../../mr-pass/screen/Smithy/MergeRequestDetail/Changes.xml) · [Overview](../../mr-pass/screen/Smithy/MergeRequestDetail/Overview.xml) |
| **3 · Check tags** | Use getMergeRequest for this repository, request and current head. For each visible check in checkList, stamp the source's state and separate required mark. Pending gets a cutout hourglass, passed a punched tick, failed a split crosspiece. Empty list reads “No check reported on this head”. Preserve missing requirements. | Choose Checks. Follow the authorized run, job or evidence link rendered there. A stale head gets a dated tag and requires a refresh before being described as current. [Checks](../../mr-pass/screen/Smithy/MergeRequestDetail/Checks.xml) · [State and URL construction](../../mr-pass/screen/Smithy/SmithyReview.js) |
| **4 · Follow-up** | Return to the issue to record the disposition or revisit its existing task in staff Work Items. Choose any different follow-up task deliberately, creating it in Projects only if separate work is needed. A visitor can stay in Smithy for the whole review. | Return to the same bench and selected request after leaving the real screen. The Proof Press can explain the evidence available to this viewer when asked. [Issues and work items](../../mr-pass/README.md) · [Staff work items](../../mr-boss/screen/Projects/WorkItems/List.xml) |

The signature prop works at rest. A missing binding leaves an engraved **Checks** tab that opens the page. A withheld record disappears from the private selection. A failed read shows the repair mark. A reported failed check is a successful data reading with a crosspiece. No run gets launched by touching a tag. T3 can ship all four screen openers. Live inserts need viewer-scoped PASS adapters, and direct opening of a particular record requires the page-resolved link for that selection.

### Four paper walks

| Person and route checked against the town plan | Result and access boundary |
| --- | --- |
| First-time visitor · Gatehouse 01 → square → west Counting house 06 → cashier | “My invoices” opens the chosen site's Billing page. Front pane withholds staff amounts. Without a signed-in eligible contact there is no invoice reading. Return lands at the writing slope. |
| Warehouse worker · square → east lane → landward entrance of 12 → working sling | With facility access, “waiting” and “late” are separate labels. Either opens the receipts list under its normal facility scope. Outside, no bale count moves. Without the Warehouse grant the street door stays locked. |
| Contributor · gate → square → west past Post loft 04 → Proof yard 16 → issue, proof, tags, follow-up | Issues and reviews respect repository features. If a check has never reported, the tag says so. Work item follow-up can remain in Smithy. The staff shortcut additionally needs Projects access. |
| Keyboard-only user · directory → Billing / My invoices, Warehouse / Receiving, or Projects / Smithy → chosen repository → Issues → Merge requests → Changes → Checks | Use a labeled link or Enter at each stop. Close restores focus at the launching control. In a new PASS tab, return to the still-open directory. Ask Proof Press is reachable beside Checks when available. The text twin announces the same restricted, empty or failed state. |

These are static walkthroughs against the design, owning screens and service code. The real return focus, scoped PASS adapters and executable walk grid will be verified when the village exists.
