<p align="center">
	<a href="https://factorio.com/"><img src="https://cdn.factorio.com/assets/img/web/factorio-logo.png" width="460" height="76" border="0" /></a>
</p>

| Factorio v0.12                                                                                                                                                                                                   | Factorio v0.18                                                                                                                                                                                                    |
| ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| [![A concept of a factory for the trailer in v0.12](https://cdn.factorio.com/assets/img/blog/fff-353-05-factorio012-fullsize.png)](https://cdn.factorio.com/assets/img/blog/fff-353-05-factorio012-fullsize.png) | [![A concept of a factory for the trailer in v0.18 ](https://cdn.factorio.com/assets/img/blog/fff-353-04-factorio018-fullsize.png)](https://cdn.factorio.com/assets/img/blog/fff-353-04-factorio018-fullsize.png) |

[Screenshots of factory concepts](https://www.factorio.com/blog/post/fff-353) for the [Factorio 1.0 launch trailer](https://www.youtube.com/watch?v=BqaAjgpsoW8), showcasing the evolution of the game. The [Factorio blog](https://www.factorio.com/blog/) covers the process in detail.

# [factorio-save-upgrader](https://joelpurra.com/projects/factorio-save-upgrader/)

Tool to upgrade old [Factorio](https://factorio.com/) game save files to the newest version. Bring on the map nostalgia!

> ⚠ **Use this tool at your own risk. Backup your saves first.**  
> The save file format in Factorio [is pretty stable](https://gaming.stackexchange.com/questions/307514/have-saved-games-been-compatible-after-patches). Each [Factorio "major" version](https://wiki.factorio.com/Version_history) supports loading save games from one or more previous save file formats. The game will tell you if your save file is too old, otherwise you don't need to do anything.

The stepwise load/upgrade concept used in `factorio-save-upgrader`:

1. Loads (a copy of) the save file in the most recent Factorio version possible, stepwise trying older versions until one works.
1. Upgrades the saved map step-by-step to the most recent version available.
1. The upgraded save file is copied to the output directory.

```text
Loading
v2.0   v1.1   v1.0   v0.17  v0.16
❌ ---> ❌ ---> ❌ ---> ✅ ... ❓ ...
                       |
Upgrading              |
v2.0   v1.1   v1.0     |
✅ <--- ✅ <--- ✅ <-----
```

- Leaves up-to-date save files alone. Output is written with the same filename but to a different directory. Does not overwrite existing files.
- Can upgrade [all save files in the `./saves` directory](https://wiki.factorio.com/Application_directory) at once, or one by one.
- Does not require the full game to be installed. Save file can be transferred to a separate computer where upgrades are performed.
- Built [using](https://github.com/factoriotools/factorio-docker/issues/440) [Factorio Docker](https://github.com/factoriotools/factorio-docker) ([factoriotools/factorio-docker](https://hub.docker.com/r/factoriotools/factorio/)), originally intented for running dedicated game servers.

## Version compatibility

Not all [Factorio versions in the official archive](https://www.factorio.com/download/archive/) have (working) container images. Check the [issues for this tool](https://github.com/joelpurra/factorio-save-upgrader/issues?q=) as well as [issues for Factorio Docker](https://github.com/factoriotools/factorio-docker/issues?q=) for possible solutions.

Here is a table of last known status (although it is presumably constantly outdated) for the available Factorio Docker container image versions. Major game versions can also load _some_ older save file formats. For example, Factorio v0.17 also supports upgrading from v0.16 and v0.15.

| [Version](https://www.factorio.com/download/archive/) | Running | Upgrading from | [Space Age](https://factorio.com/game/content-space-age) |
| ----------------------------------------------------- | ------- | -------------- | -------------------------------------------------------- |
| v2.0                                                  | ✅      | ✅             | ✅                                                       |
| v1.1                                                  | ✅      | ✅             | —                                                        |
| v1.0                                                  | ✅      | ✅             | —                                                        |
| v0.17                                                 | ✅      | ✅             | —                                                        |
| v0.16                                                 | ❌      | ✅             | —                                                        |
| v0.15                                                 | ❌      | ✅             | —                                                        |
| v0.14                                                 | ❌      | ❓             | —                                                        |
| v0.13                                                 | ❌      | ❓             | —                                                        |
| v0.12                                                 | ❌      | ❓             | —                                                        |

Upgrading save files to Factorio v2.0+ requires the [Factorio: Space Age](https://factorio.com/game/content-space-age) expansion on the client. The expansion is automatically supported and enable in the save file "server-side" upgrade process, without any additional purchase.

## Limitations

- Factorio has [evolved over the years](https://wiki.factorio.com/Version_history), which may or may not require [manual factory fixes](https://www.factorio.com/blog/post/fff-187).
  - The "vanilla" base game [Factorio v2.0](https://factorio.com/blog/post/fff-418#2-0) included new rails, train control improvements, circuit network improvements, new fluid system, and much more.
  - Recipes, such as [oil processing](https://www.factorio.com/blog/post/fff-305), may change between versions.
- Requires [Factorio: Space Age](https://factorio.com/game/content-space-age) for v2.0+.
  - Factorio v2.0 uses mods for Factorio: Space Age (also for elevated rails, qualities) which are available in Factorio Docker.
  - The space age mods are enabled by default, thus all save files upgraded to v2.0+ _become_ space age save files.
  - Loading an upgraded v2.0+ save in "vanilla" base game Factorio _may work_, but with warnings regarding missing mods — and possibly factory issues.
  - Set `FACTORIO_VERSIONS='1.1 1.0'` and below to avoid upgrading to v2.0+.
- Mods configuration is not (yet) supported.
  - Require editing `./mods/mod-list.json` inside Factorio Docker.
  - May be used to upgrade "vanilla" base game Factorio 2.0 [by disabling space age](https://github.com/factoriotools/factorio-docker/issues/500#issuecomment-2426967550).
  - Can perhaps be used to upgrade save files [with third-party mods](https://mods.factorio.com/); may require additional mod files.
  - Pull requests, including documentation etcetera, welcome.
- Replay support is untested. Presumably [doesn't upgrade replays](https://wiki.factorio.com/Replay_system), since they are more tightly coupled to the version used for recording. Loading the map may still work, but replaying is not possible.
- The save preview (screenshot) is lost during automated upgrades. When saving using the full game it is regenerated.
- This tool doesn't inspect the `savegame.zip` files to try to figure out which upgrades might be necessary. Instead it brute-forces the solution by testing the save file against several major versions of Factorio until loading succeeds. The save file is then upgraded step-by-step through each subsequent major version.
- This tool doesn't detect _when_ the save file has been successfully upgraded. Instead it waits for 60 seconds (by default) during each step, and then assumes everything went well. Upgrading a large/complex map on a slow machine _may_ take even longer, potentially interrupting the process prematurely. Upgrading a small/simple map on a reasonably fast machine _only takes seconds_ though, so the extra wait may seem unneccessary. Adjust to your liking; see [usage](#usage).

## Requirements

- Creating backups of your save files _before_ attempting to upgrade them. You don't want to lose your _precious factory_, do you?
- A [Unix](https://en.wikipedia.org/wiki/Unix-like)/[Linux](https://en.wikipedia.org/wiki/Linux) system with common programs such as `bash`, `sed`, `find`, `sort`, `mktemp`, `realpath`.
- [Podman](https://podman.io/) (in rootless mode) to run the Factorio Docker container images.

## Installation

There's no real installation step. Just download the code somewhere and execute it in your terminal.

```shell
# NOTE: clone the git repository.
git clone https://github.com/joelpurra/factorio-save-upgrader.git

cd factorio-save-upgrader

# NOTE: either run directly from this directory...
./factorio-save-upgrader

# NOTE: ...or optionally symlink to your $PATH for ease of use.
ln --symbolic "${PWD}/factorio-save-upgrader" ~/bin/

factorio-save-upgrader
```

## Usage

Only successfully upgraded files are put in the output directory.

```shell
factorio-save-upgrader <output directory> <input directory or file(s)>
```

Examples assume loading saves from [Factorio's default user data directory location on a Linux system](https://wiki.factorio.com/Application_directory).

<details>
<summary>Basic examples</summary>

**Simple backup**

You can do better.

```shell
cp --recursive ~/.factorio/saves ~/factorio-saves-backup-$(date +%F)
```

**Upgrade all saves to another directory**

Afterwards, manually copy the upgraded save files from `~/factorio-upgraded-saves` to `~/.factorio/saves`.

```shell
mkdir --parents ~/factorio-upgraded-saves

factorio-save-upgrader ~/factorio-upgraded-saves ~/.factorio/saves
```

</details>

<details>
<summary>Advanced examples</summary>

Use one or more environment variables to change some settings; see the source code for details.

**Show verbose output**

```shell
FACTORIO_SAVE_UPGRADER_DEBUG_LEVEL='1' factorio-save-upgrader ~/factorio-upgraded-saves ~/.factorio/saves
```

**Shorter map upgrade timeout**

A shorter timeout can speed things up if you have many save files, but if your map is too complex or your machine too slow it might fail.

You can also increase the timeout, and connect to the game server to verify that the upgrade worked. Start Factorio and connect to address `localhost` from the multiplayer menu.

```shell
FACTORIO_TIMEOUT='15s' factorio-save-upgrader ~/factorio-upgraded-saves ~/.factorio/saves
```

**Limit Factorio versions**

Ensure versions are listed in descending order, with a single space between.

```shell
FACTORIO_VERSIONS='0.13 0.12' factorio-save-upgrader ~/factorio-upgraded-saves ~/.factorio/saves
```

</details>

<details>
<summary>Updating the Factorio Docker container images</summary>

Because this tool executes images in a loop, it doesn't spend time checking if there are any [updated images on Docker Hub](https://hub.docker.com/r/factoriotools/factorio/). You should perform updates manually if you haven't in a couple of weeks.

Advanced users may use custom images; see the source code for details.

**Pull updates for all local Factorio images**

```shell
podman image ls --format '{{.Repository}}:{{.Tag}}' --filter 'reference=factoriotools/factorio' | xargs --no-run-if-empty --max-lines='1' podman image pull
```

**Delete all local Factorio images to force re-download**

This tool does not keep data in images or persistent volumes. Be more careful if you use the images for anything else, such as actually running a game server.

```shell
podman image ls --format '{{.Repository}}:{{.Tag}}' --filter 'reference=factoriotools/factorio' | xargs --no-run-if-empty podman image rm
```

</details>

---

[factorio-save-upgrader](https://joelpurra.com/projects/factorio-save-upgrader/) Copyright &copy; 2022, 2023, 2024 [Joel Purra](https://joelpurra.com/). Released under [GNU General Public License version 3.0 (GPL-3.0)](https://www.gnu.org/licenses/gpl.html). [Your donations are appreciated!](https://joelpurra.com/donate/)
