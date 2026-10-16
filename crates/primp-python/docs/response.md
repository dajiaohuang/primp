# Response

`Client` and module-level request helpers return `Response`; `AsyncClient` returns
`AsyncResponse`. Both types support buffered and streaming modes, with synchronous
streaming methods on `Response` and asynchronous streaming methods on `AsyncResponse`.

All properties are synchronous and work identically for both sync and async clients.

## Response Properties

| Property | Type | Description |
|:---------|:-----|:------------|
| `.url` | `str` | Final response URL (after redirects) |
| `.status_code` | `int` | HTTP status code |
| `.content` | `bytes` | Raw response body |
| `.encoding` | `str` | Character encoding (read/write) |
| `.text` | `str` | Decoded text content |
| `.headers` | `dict` | Response headers |
| `.cookies` | `dict` | Response cookies |
| `.text_markdown` | `str` | HTML → Markdown |
| `.text_plain` | `str` | HTML → plain text |
| `.text_rich` | `str` | HTML → rich text |

| Method | Description |
|:-------|:------------|
| `.json()` | Parse as JSON (raises `JSONDecodeError` on failure, also catchable as `json.JSONDecodeError`) |
| `.raise_for_status()` | Raise `StatusError` for 4xx/5xx |

## Streaming

Returned when using `stream=True`. Supports context manager for automatic cleanup.

```python
# Sync
with primp.get(url, stream=True) as resp:
    for chunk in resp.iter_bytes():
        process(chunk)

# Async
async with await client.get(url, stream=True) as resp:
    async for chunk in resp.aiter_bytes():
        process(chunk)
```

### Streaming Methods

| Sync | Async | Description |
|:-----|:------|:------------|
| `.read()` | `.aread()` | Read all remaining content |
| `.iter_bytes(size)` | `.aiter_bytes(size)` | Iterate byte chunks |
| `.iter_text(size)` | `.aiter_text(size)` | Iterate text chunks |
| `.iter_lines()` | `.aiter_lines()` | Iterate lines |
| `.next()` | `.anext()` | Get next chunk |
| `.close()` | `.aclose()` | Release resources |

Always use context managers with streams to ensure cleanup:

```python
# Good
with primp.get(url, stream=True) as resp:
    for chunk in resp.iter_bytes(65536):  # 64KB chunks
        process(chunk)

# Avoid — remember to call resp.close()
resp = primp.get(url, stream=True)
for chunk in resp.iter_bytes():
    process(chunk)
resp.close()
```
