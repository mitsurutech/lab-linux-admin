# Linux administration lab: users, permissions, cron and systemd in one Ubuntu VM

[![check](https://github.com/mitsurutech/lab-linux-admin/actions/workflows/check.yml/badge.svg)](https://github.com/mitsurutech/lab-linux-admin/actions/workflows/check.yml)

A ready-made **Linux administration lab** for classrooms, from the [MitsuruTech Learning Platform](https://mitsurutech.co.uk/learning), where schools and colleges give every student their own Ubuntu machine and a terminal in the browser. The student is the administrator of a small server and does four jobs a first-year sysadmin does: **add a user and a group**, **set the permissions of a shared folder**, **schedule a job with cron** and **write and enable a systemd service**. Nothing is installed on the student's computer, and the lab is marked by a script, so a teacher does not have to read each machine.

## The four tasks

| Task | What the student does |
| --- | --- |
| 1. A user and a group | Make an ordinary group `devs` and an ordinary user `maria` with a home folder and bash, in that group and not an administrator |
| 2. A shared folder | Make `/srv/projects` the team's folder: group members can read, write and enter it, files made in it belong to the group automatically, and nobody else has any access |
| 3. A scheduled job | Run the lab's `backup.sh` as root every day at 02:30 from a file in `/etc/cron.d`, written the way cron needs it |
| 4. A service | Write a systemd unit file for the lab's `heartbeat.sh`, restarted if it fails, and enable it so it starts at every boot |

The lab leaves a README in the student's home folder that says what to do and gives none of the answers: no commands, no modes, no unit file. It is deliberately small, so it fits a first lesson on **users and permissions, cron and systemd** and can be extended with disks, LVM or a firewall by copying the file.

## How it is marked

The `check` script runs as root on a copy of the saved machine after a fresh start, so it sees only what was **saved**, never a command that was typed and lost at the restart. It goes through the four tasks in order and stops at the first one that is wrong, and its first line says which task and why, for example `NOT PASSED: task 3: the user field is /usr/local/bin/backup.sh, it must be root`. It reads real state: the user and the group in the account database, the folder's owner and mode, and a real file created by the new user and by somebody outside the group; the cron file's owner, permissions, ending and its one job line; the unit file's `ExecStart`, `Restart` and `WantedBy`, and the link that `systemctl enable` makes. A user crontab, a folder opened wide with `chmod 777`, a service that was started but never enabled, and a copy of the unit file in place of the link all fail.

## What is in the file

`linux-admin.lab.yaml` is a whole **VM lab definition**, exactly what the platform runs:

| Key | What it is |
| --- | --- |
| `cpus`, `memory`, `disk` | the size of each student's VM: 1 CPU, 1024 MB and 10 GB |
| `open` | the ports that open in the browser: none, the lab is the terminal |
| `setup` | a script run once, as root, the first time each student's VM starts: it installs `cron` from Ubuntu's archive, creates the two small scripts and the shared folder, and writes the lab's README |
| `check` | the marking script described above; its exit status is the result and `0` passes |

## Using it on the platform

**To set it as work for a class:**

1. In your module, add a task and choose the **Ubuntu VM lab** card.
2. Press the sample, or paste the contents of `linux-admin.lab.yaml`. Using a sample replaces the text, and Undo brings yours back.
3. Publish and try your own copy first. Every student gets a VM of their own, kept between sessions.
4. Assign it like any task. When it is handed in, run the check on a student's row in the class list: it starts a copy of their saved machine that is not saved back, and shows whether it passed and what it printed.

The help manual's [page for this lab](https://mitsurutech.co.uk/help/lab-linux-admin), its [guide to Ubuntu VM labs](https://mitsurutech.co.uk/help/labs) and its [sample labs page](https://mitsurutech.co.uk/help/sample-labs) cover the rest. No college account yet? [Your first year is free](https://mitsurutech.co.uk/pricing) for up to 50 students, and [signing up](https://mitsurutech.co.uk/signup) takes an email address.

## Checking the file

On every push, `.github/workflows/check.yml` checks that the file is valid YAML with the keys above and that `setup` and `check` are each inside the platform's 16 KB limit. It does not boot a VM: that is done on the platform's own machines.

## What does not work in a lab

The VM reaches Ubuntu's package archive, PyPI, npm and GitHub, and nothing else, so a lab cannot be pointed at a host on the internet. It is one VM, so there is no second machine to administer over a network.

## About

Made by [Mitsuru Technologies](https://mitsurutech.co.uk) for the [MitsuruTech Learning Platform](https://mitsurutech.co.uk/learning): Python, Java, Go, Node, SQL and Linux in the browser for schools and colleges, with container labs, VM labs, web apps deployed from GitHub, and quizzes that mark themselves. Try code with no account in the [playground](https://mitsurutech.co.uk/playground).

More labs from the same place: [lab-netlab](https://github.com/mitsurutech/lab-netlab), [lab-juice-shop](https://github.com/mitsurutech/lab-juice-shop), [lab-dvwa](https://github.com/mitsurutech/lab-dvwa), [lab-webgoat](https://github.com/mitsurutech/lab-webgoat), [lab-mysql-phpmyadmin](https://github.com/mitsurutech/lab-mysql-phpmyadmin), [lab-git-gitea](https://github.com/mitsurutech/lab-git-gitea) and [lab-sample](https://github.com/mitsurutech/lab-sample).

## Licence

The lab definition and this documentation are MIT licensed, see [LICENSE](LICENSE): copy them, change them, teach with them. `cron` is installed from Ubuntu's archive when a VM starts; nothing of it is distributed here.
