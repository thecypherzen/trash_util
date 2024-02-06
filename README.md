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
- *Please not that the trash utility was written for, and tested only on ubuntu 20.04 and uses on other Linux distributions or shell environments may not work as expected. However, if it proves useful, we'd create versions suitable for other shells - but that would depend heavily on feedback.*

<br/>

## Installation and Usage ##
- Download or clone the repo to your device from [here](https://github.com/thecypherzen/trash_util). If you downloaded, extract the zip folder.
- `cd` into the `trash_util` or `trash_util-main` folder and run the `setup.run` file.
- If installation is successful or failed, the respective message would be printed to stdout. In event of success, simply restart your shell instance and proceed to use.
- In case of failure, the user would be directed to a link where there is a guide on how to uninstall and try again. *At the moment, this guide is not yet created, but in due course, it would. I might as well create an `unwind` utility that 'undos' changes made up to the point of failure - just maybe.*

<br/>

## Supported Commands Syntax & Usage ##
1.	To trash, delete or remove a file:
```
rm file_1 [file_2>] ...
or
rm /abs/path/to/file_1 [/rel/path/to/file_2] ...
```
2.	To restore a file back to location from where it was deleted(trashed):
```
restore file_1 [file_2] ...
or
restore /home/$USER/.trash/file_1 [~/.trash/file_2] ...
```
3.	To permanently delete a file from the system:
```
rmf file_1 [file_2] ...
or
rmf /abs/path/to/file_1 [/rel/path/to/file_2] ...
```

<br/>

## File Tree ##
trash_util(ROOT) <br/>
|_____ README.md <br/>
|_____ setup.run <br/>
|_____ .utilities <br/>
&emsp;&emsp;&emsp;|________ cron_scheduler <br/>
&emsp;&emsp;&emsp;|________ on_reboot_cron <br/>
&emsp;&emsp;&emsp;|________ restore <br/>
&emsp;&emsp;&emsp;|________ rmf <br/>
&emsp;&emsp;&emsp;|________ trash <br/>
&emsp;&emsp;&emsp;|________ trash_logr <br/>
&emsp;&emsp;&emsp;|________ trash_mgr <br/>
&emsp;&emsp;&emsp;|________ &lt;hidden files&gt;

<br/>

## Detailed File Description ##
Below is provided details of each file, what it does, usage and other necessary information, as may be useful to anyone who wishes to know more the ***utility***:
| SN | File | Description                                   |
|----|------|-----------------------------------------------|
| 1. | README.md | Project Readme file |
| 2. | [setup.run](https://github.com/thecypherzen/trash_util/blob/main/setup.run) | Installs the `trash` utility for the current user. </br> **Usage**: <br/> Execute`./setup.run` if you're  in the `trash_util` directory or `/path/to/setup.run` if you're elsewhere. <br/><br/>**What it does:**<ul><li>Creates a `.trash` folder in user's home directory.</li><li>Initialises the `trashlog` that keeps track of files in trash and their source directories.</li><li>Sets aliases for commands `rm`, `rmf`, `restore`, and environment variables `TRASH_UTIL_DIR` and `MAX_TTL`, which are needed for its functionlity.</li><li>Schedules two cronjobs: the first runs the `trash_mgr` every `7am, 10am and 4pm` daily by default. (*Currently, these values can only be changed from within the script itself)*.<br/>The second is scheduled to run on every reboot. Its job is to re-schedule the first cron, given that it is usually lost on each system shutdown. This means that three times daily, the `trash_mgr` checks for files that have overstayed their welcome and removes them from the `trash` accordingly. See *trash_mgr* description below for further details.</li></ul>|
| 3. | [.utilities](https://github.com/thecypherzen/trash_util/tree/main/.utilities) | The folder housing all utility scripts and the error log. See items 4-10 below.|
| 4. | [cron_scheduler](https://github.com/thecypherzen/trash_util/blob/main/.utilities/cron_scheduler) | A cronjob that schedules the `trash_mgr` utility to execute three times daily: 7am, 6am and 4pm. See the description of `trash_mgr` below.|
| 5. | [on_reboot_cron](https://github.com/thecypherzen/trash_util/blob/main/.utilities/on_reboot_cron) | A cronjob that is scheduled to run each time the system reboots. Its job is simply to execute the `cron_scheduler` described above. This is necessary as on each shutdown, the system loses track of all cronsjobs not directly controlled by the `cron` daemon.|
| 6. | [trash](https://github.com/thecypherzen/trash_util/blob/main/.utilities/trash) | A bash script that implements the `trash` functionality, to which the `rm` command is pointed. <br/><br/>**How it works:**<br/><ul><li>**Syntax:**<br/>`rm <file1> <file2> ...`<ul><li>The files passed can be directories or regular files. The utility is able to handle each properly.</li><li>At least one ***'file_name'*** must be passed as an argument.</li><li>arguments can be passed as either exact filenames, e.g. `rm file1 ...` or as absolute/relative paths, e.g: `rm /abs/path/to/file1 ~/path/to/file2 ...`</li></ul></li><li>For each argument passed, it:</li><ul><li>checks if it exists. If true, the argument(*which could be any file type*) is moved to the *trash*. Otherwise an error is flagged.</li><li>updates the ***'trash_log(fullpath: `/home/$USER/.trash`)'*** with the new file and its source directory.</li></ul><li>For each ***file_name*** that already exists in the ***trash***, a `.old` extension is added to the already trashed file. *When restoring the file, all `.old` extensions are stripped off*.</li></ul>|
| 7. | [restore](https://github.com/thecypherzen/trash_util/blob/main/.utilities/restore) | A bash script that ***'restores'*** a file in the ***'trash'*** folder to its original location(the folder from which it was `rm`ed.). <br/><br/>**How it works:**<br/><ul><li>**Syntax:**<br/>`restore <file1> <file2> ...`<ul><li>Just like ***'trash'*** above, the utility is able to handle any ***'file_type'*** properly, and</li><li>At least one(1) file_name must be passed to it as an argument.</li><li>arguments can be passed as either exact filenames, e.g. `restore file1 ...` or as absolute/relative paths to the trash, e.g: `restore /home/$USER/.trash/file1 ~/.trash/file2 ...`</li></ul></li><li>For each argument passed, it:</li><ul><li>checks if a matching file is in the *'trash'*. If false, an error is flagged. Else:<ul><li>if multiple files of same *'base_name'(i.e having only the `.old` extension as their difference) and 'source location'*, are found, the user is prompted with a list of from which to select the exact file to restore.</li></ul></li><li>moves the proper file from the ***'trash'*** to its appropriate ***'source'*** location.<ul><li>if file being restored has one or more `.old` extensions, they are all stripped and only the ***'base name'*** of the file is used. </li><li>if a file in the destination matches the name of the file being restored, the user is prompted and the case is handled accordingly.</li></ul></li><li>updates the *'trash_log'(fullpath: `/home/$USER/.trash`)* by removing the entry matching the restored file.</li></ul></ul>|
| 8. | [trash_logr](https://github.com/thecypherzen/trash_util/blob/main/.utilities/trash_logr) | A bash script that manages the `.trashlog` file in the trash. It is a utility script to the `trash`, `rm` and `rmf` scripts and basically does the following:<ul><li>adds newly `rm`ed files and their source locations to the log - usually the case when `rm` is used.</li><li>updates a file_name's entry in the log when a `.old` extension is added to it - usually the case when multiple files of same name were `rm`ed from the same location(directory)</li><li>deletes a file's entry in the log when it's moved from the trash either using `rmf` or `restore`.</li></ul>|
| 9. | [trash_mgr](https://github.com/thecypherzen/trash_util/blob/main/.utilities/trash_mgr) | A bash script that manages the trash, deleting files that have lived in the trash for up to, or more than the maximum time-to-live.<ul><li>the maximum time-to-live is defined by the environment variable `MAX_TTL` which is set to `30 days` except specified otherwise by the user either during installation or by a hard-reset of its value in the `/home/$USER/.bashrc` file.</li></ul>|
| 10. | [rmf](https://github.com/thecypherzen/trash_util/blob/main/.utilities/rmf) | A bash script that ***'permanently'*** deletes files or folders from any location, including the trash. It uses the `rm -f` command and flag.<br/><br/>**How it works:**<br/><ul><li>**Syntax:**<br/>`rmf <file1> <file2> ...`</li><li>Just like its counterparts, it knows how to handle files and folders alike in the proper way so no extra flags are needed.</li><li>It is also able to take in exact file names or absolute/relative file paths as arguments.</li><li>It updates the ***'trashlog'*** accordingly if file was `rmf`ed from the ***'trash'***.</li></ul>|
