---
description: How to use the HTTP interface
---

# 💻 HTTP Interface

## Endpoints

The HTTP interface is composed of only two routes.

<table><thead><tr><th>path</th><th>description</th><th>input</th><th>output</th><th data-hidden>name</th></tr></thead><tbody><tr><td>/catalog</td><td>get schemas, tables, columns metadata</td><td></td><td>A JSON-formatted representation of the database catalog</td><td>catalog</td></tr><tr><td>/query</td><td>process SQL queries</td><td>An SQL query in the HTTP request's body</td><td>A JSON-formatted result set</td><td>query</td></tr></tbody></table>

## Caching

Various parameters of the query influence the caching behavior of the query endpoint. We try to stay as close to standard HTTP caching as possible and implement custom extensions only when needed. Cache-related parameters must be passed through the standard `Cache-Control` header (or `cache-control` query param).

| supported directive | semantic                                                 |
| ------------------- | -------------------------------------------------------- |
| `no-cache`          | Bypass query cache                                       |
| `no-store`          | Do not store the result of this query in the query cache |
| `max-age=N`         | The client can tolerate a result at most `N` seconds old |

We support some custom request headers related to the caching behavior of the HTTP interface.

| header                             |                                                                                                                                                                                                                                      |
| ---------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| `X-Agnostic-Cache-Refresh-Trigger` | <p>The value must be a float between 0 and 1.<br>On cache hit, we compute the ratio <code>Age / Max-Age</code> with this value and trigger an asynchronous cache refresh when the value is higher than the aforementioned ratio.</p> |

Some cache-related headers are set on the response.

| header             | meaning                                                                                                                                                                                                                                               |
| ------------------ | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `X-Agnostic-Cache` | will be set to `hit` if the resultset comes from the cache, `miss` otherwise                                                                                                                                                                          |
| `Cache-Control`    | `max-age`, `no-cache` and `no-store` directives will be set to ensure no intermediate (browser, proxy, ...) cache the response. This ensures we have complete control over caching so that features like asynchronous cache refresh work as expected. |
| `Age`              | this header is set on cache hit with the age (in seconds) of the served result                                                                                                                                                                        |

