# Development Frameworks, APIs & Databases

## Development Frameworks

Frameworks provide reusable functionality (user registration, routing, templating, etc.) so common features don't need to be built from scratch each time.

| Framework | Language | Notable users |
|---|---|---|
| Laravel | PHP | Startups, smaller companies |
| Express | Node.js | PayPal, Yahoo, Uber, IBM |
| Django | Python | Google, YouTube, Instagram |
| Rails | Ruby | GitHub, Hulu, Twitch, Airbnb |

Large sites commonly combine **multiple** frameworks/web servers rather than depending on just one.

### Flask Routing (a minimal example)

```python
@app.route("/hack", methods=["GET"])
def challenge():
    ...
```
```
/hack        → endpoint path
GET           → accepted HTTP method(s)
challenge()    → handler function that runs for this route
```
`@app.route()` maps a URL path to a Python function; `methods=` restricts which HTTP methods that function will handle.

## APIs

An **API (Application Programming Interface)** defines how one piece of software talks to another. On the web, this most often means the front end requesting functionality from the back end:
```
1. Front end sends request (with input)
2. Back end processes it
3. Back end performs the operation
4. Response returned to front end
5. Front end renders the result
```
Web APIs are usually accessed over HTTP, frequently exchanging JSON.

### SOAP vs. REST

| | SOAP | REST |
|---|---|---|
| Data format | XML only | Usually JSON (can also do XML, form-urlencoded, raw) |
| Structure | Formal envelope (Header/Body/Fault) | URL paths identify resources |
| Complexity | Higher, even for simple calls | Generally simpler, modular endpoints |
| State | Can be stateful | Typically stateless |

```xml
<soap:Envelope>
  <soap:Header></soap:Header>
  <soap:Body>
    <soap:Fault></soap:Fault>
  </soap:Body>
</soap:Envelope>
```
```
REST example path: /search/users/1
```

### REST and HTTP Methods

| Method | Purpose | Idempotent |
|---|---|---|
| `GET` | Retrieve data | Yes |
| `POST` | Create data | **No** |
| `PUT` | Create or replace data | Yes |
| `DELETE` | Remove data | Yes |

(Same idempotency table as file 03 — REST just leans on HTTP's existing method semantics directly.)

## Databases

Web apps use databases to persist images/files, posts, credentials, and other application data. Key characteristics to weigh: speed, storage capacity, scalability, cost.

### Relational (SQL) Databases

Organize data into **tables, rows, columns**; tables relate via **keys**; the overall structure/relationships form the **schema**.
```
users                      posts
id | username | ...        id | user_id | date | content
```
`posts.user_id` references `users.id` — avoids duplicating user info in every post. Good fit for structured data with clear relationships.

| DB | Notes |
|---|---|
| MySQL | Popular, open-source, free |
| MSSQL | Microsoft's, common with Windows/IIS |
| Oracle | Enterprise-focused, reliable, often expensive |
| PostgreSQL | Open-source, highly extensible |

(Also common: SQLite, MariaDB, Amazon Aurora, Azure SQL.)

### Non-Relational (NoSQL) Databases

Skip the rigid table/row/schema structure — generally more flexible/scalable, suited to less-structured or fast-changing data.

| Model | Idea |
|---|---|
| Key-Value | `{key: value}` — like a dictionary/map |
| Document-Based | JSON-like documents + metadata |
| Wide-Column | Column-family storage |
| Graph | Nodes + relationships as first-class data |

```json
{ "100001": { "date": "01-01-2021", "content": "Welcome." } }
```

| DB | Model |
|---|---|
| MongoDB | Document-based |
| Elasticsearch | Search/analytics-optimized |
| Apache Cassandra | Highly scalable, fault-tolerant |

(Also common: Redis, Neo4j, CouchDB, Amazon DynamoDB.)

### Connecting from Application Code

```php
$conn = new mysqli("localhost", "user", "pass");
$conn->query("CREATE DATABASE database1");

$conn = new mysqli("localhost", "user", "pass", "database1");
$result = $conn->query("select * from table_1");
while ($row = $result->fetch_assoc()) {
    echo $row["name"] . "<br>";
}
```
Applications frequently build queries using **user-supplied input**:
```php
$searchInput = $_POST['findUser'];
$query = "select * from users where name like '%$searchInput%'";
```
⚠️ This exact pattern — pasting user input straight into a query string — is the textbook setup for **SQL Injection** if the input isn't properly handled; see the Web Vulnerabilities notebook for the attack side of this.

## Cheat Sheet

```
Framework examples: Laravel(PHP) Express(Node) Django(Python) Rails(Ruby)
@app.route("/path", methods=["GET"])   → Flask: path + method → handler function

API: front end ↔ HTTP ↔ back end, usually exchanging JSON
SOAP: XML envelope, more complex     REST: URL-based resources, usually JSON, simpler

GET(retrieve) POST(create,not idempotent) PUT(create/replace) DELETE(remove) — REST/HTTP shared

SQL (relational): tables/rows/columns/keys/schema — MySQL, PostgreSQL, MSSQL, Oracle
NoSQL: Key-Value / Document / Wide-Column / Graph — MongoDB, Redis, Cassandra, Neo4j

Pasting raw user input into a query string = classic SQL injection setup
```
