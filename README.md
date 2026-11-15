# Restful-booker-api-testing

Automated API test suite for the live [Restful-Booker](https://restful-booker.herokuapp.com/apidoc/index.html) API.
Executes a chained flow: **Auth → Create → Update → Verify → Delete**, with a negative scenario for every endpoint.

## How to run
1. Import `Restful Booker API Testing.postman_collection.json` into Postman.
2. Right-click the collection → **Run collection** → **Run** (requests execute in order).
3. No setup needed: `token` and `bookingId` are captured automatically as collection variables.

## Dynamic variable chaining
| Variable | Set by | Used by |
|---|---|---|
| `token` | Auth (POST /auth) | PUT and DELETE via `Cookie: token={{token}}` |
| `bookingId` | Create (POST /booking) | PUT, GET, DELETE via `/booking/{{bookingId}}` |

## Test coverage
| Endpoint | Positive test | Negative test |
|---|---|---|
| POST /auth | 200, token schema, token saved | Wrong password → no token issued |
| POST /booking | 200, JSON schema, payload echo, `bookingid` saved | Missing required fields → rejected |
| PUT /booking/:id | 200, schema, every updated field asserted | No token → 403 Forbidden |
| GET /booking/:id | 200, schema, update persisted, unchanged fields kept | Non-existent ID → 404 |
| DELETE /booking/:id | 201, body "Created", then GET returns 404 | No token → 403; already deleted → 405 |

## Bugs / API deviations found
| # | Endpoint | Actual behavior | Expected behavior | Severity |
|---|---|---|---|---|
| 1 | POST /auth | Invalid credentials return **200** with `{"reason":"Bad credentials"}` | **401 Unauthorized** | Medium |
| 2 | POST /booking | Missing required fields return **500 Internal Server Error** | **400 Bad Request** with validation message | High |
| 3 | DELETE /booking/:id | Successful delete returns **201 Created** | **200 OK** or **204 No Content** | Low |
| 4 | DELETE /booking/:id | Deleting a non-existent booking returns **405 Method Not Allowed** | **404 Not Found** | Low |

## Request / response analysis
Add one short entry per request here (request details, response details, analysis). Screenshots are in the `screenshots/` folder.

## Results
Add the Collection Runner summary screenshot here, for example `![Runner results](screenshots/runner-results.png)`.
