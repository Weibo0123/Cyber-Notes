# Web Architecture: Front End, Back End, Servers & Infrastructure

## Front End vs. Back End

| | Front End | Back End |
|---|---|---|
| Runs | Client-side, in the browser | Server-side |
| Visible to users | Yes, directly | No, not directly |
| Technologies | HTML, CSS, JavaScript | Web server, application logic, database, framework |
| Also includes | Visual design, UI, UX | APIs, business logic, integrations |

A poorly-optimized front end can make an app feel slow/unresponsive even when the server itself is perfectly healthy — performance problems aren't automatically a back-end issue.

### Back-End Components

| Component | Role | Examples |
|---|---|---|
| Back-end server | Hardware/OS hosting everything | Linux, Windows, containers |
| Web server | Handles HTTP connections/requests | Apache, NGINX, IIS |
| Database | Stores/retrieves data | MySQL, PostgreSQL, MongoDB |
| Development framework | Structure/libraries for building the app | Laravel, Django, Express, Rails |

These can run on the same machine or be split across separate servers/containers (e.g. Docker) — splitting gives **logical isolation**, limiting how far a compromise of one component can spread.

### Back-End Responsibilities
Application logic, core functionality, database development/maintenance, reusable libraries, technical/business requirements, APIs for the front end, integrating remote/cloud services.

## Web Servers

A **web server** handles HTTP traffic specifically — receiving requests, routing them to the right resource/process, and returning responses. Typically listens on TCP `80` (HTTP) or `443` (HTTPS).

| Server | Notes |
|---|---|
| **Apache (`httpd`)** | Open-source; commonly paired with PHP (`mod_php`); also supports .NET, Python, Perl, Bash via CGI; cross-platform |
| **NGINX** | Open-source; asynchronous architecture → handles many concurrent connections with low overhead; favored for high-traffic sites |
| **IIS** | Microsoft's web server; Windows Server; pairs with .NET; strong Active Directory / Windows Authentication integration |
| Others | Apache Tomcat (Java apps), Node.js (JS back ends) |

## Server Stacks

A "stack" names the OS + web server + database + language/framework together:
```
LAMP   Linux + Apache + MySQL + PHP
WAMP   Windows + Apache + MySQL + PHP
WINS   Windows + IIS + .NET + SQL Server
MAMP   macOS + Apache + MySQL + PHP
XAMPP  Cross-platform + Apache + MySQL + PHP/Perl
```

## Web Application Infrastructure Models

| Model | Description | Trade-off |
|---|---|---|
| **Client-Server** | Browser ↔ web server ↔ app logic ↔ database | The basic pattern everything else builds on |
| **One Server** | App + database on a single machine | Simple, but a single point of failure — one compromise/outage affects everything |
| **Many Servers – One Database** | Several app servers share one DB server | App servers isolated from each other; compromising one doesn't automatically compromise the rest — access control still needed so each app only reaches the data it should |
| **Many Servers – Many Databases** | Each app gets its own DB(s) | Stronger isolation, easier redundancy/failover, but more complex (often needs load balancers) |

```
Client-Server:      Browser → Web Server → App Logic → Database → (response back)
Many Servers/1 DB:  Server1, Server2, Server3 → shared Database
Many Servers/N DBs: Server1→DB1   Server2→DB2   Server3→DB3
```

## Three-Tier Architecture

```
Presentation Layer  → UI, client communication (HTML/CSS/JS)
Application Layer     → request processing, business logic, auth
Data Layer               → data storage/retrieval
```
Layers can be physically separate machines/services. Some web servers can also execute server-side programs directly (IIS ISAPI, PHP-CGI).

## Microservices & Serverless

**Microservices** — split an application into small, independent, per-function services (e.g. separate services for registration, search, payments, reviews) that communicate via APIs. Typically **stateless**: each request carries what it needs; persistent data lives elsewhere. Different services can even use different languages. Benefits: agility, independent scaling, easier deployment, resilience.

**Serverless** — the cloud provider manages provisioning, scaling, and maintenance; the application is built from **stateless functions/containers** that run on trigger. You focus on code/config, not infrastructure.

## Architecture Security — a Design Concern, Not Just a Code Concern

Security problems aren't only programming bugs — **architectural decisions** matter too: network segmentation, access control, authentication/authorization, RBAC, separating app servers from databases, isolating applications from each other, redundancy/failover. An app can have flawless individual features and still have **broken access control** letting users reach admin functionality or other users' data because of how the pieces are wired together.

> **Key principle:** security has to be considered across the *entire* architecture, not component-by-component — but conversely, good segmentation also means one compromised component doesn't necessarily hand over everything else.

## Cheat Sheet

```
Front End (browser: HTML/CSS/JS)  vs.  Back End (server: logic/DB/framework)

Web server: Apache / NGINX / IIS         → handles HTTP specifically
Stack: LAMP / WAMP / WINS / MAMP / XAMPP

One Server                → simple, single point of failure
Many Servers – 1 DB          → app-layer isolation, shared data
Many Servers – Many DBs        → strongest isolation, most complex

3-Tier: Presentation → Application → Data

Microservices  → small independent stateless services, per function
Serverless       → provider manages infra; you ship stateless functions

Security = whole-architecture concern, not just per-feature code review
```
