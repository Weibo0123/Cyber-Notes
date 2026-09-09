# Python for Blue Team — Review Set


| #   | File                                                                             | Covers                                                                 |
| --- | -------------------------------------------------------------------------------- | ----------------------------------------------------------------------- |
| 01  | [Python and Socket Programming](01%20Python%20and%20Socket%20Programming.md)      | `socket` basics, TCP server example, address families, socket types, UDP |
| 02  | [Port Scanner and Service Monitoring](02%20Port%20Scanner%20and%20Service%20Monitoring.md) | `connect_ex()` port scanning, automated availability monitoring + Slack alerts |
| 03  | [Web Scraping with Python](03%20Web%20Scraping%20with%20Python.md)                  | Requests/Beautiful Soup/Scrapy compared, `.find()`, CSS selectors, XPath, saving results |
| 04  | [IDS Application with Python](04%20IDS%20Application%20with%20Python.md)             | Raw sockets, parsing the IPv4 header with `struct`, matching traffic against a watchlist |
| 05  | [File Integrity Monitoring (FIM) with Python](05%20File%20Integrity%20Monitoring%20(FIM)%20with%20Python.md) | SHA-256 baseline hashing, continuous integrity checks |
| 06  | [Forensics and Incident Response with Python](06%20Forensics%20and%20Incident%20Response%20with%20Python.md) | IOC search (Linux logs, Windows Event Logs), hash-based file search, memory forensics, disk images |

## How These Chapters Connect

Unlike the other note sets, this one reads less like independent topics and more like a small toolkit where each piece feeds another:
```
File 03 (scrape a threat-intel IP list) → saves iplist.txt
                                              ↓
File 04 (IDS) → reads iplist.txt as its watchlist, built on File 01's raw-socket type
File 02 (Port Scanner) → also builds directly on File 01's socket basics
File 06's chunked-hashing technique → improves on File 05's simpler whole-file hashing
```
Worth reading with that pipeline in mind rather than as fully separate chapters.
