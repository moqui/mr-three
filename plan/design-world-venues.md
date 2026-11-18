# Venues · places to meet outside Millrace

Millrace is where the work is. A venue is somewhere to go for a meeting: a small world with seats, a screen and a view, and nothing of the town in it. Four ship at first. Each is its own world, reached through a door, and each is an ordinary call with a place wrapped around it. [Worlds](design-technical.md#84-worlds) · [Meetings and sound](design-technical.md#14-audio-and-meetings)

A meeting room in a Millrace building is for a few people who are already there. A venue is for a meeting someone planned: more people, a presentation, a guest, or simply a better view.

## The four venues

| Venue | Seats | The place | Good for |
| --- | ---: | --- | --- |
| **Summit table** | 6–8 | A round stone table on a mountain summit above a floor of cloud, with a low cairn and one standing slab for a screen. The horizon curves. Wind moves a prayer-flag line and nothing else. | A decision among a few people |
| **Clock-face room** | 10–12 | The room behind the glass dial of a great clock tower at dusk. The numerals read backwards across the floor and a city's lights lie far below. One wall is a screen and the facing wall a board. | A team's weekly meeting, a review |
| **Great hall** | To the call's limit | A hammer-beam hall with long benches, a low stage and two big screens above it. Tall windows, banners in the organization's colors. | An all-hands, a training session |
| **Cliff theatre** | To the call's limit | An open-air theatre cut into a cliff over the sea. The stage has the horizon behind it and the dome above is the screen. | A launch, a public event |

**On well-known places.** The idea is a room somewhere impossible. Natural and ancient landmarks are free to draw: a named summit, the top of a pyramid, a watchtower on a great wall. Some modern landmarks carry image or trademark claims. The clock tower is therefore a generic great clock, and any later venue modeled on a modern building is a look-alike without its name.

Each venue fits the 2 MB an interior is allowed. The view is the inside of a small dome, as the town's sky is, so a venue costs a few pieces of furniture and one painted surface. In a plain pack the painting is a gradient with silhouettes.

A venue is a mod like any other ([mods](design-technical.md#17-mods)). An installation can add its own: its boardroom, its factory floor, the top of its own building.

## Getting in

- **From the town.** A meeting door whose room is bound to a venue takes the person there instead of into a building room. Leaving returns them to that door.
- **From anywhere else.** “Join in 3D” on the call window and on a calendar event's join action. Someone who prefers the ordinary call screen joins there and is in the same meeting.
- **Who may enter.** Anyone signed in who may join the room's call. A venue shows no app and no part of the town, so it does not need the town's grant. A guest who joined by link stays in the ordinary call screen and appears in the venue as a plaque, as every 2D participant does.
- **How many.** A call holds 50 today, in 2D and 3D together, and has no listen-only role. The hall and the theatre hold that many until calls gain an audience. Seats beyond the figures present stay empty chairs.

## Meeting furniture

The same pieces furnish a building's meeting room and a venue. Each reads the call or the event and none of them is the only way to learn what it shows.

| Piece | Shows | Source |
| --- | --- | --- |
| Roster plaques | A plaque at a seat for each participant who is not there as a figure | The call's participants |
| Big screen | What the presenter is sharing, described below | The call's screen-share track |
| Scribe's quill | A quill writing by itself at a small desk while the meeting is recorded or captioned. Still when it is not. Also a line in the HUD's sound panel. | The call's recording state |
| Agenda lectern | The event's page or description. Opens the real page. | The calendar event bound to the room |
| Sandglass | Sand running to the scheduled end, and a turned glass once it has passed | The event's end time |
| Speaking order | A row of pegs by the stage. Raising a hand hangs the person's token on the next peg. | New: a small list kept with the room for the length of the call |
| Notes desk | The meeting's notes page, opened for co-editing in the framed window | A Knowledge page bound to the room |
| Board wall | A shared canvas, later | See below |

## Big screens

Most meetings need one thing: everyone looking at the app the presenter is using. The call already carries a shared screen, so the venue shows it.

- **Presenting.** The presenter chooses Present and picks their own window, as in any call. They go on working in their framed window. Every screen place in the venue shows the shared picture.
- **Watching.** A screen on a wall is a sign. It tells the room what is being shown. Selecting it opens the same picture flat, framed and at full size, which is how anyone reads it. Text on a tilted surface across a hall is not legible and the design does not pretend it is.
- **Following.** The presenter may also offer the address of what they are showing. Each person who takes it opens that screen in their own window under their own access, sharp and live, and sees only what they are allowed to.
- **No presenter.** The screen shows the room's name and the agenda's first line, or whoever is speaking if they have a camera on.

## The board, later

A shared canvas is planned as another kind of wiki document, co-edited as pages are. When it exists, a board wall binds to one. A canvas can be painted straight onto the wall, which a page of an app cannot, so the wall is live: people in the room see strokes appear. Drawing happens flat in the framed window at first and directly on the wall once that is pleasant. Until then a board wall shows a notes page's title and opens it. What this needs from the canvas document is in the [technical design](design-technical.md#143-venues).

## In the directory

A venue reads as a short list: the room's name and state, who is present, what is on the screen with a link to open it, the agenda, the time left and the speaking order. Joining from the directory is joining the call.
