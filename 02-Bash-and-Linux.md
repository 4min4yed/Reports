# Bash and Linux

## Scripting and Shell Basics

- Numeric comparison example: use `-eq`.
- String comparison: use quoted variables.
- Exit status conventions: `0` success, non-zero failure.

## Useful Commands

- List all users:

```bash
cut -d: -f1 /etc/passwd
```

- Convert Windows line endings to Unix:

```bash
dos2unix file.sh
```

- Hash file:

```bash
md5sum file.txt
```

- Exclude pattern with grep:

```bash
grep -v "word"
```

- Follow logs:

```bash
tail -f file.txt   # follow by descriptor
tail -F file.txt   # follow by filename (handles rotation)
```

- File size:

```bash
ls -lh file
```

- Audible bell:

```bash
echo -e "\a"
```

- PID by process name:

```bash
pidof process_name
```

- Count matches:

```bash
grep -c pattern file
# equivalent to grep pattern file | wc -l
```

- What can run with sudo:

```bash
sudo -l
```

## Parallel Execution

```bash
parallel curl -s -X POST -d {1} {2} ::: $(cat fuzz.txt) ::: $(cat eps.txt)
```

Equivalent nested loop style:

```bash
for i in $(cat fuzz.txt); do
  for j in $(cat eps.txt); do
    curl "$j" -d "$i"
  done
done
```

## Aliases

```bash
nano ~/.bashrc
# add:
alias XYZ='long_command'
source ~/.bashrc
```

## Finding Commands and Files

```bash
which command
whereis command
find /usr -name command -type f
find / -name docker-compose.yml
```

## Init/Systemd Checks

```bash
ps -p 1
```

- If PID 1 is not systemd, `systemctl` may not work.
- For runit or specific service managers, use their native CLI (example: `gitlab-ctl status`).

## Filesystem and Embedded Notes

- `/bin`: executable commands.
- `/etc`: configuration files.
- `opkg`: package manager in OpenWRT.
- `busybox`: many small tools bundled in one binary.

## Disable Sleep and Hibernate (Linux)

```bash
sudo systemctl mask sleep.target suspend.target hibernate.target hybrid-sleep.target
sudo nano /etc/systemd/logind.conf
# HandleLidSwitch=ignore
# HandleLidSwitchExternalPower=ignore
# HandleLidSwitchDocked=ignore
sudo systemctl restart systemd-logind

# Optional GNOME settings
gsettings set org.gnome.settings-daemon.plugins.power sleep-inactive-ac-type 'nothing'
gsettings set org.gnome.settings-daemon.plugins.power sleep-inactive-battery-type 'nothing'
```

Verification:

```bash
systemctl status sleep.target suspend.target hibernate.target hybrid-sleep.target
gsettings get org.gnome.settings-daemon.plugins.power sleep-inactive-ac-type
gsettings get org.gnome.settings-daemon.plugins.power sleep-inactive-ac-timeout
uptime
grep HandleLidSwitch /etc/systemd/logind.conf
```
