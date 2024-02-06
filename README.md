# Overview of trash_util #

***trash_util*** is a simple set of `bash` scripts that create a trash management (recycle bin) utility on the host system.

When working on Ubuntu sandboxes or virtual environments, the `rm` command has to be used super carefully as its sideffects are permanent and cannot be recalled. However as humans, errors are bound to be made, and if in a fast-paced environment like **ALX**, we are even more likely to delete files we didn't intend to - at least I am guilty of this.

That's why I created this utility, so that in that moment when it matters most, I won't have to rewrite lines of code or re-do work already done due to an erroneous use of the `rm`.

<br/>

## How it Works ##
- The `trash_util`, when installed, creates a hidden *'trash folder(`.trash`)'* in the user's home directory.
- It masks the `rm` command by setting an *alias* in the user's `.bashrc` or `.bash_profile` files, as the case may be. If neither exists, it creates a `.bashrc` file in the *user's home directory*. The alias links the `rm` command with the `trash` utility(script).
- This way, when `rm` is used on any file, just like when the default `rm` is used, instead of permanently deleting the file from the system, it is rather moved into the `trash` folder created during installation.
- The utility also introduces two more commands (aliases): `restore` and `rmf`. The command `restore` links to the `restore` utility(script) and it does what it sounds like - it restores any file in the *trash* back to the location from where it was removed.
	- The `trash_util` maintains a log in the *trash* that maps files to their source directories.
- The second command(alias) `rmf` allows the user to **permanently delete** a file or folder from any location on the system. It uses the `rm -f` command + flag to delete files or directories passed as arguments to it.
- Finally, the utility keeps track of files' time(in days) in the `trash` and deletes those which have lived up to, or more than the `MAX_TTL` environment variable set during installation. The user is usually prompted to set this value during installation. If a `0` or `non-integer value` is passed, the default of `30` days is used.
- It does its best to gracefully handle errors and where necessary, the user is shown the path to the `error log file` to see more details. If it's a developer issue, the user could send a email with the log message and it'll be fixed.
- It is my hope that you find this utility useful and helpful, and your feedback would be greatly appreciated. ENJOY!

<br/>

## Author ##
- [William Inyam](https://github.com/thecypherzen/)

<br/>

## Technologies ##
- All utilities are shell scripts written in GNU bash 5.0.17(1)-release (x86_64-pc-linux-gnu).
- Code tested **only** on Ubuntu 20.04 LTS.
- ````Please not that the trash utility was written for, and tested only on ubuntu 20.04 and uses on other Linux distributions or shell environments may not work as expected. However, if it proves useful, we'd create versions suitable for other shells - but that would depend heavily on feedback!```

<br/>

## Installation and Usage ##
- Download or clone the repo to your device from [here](https://github.com/thecypherzen/trash_util). If you downloaded, extract the zip folder.
- `cd` into the `trash_util` or `trash_util-main` folder and run the `setup.run` file.
- If installation is successful or failed, the respective message would be printed to stdout. In event of success, simply restart your shell instance and proceed to use.
- In case of failure, the user would be directed to a link where there is a guide on how to uninstall and try again. *At the moment, this guide is not yet created, but in due course, it would. I might as well create an `unwind` utility that 'undos' changes made up to the point of failure - just maybe.*

<br/>

## File Tree ##
trash_util(ROOT) <br/>
|_____ README.md <br/>
|_____ setup.run <br/>
|_____ .utilities <br/>
|________ cron_schedule <br/>
&nbsp;&nbsp;&nbsp;&nbsp;|________ on_reboot_cron <br/>
		|________ restore <br/>
		|________ rmf <br/>
		|________ trash <br/>
		|________ trash_logr <br/>
		|________ trash_mgr <br/>
		|________ <hidden files>

<br/>

## Detailed File Description ##
Below is provided details of each file, what it does, usage and other necessary information, as may be useful to anyone who wishes to know more the ***utility***
|----|------|-------------|
| 1. | README.md | Project Readme file |
| 2. | [setup.run](https://github.com/thecypherzen/trash_util/blob/main/setup.run) | Installs the `trash` utility for the current user. </br> **Usage**: `./setup.run` if in same directory as `trash_util` or `/path/to/setup.run` if you're elsewhere. </br>**What it does:**<ul><li>Creates a `.trash` folder in user's home directory.</li><li>Initialises the `trashlog` that keeps track of files in trash and their source directories.</li><li>Sets aliases for commands `rm`, `rmf`, `restore`, and environment variables `TRASH_UTIL_DIR` and `MAX_TTL`, which are needed for it's functionlity.</li><li>Schedules two cronjobs: the first runs the `trash_mgr` every `7am, 10am and 4pm` daily. The second is scheduled to run on every reboot. Its job is to re-schedules the first cron, given that it is usually lost on each system shutdown. This means that three times daily, the `trash_mgr` checks for files that have overstayed their welcome and removes them from the `trash` accordingly. See *trash_mgr* description below for further details.</li></ul>|
| 3. | [.utilities](https://github.com/thecypherzen/alx-higher_level_programming/tree/main/tests) | The folder containing all project tests. Contents are: <ol><li>[test_models](https://github.com/thecypherzen/alx-higher_level_programming/tree/main/tests/test_models): the folder containing tests corresponding to each module in the [models](https://github.com/thecypherzen/alx-higher_level_programming/tree/main/0x0C-python-almost_a_circle/models) folder in (2) above. They are listed below:<ol><li>[test_base.py](https://github.com/thecypherzen/alx-higher_level_programming/tree/main/tests/test_models/test_base.py): unittest for the [base.py](https://github.com/thecypherzen/alx-higher_level_programming/blob/main/0x0C-python-almost_a_circle/models/base.py) module.</li></ol></li></ol>|
