# Linux Service Management

## What Is a Service?

A **service** is a program that runs, or waits to run, in the background — an FTP daemon, a web server, or any other system service that isn't tied to an interactive terminal session. On systems using `systemd` (most modern distributions), services are managed with **`systemctl`**.

### Why This Matters for Security

Every running service is a potential entry point: it can expose functionality over a network port and widen the system's attack surface. Basic hardening practice follows directly from this: stop or disable services that aren't actually needed, and keep an eye on the ones that are.

## Listing Services

```bash
systemctl                        # list services, running or not
systemctl --state=running        # list only services currently running
```

## Checking, Stopping, and Starting a Specific Service

```bash
systemctl status apache2.service     # view a service's current status (running/inactive/etc.)
sudo systemctl stop apache2.service  # stop it → state becomes "inactive"
sudo systemctl start apache2.service # start it → state returns to "running"
```

The `.service` suffix can be included explicitly or left off in most of these commands, depending on the tool's own resolution rules — writing it out (`apache2.service`) is always unambiguous.

### `stop`/`start` vs. `enable`/`disable`

It's worth distinguishing two different axes of "on or off" that `systemctl` controls separately:

- **`start`/`stop`** change whether a service is running *right now*, in the current boot session.
- **`enable`/`disable`** change whether a service will *automatically start on the next boot* — independent of its current running state.

A service can be running now but disabled for next boot, or stopped now but still enabled for next boot. Genuinely "turning off" a service you don't want, in a lasting way, usually means both stopping it now *and* disabling it for future boots.

## Related: File 16 (Package Management) for installing the software behind a service in the first place; file 11 (Processes and Job Control) for inspecting a running service at the process level once `systemctl status` says it's active.

## Cheat Sheet

```
systemctl                            list all services
systemctl --state=running            list only running services
systemctl status <service>.service   check a specific service's status

sudo systemctl stop <service>        stop it now
sudo systemctl start <service>       start it now
sudo systemctl disable <service>     prevent it from starting on next boot
sudo systemctl enable <service>      allow it to start on next boot

stop/start  = current running state.   enable/disable = behavior on next boot.
```
