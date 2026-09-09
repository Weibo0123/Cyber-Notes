# Port Scanner & Service Monitoring

Builds directly on file 01's socket basics — a port scanner is just repeated TCP connection attempts against a range of ports.

## Basic Port Scanner

```python
import socket

target = "letsdefend.io"

for port in range(1, 1025):
    s = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
    s.settimeout(1)

    result = s.connect_ex((target, port))

    if result == 0:
        print(f"Port {port} open!")

    s.close()
```
The scanner attempts a TCP connection to every port from `1` to `1024`, reporting which ones respond.

### `connect()` vs. `connect_ex()`

| | Behavior |
|---|---|
| `connect()` | Raises an exception if the connection fails |
| `connect_ex()` | Returns an **error code** instead — no exception handling needed |

`connect_ex()` returns `0` specifically when the connection **succeeds**, making it a convenient way to check "is this port open?" without wrapping every attempt in `try`/`except`.

`settimeout(1)` caps how long each attempt waits before giving up — without it, a closed/filtered port could hang the scan far longer than necessary.

## Automated Port Monitoring

The same connection-checking logic, run continuously, turns a scanner into a **service availability monitor**:

```python
import socket
import time
import requests

targets = [
    ("letsdefend.io", 80, "TCP"),
    ("letsdefend.io", 443, "TCP"),
    ("8.8.8.8", 80, "TCP")
]

SLACK_WEBHOOK_URL = "YOUR_SLACK_WEBHOOK_URL_HERE"


def check_port(target, port, protocol):
    try:
        s = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
        s.settimeout(3)

        if protocol == "TCP":
            result = s.connect_ex((target, port))
            s.close()
            return result == 0

    except socket.error:
        return False


def send_slack_notification(target, port, protocol):
    payload = {"text": f"{target}:{port}/{protocol} port not responding!"}
    print(payload)
    requests.post(SLACK_WEBHOOK_URL, json=payload)


while True:
    for target, port, protocol in targets:
        if not check_port(target, port, protocol):
            send_slack_notification(target, port, protocol)

    time.sleep(3600)
```
Example requirement this satisfies: check `letsdefend.io:80/TCP`, `letsdefend.io:443/TCP`, and `8.8.8.8:80/TCP` every 60 minutes, alerting to Slack on failure.

### Key Components

| Piece | Role |
|---|---|
| `socket` + `connect_ex()` | Performs the actual connection check |
| `settimeout(3)` | Bounds how long each check can take |
| `requests.post()` | Sends the alert to a Slack **incoming webhook** |
| `time.sleep(3600)` | Waits 60 minutes between full monitoring cycles |
| `while True` | Repeats the cycle indefinitely |

⚠️ `except socket.error` in `check_port()` catches connection-level failures (e.g. DNS resolution failure, network unreachable) and treats them the same as "port closed" — worth remembering if you ever need to distinguish *why* a check failed, not just *whether* it failed.

## Cheat Sheet

```
s.connect_ex((host, port)) == 0      port is open (no exception needed)
s.settimeout(n)                        cap wait time per connection attempt

Monitoring pattern:
  while True:
      for each target: check_port() → alert if failed
      time.sleep(interval)

requests.post(webhook_url, json={"text": "..."})    Slack incoming-webhook alert
```
