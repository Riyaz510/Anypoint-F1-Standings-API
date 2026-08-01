# F1 Standings API

A MuleSoft Anypoint Studio application that proxies Formula 1 driver and constructor standings data from the **OpenF1 API** (`api.openf1.org`). The API exposes two HTTP endpoints that forward requests to the upstream OpenF1 service and return the raw JSON responses.

---

## Tech Stack

| Technology | Version / Detail |
|---|---|
| MuleSoft Anypoint Studio | Mule Runtime 4.12.1 |
| Mule Maven Plugin | 4.10.1 |
| Java | 17 |
| HTTP Connector | 1.11.3 |
| Sockets Connector | 1.2.7 |
| Upstream API | [OpenF1 API](https://openf1.org) (HTTPS) |

---

## Project Structure

```
f1-standing-api/
├── src/
│   ├── main/
│   │   ├── mule/
│   │   │   └── f1-standing-api.xml       # Mule flows (listener + HTTP request configs)
│   │   └── resources/
│   │       ├── api/                       # API specification directory (RAML)
│   │       ├── application-types.xml      # Mule type catalog
│   │       └── log4j2.xml                 # Log4j2 logging configuration
│   └── test/
├── exchange-docs/
│   └── home.md                            # Anypoint Exchange documentation
├── mule-artifact.json                     # Mule artifact metadata
└── pom.xml                                # Maven project descriptor
```

---

## Mule Flows

The core logic lives in `src/main/mule/f1-standing-api.xml` and defines two flows:

### `driverflow`
- **Listener**: `GET /api/standings/drivers`
- **Upstream request**: `GET https://api.openf1.org/v1/championship_drivers?session_key=9839`
- Returns raw JSON array of driver championship standings for the given session.

### `constructorsFlow`
- **Listener**: `GET /api/standings/constructors`
- **Upstream request**: `GET https://api.openf1.org/v1/championship_teams?session_key=9839`
- Returns raw JSON array of constructor/team championship standings for the given session.

### HTTP Configurations

| Config Name | Role | Host | Port | Protocol |
|---|---|---|---|---|
| `HTTP_Listener_config` | Inbound listener | `0.0.0.0` | `8081` | HTTP |
| `HTTP_Request_configuration` | Outbound to OpenF1 | `api.openf1.org` | `443` | HTTPS |

---

## Running the Project

### Prerequisites
- MuleSoft Anypoint Studio installed
- Java 17 JDK
- Internet access (to reach `api.openf1.org`)

### Steps

1. **Clone / Import the project** into Anypoint Studio:
   - `File → Import → Anypoint Studio → Anypoint Studio Project from File System`

2. **Run as a Mule Application**:
   - Right-click the project → `Run As → Mule Application`
   - Wait for the console to show `DEPLOYED`

3. **Access the endpoints** (default port `8081`):

   ```
   GET http://localhost:8081/api/standings/drivers
   GET http://localhost:8081/api/standings/constructors
   ```

---

## Testing with Postman

The API was tested using **Postman**. Below are the request details:

### Driver Standings

| Field | Value |
|---|---|
| Method | `GET` |
| URL | `http://localhost:8081/api/standings/drivers` |
| Headers | *(none required)* |
| Body | *(none)* |

**Sample Response (200 OK):**
```json
[
  {
    "broadcast_name": "M VERSTAPPEN",
    "country_code": "NED",
    "driver_number": 1,
    "first_name": "Max",
    "full_name": "Max VERSTAPPEN",
    "headshot_url": "https://...",
    "last_name": "Verstappen",
    "meeting_key": 1256,
    "name_acronym": "VER",
    "points": 437,
    "position": 1,
    "session_key": 9839,
    "team_colour": "3671C6",
    "team_name": "Red Bull Racing"
  }
]
```

---

### Constructor Standings

| Field | Value |
|---|---|
| Method | `GET` |
| URL | `http://localhost:8081/api/standings/constructors` |
| Headers | *(none required)* |
| Body | *(none)* |

**Sample Response (200 OK):**
```json
[
  {
    "country_code": "AUT",
    "meeting_key": 1256,
    "points": 860,
    "position": 1,
    "session_key": 9839,
    "team_colour": "3671C6",
    "team_name": "Red Bull Racing"
  }
]
```

---

## Upstream API Reference

This application consumes the [OpenF1 REST API](https://openf1.org).

| Endpoint | Description |
|---|---|
| `GET /v1/championship_drivers` | Driver championship standings filtered by `session_key` |
| `GET /v1/championship_teams` | Constructor championship standings filtered by `session_key` |

> **Note:** The `session_key` query parameter is hardcoded to `9839` in the current implementation. To target a different F1 season/session, update the DataWeave query-param block in each flow.

---

## Logging

Log output is written to:
```
<mule-home>/logs/f1-standing-api.log
```
Configured via `src/main/resources/log4j2.xml` with a rolling file appender (max 10 MB per file, up to 10 files retained).

---

## Author

**Mohd Riyaz**