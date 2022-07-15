<p align="center">
	<a href="https://factorio.com/"><img src="https://cdn.factorio.com/assets/img/web/factorio-logo.png" width="460" height="76" border="0" /></a>
</p>

| Factorio 0.12                                                                                                                                                                                                   | Factorio 0.18                                                                                                                                                                                                    |
| --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| [![A concept of a factory for the trailer in 0.12](https://cdn.factorio.com/assets/img/blog/fff-353-05-factorio012-fullsize.png)](https://cdn.factorio.com/assets/img/blog/fff-353-05-factorio012-fullsize.png) | [![A concept of a factory for the trailer in 0.18 ](https://cdn.factorio.com/assets/img/blog/fff-353-04-factorio018-fullsize.png)](https://cdn.factorio.com/assets/img/blog/fff-353-04-factorio018-fullsize.png) |

[Screenshots of factory concepts](https://www.factorio.com/blog/post/fff-353) for the [Factorio 1.0 launch trailer](https://www.youtube.com/watch?v=BqaAjgpsoW8), showcasing the evolution of the game. The [Factorio blog](https://www.factorio.com/blog/) covers the process in detail.

# [factorio-save-upgrader](https://joelpurra.com/projects/factorio-save-upgrader/)

Tool to upgrade old [Factorio](https://factorio.com/) game save files to the newest version. Bring on the map nostalgia!

> ⚠ **Use this tool at your own risk. Backup your saves first.**  
> The save file format in Factorio [is pretty stable](https://gaming.stackexchange.com/questions/307514/have-saved-games-been-compatible-after-patches). Each [Factorio "major" version](https://wiki.factorio.com/Version_history) supports loading save games from one or more previous save file formats. The game will tell you if your save file is too old, otherwise you don't need to do anything.

- Loads saves using images from [Factorio Docker](https://github.com/factoriotools/factorio-docker) ([factoriotools/factorio-docker](https://hub.docker.com/r/factoriotools/factorio/)), originally intented for running dedicated game servers.
- Performs a stepwise upgrade, from the oldest compatible version to the newest available.
- Can upgrade [all save files in the `./saves` directory](https://wiki.factorio.com/Application_directory) at once, or one by one.
- Does not overwrite existing files. Output is written with the same filename but to a different directory.
- Leaves up-to-date save files alone.
- Does not require the full game to be installed. Save file can be transferred to a separate computer where upgrades are performed.

## Version compatibility

Not all Factorio versions have (working) Docker images. Check the [issues for this tool](https://github.com/joelpurra/factorio-save-upgrader/issues?q=) as well as [issues for Factorio Docker](https://github.com/factoriotools/factorio-docker/issues?q=) for possible solutions.

Here is a table of last known status (although it is presumably constantly outdated) for the available Factorio Docker image versions. Major game versions can also load _some_ older save file formats. For example, Factorio 0.17 also supports upgrading from 0.16 and 0.15.

| Version | Running | Upgrading from |
| ------- | ------- | -------------- |
| 1.1     | ✅      | ✅             |
| 1.0     | ✅      | ✅             |
| 0.18    | ✅      | ✅             |
| 0.17    | ✅      | ✅             |
| 0.16    | ❌      | ✅             |
| 0.15    | ❌      | ✅             |
| 0.14    | ❌      | ❓             |
| 0.13    | ❌      | ❓             |
| 0.12    | ❌      | ❓             |

Older versions are not supported, unless someone publishes a compatible Docker image.

## Limitations

- Factorio has evolved over the years. For example some recipes, such as [oil processing](https://www.factorio.com/blog/post/fff-305), have changed so your factory may not run very well without manual fixes.
- Only upgrading vanilla Factorio has been tested. For this reason, upgrading save files [with mods](https://mods.factorio.com/) are not officially supported by this tool.
- Replay support is untested. Presumably [doesn't upgrade replays](https://wiki.factorio.com/Replay_system), since they are more tightly coupled to the version used for recording. Loading the map may still work, but replaying is not possible.
- The save preview (screenshot) is lost during automated upgrades. When saving using the full game it is regenerated.
- This tool doesn't inspect the `savegame.zip` files to try to figure out which upgrades might be necessary. Instead it brute-forces the solution by testing the save file against several major versions of Factorio until loading succeeds. The save file is then upgraded step-by-step through each subsequent major version.
- This tool doesn't detect _when_ the save file has been successfully upgraded. Instead it waits for 60 seconds (by default) during each step, and then assumes everything went well. Upgrading a large/complex map on a slow machine _may_ take even longer, potentially interrupting the process prematurely. Upgrading a small/simple map on a reasonably fast machine _only takes seconds_ though, so the extra wait may seem unneccessary. Adjust to your liking; see [usage](#usage).

## Requirements

- Creating backups of your save files _before_ attempting to upgrade them. You don't want to lose your _precious factory_, do you?
- A [Unix](https://en.wikipedia.org/wiki/Unix-like)/[Linux](https://en.wikipedia.org/wiki/Linux) system with common programs such as `bash`, `sed`, `find`, `sort`, `mktemp`, `realpath`.
- [Docker](https://docker.com/) to run the Factorio Docker images.
  - Does not require `root` access if your user belongs to the `docker` group.
  - Similar tools, like [Podman](https://podman.io/), should also work.

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

## Basic examples

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

### Advanced examples

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

## Updating the Factorio Docker images

Because this tool executes images in a loop, it doesn't spend time checking if there are any [updated images on Docker Hub](https://hub.docker.com/r/factoriotools/factorio/). You should perform updates manually if you haven't in a couple of weeks.

Advanced users may use custom images; see the source code for details.

**Pull updates for all local Factorio images**

```shell
docker image ls --format '{{.Repository}}:{{.Tag}}' --filter 'reference=factoriotools/factorio' | xargs --max-lines=1 docker pull
```

**Delete all local Factorio images to force re-download**

This tool does not keep data in images or persistent volumes. Be more careful if you use the images for anything else, such as actually running a game server.

```shell
docker image rm $(docker image ls --format '{{.Repository}}:{{.Tag}}' --filter 'reference=factoriotools/factorio')
```

---

[factorio-save-upgrader](https://joelpurra.com/projects/factorio-save-upgrader/) Copyright &copy; 2022 [Joel Purra](https://joelpurra.com/). Released under [GNU General Public License version 3.0 (GPL-3.0)](https://www.gnu.org/licenses/gpl.html). [Your donations are appreciated!](https://joelpurra.com/donate/)
