# Linux Scheduled Tasks

## What Is a Scheduled Task?

A **scheduled task** runs automatically at a specific time or interval, rather than being launched manually. On Linux these are almost always called **cron jobs**, managed by the **cron** service.

## Listing and Editing Cron Jobs

```bash
crontab -l    # list the current user's scheduled tasks (-l = list)
crontab -e    # open the current user's crontab in an editor (-e = edit)
```

Lines starting with `#` in a crontab are comments and don't get executed.

## Crontab Syntax: Five Time Fields + a Command

```text
Minute  Hour  Day-of-Month  Month  Day-of-Week  Command
```

| Field | Allowed Values | Meaning |
|---|---|---|
| Minute | `0-59` | Minute of the hour |
| Hour | `0-23` | Hour of the day |
| Day of month | `1-31` | Day of the month |
| Month | `1-12` | Month |
| Day of week | `0-7` | Day of week; `0` **and** `7` both mean Sunday |

`*` in any field means "every possible value" for that field. For example:

```text
30 08 * * * /home/pete/scripts/change_wallpaper
```

runs that script every day at 08:30. And a step value works too:

```text
*/5 * * * * echo "Hello Analyst" >> /home/debian/hello.txt
```

runs every 5 minutes.

## Disabling a Cron Job

Either comment it out (add `#` at the start of the line) or delete the line outright:

```text
# */5 * * * * /home/debian/backup.sh
```

## Cron Schedule Examples

> ⚠️ **Correction:** the original notes had a stray leading digit at the start of each example line (`1 4 0 * * 1-5 ...`, `2 0 4 * * 1-5 ...`, etc.) — one extra field beyond the standard five, which isn't valid cron syntax on its own. Those leading digits look like they were meant as list numbering (1., 2., 3., ...) that got merged into the code block. The corrected, valid five-field versions are below.

```text
4 0 * * 1-5      /home/debian/backup.sh    → 00:04, Monday–Friday
0 4 * * 1-5      /home/debian/backup.sh    → 04:00, Monday–Friday
0 7 * * 2        /home/debian/backup.sh    → 07:00, every Tuesday
*/5 * * * *      /home/debian/backup.sh    → every 5 minutes, every day
0 * * 4 0        /home/debian/backup.sh    → top of every hour, every Sunday in April
0 * * * *        /home/debian/backup.sh    → top of every hour, every day
```

Each line follows the same `Minute Hour Day-of-Month Month Day-of-Week Command` structure — the same command can be scheduled very differently just by changing which fields are fixed values versus `*`.

## Related: File 17 (Service Management) — cron itself runs as a system service; file 11 (Processes and Job Control) for how a running cron job actually shows up as a process while it executes.

## Cheat Sheet

```
crontab -l    list current user's cron jobs
crontab -e    edit current user's cron jobs

Minute(0-59) Hour(0-23) Day-of-Month(1-31) Month(1-12) Day-of-Week(0-7, 0=7=Sunday)  Command

*        every value for that field
*/5      every 5 units (e.g. */5 in Minute = every 5 minutes)
1-5      a range (e.g. Day-of-Week 1-5 = Mon-Fri)

# at the start of a line = comment / disabled job
```
