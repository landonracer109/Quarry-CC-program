# Chunk Quarry (ComputerCraft 1.63, Minecraft 1.6.4)

A 16-turtle quarry that mines whole Minecraft chunks, one after another in a
straight line, all the way down to bedrock. A main computer runs the show
and draws a dashboard on a monitor; a service turtle unloads the miners and
refuels them, so nobody has to empty turtles by hand.

Built for the Tekkit modpack (ComputerCraft 1.63, advanced turtles). There's
a newer version that mines in rings around the base instead:
[Ring-Quarry-CC](https://github.com/landonracer109/Ring-Quarry-CC).

## How it works

- **16 mining turtles**, one per lane: together they cover a 16-block-wide
  chunk. Each digs its own lane 3 layers per pass, down to bedrock. When all
  16 finish, the whole fleet moves on to the next chunk.
- **Junk is thrown away** as they go (cobblestone, dirt, gravel, into a
  Trash Can each miner carries), so trips home are rare.
- **Going home**: when a miner is nearly full or low on fuel, it drives back
  to its start spot. The **service turtle** drives along the row behind the
  miners, pulls out their blocks into the output chest and fills them with
  fuel from the fuel chest.
- **Chunk loading**: lane 0 carries Spot Loaders and places one ahead for
  each new chunk, then picks them all back up at the end of the run (except
  the one by the start, which keeps home loaded).
- **Main computer**: starts and stops runs, moves the fleet from chunk to
  chunk, shows everything on the dashboard, and hands out program updates.
- **Survives server restarts and crashes**: everything saves its state
  before every move and carries on after a restart by itself.
- **Light on the server**: the whole quarry sends about 0.5 radio messages a
  second while mining (each turtle in its own time slot), and nothing at all
  between runs.
- **Turtles never attack** (they can't tell players from mobs).

## What you need

- 17 **advanced turtles** with **wireless modems** (16 miners, 1 service),
  each with a pickaxe.
- 1 **computer** with a **wireless modem** (the main computer), plus an
  **Advanced Monitor** (6 wide x 5 tall or bigger) connected to it with wired
  modems and networking cable.
- A **disk drive** and a **floppy disk** to install the turtles.
- Two **chests** (fuel and output) and one solid "marker" block.
- For each miner: 1 cobblestone, 1 dirt, 1 gravel and an **Extra Utilities
  Trash Can**.
- **Spot Loaders**: one per chunk you want to mine.
- Fuel: **coal blocks** are best (800 fuel each).
- A `get <url> <file>` program (a small HTTP downloader) on the floppy.

## Layout

Stand behind the miners, looking the way they will dig:

```
   lane 0  lane 1  ...  lane 15      <- miners in a flush row, all facing forward
 S  .       .           .            <- home row: the service turtle drives along here
```

- **Miners**: 16 in a row, all facing the digging direction. **Lane 0 is
  the leftmost.** Line the row up with a Minecraft chunk (F3 shows chunk
  borders): lane 0 on the chunk's first column, the row on its first block.
- **Service turtle home**: one block **behind** lane 0 and one block to its
  **left**, facing the same way as the miners.
  - **Output chest** directly **above** it.
  - **Fuel chest** directly **below** it (only fuel, one kind).
  - A **solid block** directly **in front** of it (required: it checks its
    facing against it).
- Keep the home row behind the miners clear.
- The home area (base, chests, main computer) must stay chunk-loaded.

**Radio range**: a wireless modem at ground level reaches only about 64
blocks, which is less than 4 chunks. Put the main computer **high up** (a
pillar at y 200+), where the range grows to several hundred blocks, and run
networking cable down to the monitor.

## Installing

1. **Main computer** (with its wireless modem and the monitor attached):
   ```
   get https://raw.githubusercontent.com/landonracer109/Quarry-CC-program/main/ControlCPU c
   c install
   c update
   ```
   `c install` makes it start by itself on every boot. `c update` downloads
   the turtle programs it hands out. Keep `get` on this computer too.
   Reboot it (hold Ctrl+R).
2. **Floppy**: put the floppy in the disk drive and download the installer:
   ```
   get https://raw.githubusercontent.com/landonracer109/Quarry-CC-program/main/Join disk/join
   ```
3. **Service turtle**: put it on its home spot, fuel it, put the floppy drive
   next to it and run:
   ```
   disk/join service
   ```
4. **Miners**: for each one, fill slots 12, 13, 14 with 1 cobblestone,
   1 dirt, 1 gravel, slot 15 with the Trash Can, and (lane 0 only) Spot
   Loaders in slot 16. Then run, with its own lane number:
   ```
   disk/join miner 0
   ```
   `join` makes the turtle start by itself on every boot and fetch the
   newest program from the main computer each time.
5. On the dashboard, check that all 16 lanes and the service turtle show
   up, set the number of chunks with **SET**, and press **START**.

Branch links (`/main/`) can be up to 5 minutes out of date right after a
change. For an exact version, replace `main` with a commit hash from the
repo's history.

## Using it

Dashboard buttons (bottom row of the monitor), or keys on the main computer:

| Button | Key | What it does |
|---|---|---|
| START | Enter | start a run (once everyone has registered) |
| STOP | S | lanes finish their current pass and go home (touch twice) |
| SET | C | how many chunks to mine, and how many are already done |
| UPD | U | download the newest programs from GitHub; they install between runs and every turtle reboots onto them (touch twice) |
| LOG | L | every turtle uploads its debug log; the main computer shows one link to all of them |

- Each run carries on after the chunks mined in earlier runs.
- The **VER** column shows each turtle's program version: red means it
  hasn't taken the newest update yet.
- If a turtle says **LOST**, put it on its start spot and run
  `m <lane> here` (or `s here` for the service turtle).

## Programs

| File | In game | What |
|---|---|---|
| ControlCPU | `c` | main computer: dashboard, run control, update server |
| TurtleTest1 | `m <lane>` | mining turtle |
| ServiceTurtle | `s` | service turtle |
| Join | `disk/join` | installer and boot loader for turtles |
| Push | `push <file>` | uploads files and prints a link to share them |
| NetMon | `netmon` | listen-only live count of radio messages (adds no load) |
| RangeTest | `rangetest` | checks how far a wireless modem reaches |

Turtles get their programs from the main computer, so only the main
computer ever downloads from GitHub.
