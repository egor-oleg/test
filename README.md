# A.E.G.I.S autocraft for CC:Tweaked

A.E.G.I.S. is a CC:Tweaked autocraft in plain Lua. The first version was really basic — no dependency tree, no scheduler, just pulling ready components from a chest for one-level crafts. But I got so hooked on the process itself that after half a year of endless tweaking and feature creep the project grew way bigger than I'd originally planned — kind of a *factory core in a single computer*. 12k lines in and I'm still poking at it. Ah right — the factory must grow.

<p align="center"><a href="./images/2026-08-09-20-23-25.png"><img src="./images/2026-08-09-20-23-25.png" width="720"></a></p>

When you request an item, A.E.G.I.S. keeps the taught recipes on disk, walks the dependency tree, checks what's already sitting in your vaults, and sends the missing ingredients to whichever free machines it knows about.

Teaching is interactive: drop the ingredients into T.BOX (3x3 for turtle crafts, or just place them for a machine recipe), pick the machine, hit SCAN. That's it — recipe is saved. Next time you order that item, the system walks the dependencies itself and crafts everything up to the final product. More details in the [Teaching recipes](#teaching-recipes-recipes-tab) section.

How well a specific machine works comes down to what the mod exposes to the CC:Tweaked peripheral API — the system only ever sees what the peripheral tells it. See [Known limitations](#known-limitations).

---

## Requirements

- **Advanced Computer** — must be placed directly to the left of the monitor wall.
- **Advanced Monitors** — 5x3 or larger is comfortable, other layouts work too.
- **Crafty Turtle** — Advanced Turtle running `turtle/startup.lua`, used for 3x3 grid recipes.
- **Storage** — connected chests, vaults, drawers or tanks.
- **T.BOX** — one barrel or chest used as staging area for teaching recipes and item delivery.
- **Wired network** — modems on every machine/chest, connected by cable, activated (red ring on).

---

## Quick setup

| Interface | Steps |
| :--- | :--- |
| <a href="/images/2026-08-09-20-36-05.png"><img src="/images/2026-08-09-20-36-05.png" width="380" /></a> | **Install & tag**<br><br>Drop `computer/startup.lua` onto the Advanced Computer, `turtle/startup.lua` onto the turtle, then `reboot`. On the monitor, open the **NETWORK** tab and tag your peripherals: storages → `[VAULT]`, staging barrel → `[T.BOX]`, crafting turtles → `[TURTLE]`, fluid tanks → `[TNK]` (optional). |

---

## Teaching recipes (+RECIPES tab)

| Interface & Setup | Step-by-Step Instructions |
| :--- | :--- |
| <a href="/images/2026-08-09-20-26-45.png"><img src="/images/2026-08-09-20-26-45.png" width="380" /></a> | **3x3 grid recipe**<br><br>Lay ingredients in the 3x3 center of T.BOX, then `+RECIPES → TURTLE → SCAN`. |
| <a href="/images/2026-08-09-20-27-09.png"><img src="/images/2026-08-09-20-27-09.png" width="380" /></a> | **Machine recipe**<br><br>Place ingredients in T.BOX, select the machine on the computer, then `+RECIPES → MACHINES → SCAN`. |
| <a href="/images/2026-08-09-20-30-35.png"><img src="/images/2026-08-09-20-30-35.png" width="380" /></a> | **Fluid / hybrid recipe**<br><br>Solid items go into T.BOX, fluid type and mB volume are typed on the computer, then `+RECIPES → FLUID → SCAN`. |
| <a href="/images/2026-08-09-20-32-34.png"><img src="/images/2026-08-09-20-32-34.png" width="380" /></a> | **Alternatives (+ALT)**<br><br>If an item already has a recipe, you can add another one as `+ALT`. At craft time the alts are checked top to bottom, first one that matches the stock is picked. |
| <a href="/images/2026-08-09-20-34-39.png"><img src="/images/2026-08-09-20-34-39.png" width="380" /></a> | **Editing**<br><br>`[E]` in the recipes tab lets you swap the machine for a recipe, or reassign a whole machine tag across every saved recipe at once. |

---

## Autocrafting

| Interface | Description |
| :--- | :--- |
| <a href="/images/2026-08-09-20-48-33.png"><img src="/images/2026-08-09-20-48-33.png" width="380" /></a> | **Parallel execution**<br><br>When you request an item, A.E.G.I.S. first looks at what's already in storage, then goes after the missing ingredients. Parts of the job that don't depend on each other can run at the same time if there are free machines. The header shows `parallel X` for active sub-tasks. |
| <a href="/images/2026-08-09-20-48-13.png"><img src="/images/2026-08-09-20-48-13.png" width="380" /></a> | **Multi-machine batching (`pm3`)**<br><br>For recipes with probabilistic outputs the system just keeps feeding ingredients until the requested count is reached. `pm3` on a task means 3 machines are working on it together. |
| <a href="/images/2026-08-09-20-50-54.png"><img src="/images/2026-08-09-20-50-54.png" width="380" /></a> | **Queue**<br><br>You can queue several crafts at once. If one runs out of resources it gets a warning, stays in the queue, and the next order starts. |
| <a href="/images/2026-08-09-21-13-50.png"><img src="/images/2026-08-09-21-13-50.png" width="380" /></a> | **Tools**<br><br>GT-style durability tools (saws, hammers, files) are supported. If a tool breaks mid-craft, A.E.G.I.S. crafts a replacement first, then resumes the parent job. |
| <a href="/images/2026-08-09-20-44-09.png"><img src="/images/2026-08-09-20-44-09.png" width="380" /></a> | **Cancel / cleanup**<br><br>Output slots are cleared before a sub-task and again on finish. Pressing `CANCEL` stops the current craft and dumps output slots to vaults. It can't always pull from input slots though — some mods block that (see limitations). |

---

## Machines and groups

| Interface | Description |
| :--- | :--- |
| <a href="./images/2026-08-09-20-37-29.png"><img src="./images/2026-08-09-20-37-29.png" width="380" /></a> | **Auto-grouping**<br><br>Machines that share a peripheral name auto-form a pool, so you don't have to configure anything for the usual case. |
| <a href="./images/2026-08-09-20-39-38.png"><img src="./images/2026-08-09-20-39-38.png" width="380" /></a> | **Custom groups**<br><br>You can also group machines manually — e.g. split Create Depots into `Depot Forge` vs `Depot Press`. If you add a machine to a custom group, exclude it from auto-grouping, otherwise it ends up in both. |
| <a href="./images/2026-08-09-20-25-28.png"><img src="./images/2026-08-09-20-25-28.png" width="380" /></a> | **Exclusions & suffixes**<br><br>Individual machines can be excluded from a pool, or given a display suffix (`SUF`) in the NETWORK tab without breaking pooling. |
| <a href="/images/2026-08-09-20-20-44.png"><img src="/images/2026-08-09-20-20-44.png" width="380" /></a> | **Multiblocks**<br><br>Multiblocks with separate input and output hatches are supported. |

---

## Fluids

| Interface | Description |
| :--- | :--- |
| <a href="/images/2026-08-09-20-16-47.png"><img src="/images/2026-08-09-20-16-47.png" width="380" /></a> | **Hybrid & sequential pouring**<br><br>Solid+fluid recipes work, mB is tracked, and multiple fluids can be poured in sequence. For machines with more than one fluid hatch, route the fluids through transfer tanks with hatches attached. |
| <a href="/images/2026-08-09-20-07-57.png"><img src="/images/2026-08-09-20-07-57.png" width="380" /></a> | **Fluid stock & OPTIMIZE**<br><br>The stock view shows how much of each fluid is in which tank. `OPTIMIZE` scans, merges and defrags fluid storage. |
| <a href="/images/2026-08-09-20-06-57.png"><img src="/images/2026-08-09-20-06-57.png" width="380" /></a> | **KEEP-FLUID**<br><br>`KEEP-FLUID` sets an automated mB buffer for a fluid, similar to KEEP for items. |

---

## KEEP (auto-stocking)

| Interface | Description |
| :--- | :--- |
| <a href="./images/2026-08-09-20-05-35.png"><img src="./images/2026-08-09-20-05-35.png" width="380" /></a> | **Threshold → target**<br><br>KEEP uses two numbers per item: a threshold and a target. Crafting starts only when stock drops below the threshold and stops once it reaches the target. So the system doesn't re-trigger a craft after every single item consumed. |
| <a href="/images/2026-08-09-20-51-53.png"><img src="/images/2026-08-09-20-51-53.png" width="380" /></a> | **Priority order**<br><br>KEEP entries run in the order you set them — higher entries first. |
| <a href="/images/2026-08-09-21-00-11.png"><img src="/images/2026-08-09-21-00-11.png" width="380" /></a> | **Idle execution**<br><br>KEEP runs automatically when the system has been idle for ~3 minutes and no manual craft is active. Can be paused at any time. |

---

## Storage

| Interface | Description |
| :--- | :--- |
| <a href="/images/2026-08-09-20-04-36.png"><img src="/images/2026-08-09-20-04-36.png" width="380" /></a> | **STOCK & request**<br><br>`STOCK` gives one unified view of everything in the connected vaults. From there you can request items straight into free T.BOX slots. |
| <a href="/images/2026-08-09-20-03-47.png"><img src="/images/2026-08-09-20-03-47.png" width="380" /></a> | **OPTIMIZE**<br><br>`OPTIMIZE` on the stock view walks every vault and merges unstacked stacks of the same item. |
| <a href="/images/2026-08-09-20-02-54.png"><img src="/images/2026-08-09-20-02-54.png" width="380" /></a> | **LOGICK transfer rules**<br><br>LOGICK replaces item/fluid pipes with rule-based transfers. Rules can be `Standard`, `Provider` (pull-only) or conditional `IF` like *move wood if charcoal < 32*. |

---

## Remote and integrations

| Interface | Description |
| :--- | :--- |
| <a href="/images/2026-08-09-19-51-42.png"><img src="/images/2026-08-09-19-51-42.png" width="380" /></a> | **RedNet remote**<br><br>Run `remote_control` on a pocket computer with an Ender Modem to order items and follow live progress from anywhere in loaded chunks. |
| <a href="/images/2026-08-09-19-51-12.png"><img src="/images/2026-08-09-19-51-12.png" width="380" /></a> | **GitHub sync**<br><br>Recipe library can be exported/imported via a GitHub Personal Access Token. The token is masked in the UI and dropped from RAM on reboot. |
| <a href="/images/2026-08-09-19-50-38.png"><img src="/images/2026-08-09-19-50-38.png" width="380" /></a> | **ntfy push**<br><br>Optional push notifications to your phone when a craft finishes. |

---

## Known limitations

- Some mods don't expose machine input/output inventories through the CC:Tweaked peripheral API. For those you can usually put a chest between the machine and the network — [guide](https://www.reddit.com/r/ComputerCraft/comments/1uyx4td/comment/oy8femh/?utm_source=share&utm_medium=web3x&utm_name=web3xcss&utm_term=1&utm_content=share_button).
- Some mods let you push into an input slot but not pull out of it. `CANCEL` can't always recover items from those input slots — check them by hand after a cancel.
- Multiblocks may need transfer chests or separate input/output hatches, depending on how the mod wires them up.
- CC:Tweaked computers have a 1 MB filesystem cap. If new recipes stop saving or vanish on reboot, the computer folder is full — delete old `.BAK` files.
- Solid-fuel furnaces need a specific setup — [guide](https://www.reddit.com/r/ComputerCraft/comments/1uyx4td/comment/oyb7z51/?utm_source=share&utm_medium=web3x&utm_name=web3xcss&utm_term=1&utm_content=share_button).
- Machines must stay chunk-loaded for a job to keep running.
- The Advanced Computer must sit directly to the left of the monitor wall — the layout code assumes this.
- Keep an eye on free vault and tank space at the top of the monitor. If there's nowhere to unload, collection tasks stall.

| Reference shot | Reference shot |
| :---: | :---: |
| <a href="/images/2026-08-09-21-08-50.png"><img src="/images/2026-08-09-21-08-50.png" width="380" /></a> | <a href="/images/2026-08-09-20-57-35.png"><img src="/images/2026-08-09-20-57-35.png" width="380" /></a> |

In practice, most machines from most mods should work with Modern Industrialization / Create / Greg_th / PneumaticCraft, etc. For some mods, such as Mekanism, which don't have integration with CC, you'll need workarounds — for example, chests for transferring items.

---

Community & support: https://www.reddit.com/r/ComputerCraft/comments/1uyx4td/aegis_autocraft_for_computercraft_cctweaked/

Setup & guide video: https://youtu.be/mhVkOqG2S1A?si=LeTGJ9jsijtcVPoM
