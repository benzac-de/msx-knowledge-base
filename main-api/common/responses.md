---
title: Responses
category: Main API - Common
summary: Reference for MSX response types returned from server endpoints.
---

# Responses

By default, the JSON data is provided directly as root object. Please see this example code of a menu root object.

```json
{
    "headline": "My Menu",
    "menu": [{
            "label": "My Menu Item"
        }]
}
```

This data structure is very suitable if the HTTP server hosts static JSON files. However, if the HTTP server uses a database or an external API to dynamically create JSON files, an extended data structure is useful. Therefore, it is possible to wrap any JSON data in a `response` object to have an unified root object and to provide advanced error handling. Please see this example code of a wrapped menu root object.

```json
{
    "response": {
        "status": 200,
        "text": "OK",
        "message": null,
        "data": {
            "headline": "My Menu",
            "menu": [{
                    "label": "My Menu Item"
                }]
        }
    }
}
```

The next example code shows how an error response can look like.

```json
{
    "response": {
        "status": 404,
        "text": "Not Found",
        "message": "Menu for database ID 'xyz' not found.",
        "data": null
    }
}
```

If you execute actions on server side, the server action response must always be wrapped in a `response` object. Please see the next example code of how a server action response can look like.

```json
{
    "response": {
        "status": 200,
        "text": "OK",
        "message": null,
        "data": {
            "action": "menu:data",
            "data": {
                "headline": "My Menu",
                "menu": [{
                        "label": "My Menu Item"
                    }]
            }
        }
    }
}
```

Please see [Actions](./actions.md#server-actions) for more information.

## Property Syntax of Response Object

| Property | Type | Default Value | Mandatory | Since Version | Description |
|---|---|---|---|---|---|
| `status` | `number` | `0` | **Yes** | 0.1.0 | HTTP-compliant status code. This property must be set to `200` for success responses.<br><br>**Note: Error responses must also be sent as successful HTTP (2xx) responses. The actual error status code is provided in this property.** |
| `text` | `string` | `null` | No | 0.1.0 | HTTP-compliant status text. |
| `message` | `string` | `null` | No | 0.1.0 | This property is only used in error responses and can be used to provide a detailed error description. |
| `data` | `object` | `null` | No | 0.1.0 | This property is only used in success responses and contains the actual JSON data. |

**Note: MSX is a pure client-side framework. There is no server component included — you provide the JSON endpoints on any HTTP server of your choice. MSX fetches these URLs, parses the responses, and renders the UI or executes the action. Please note that every HTTP request made by MSX has a timeout of 30 seconds. If no response is received within this time, MSX discards the request and throws an internal error. Design your endpoints to respond well within this limit.**

## Error Handling

There are three distinct error scenarios.

| Scenario | Cause | Behavior |
|---|---|---|
| **HTTP error** | The server returns a non-2xx HTTP status (e.g. 404, 500). | Platform-dependent. Most TV platforms cannot display error details in this case — the error message may appear or a generic one is shown. **Avoid relying on this for user-facing error details.** |
| **Response error** | The server returns HTTP 200 but the `response.status` is not `200`. | MSX displays the `message` property to the user. This is the **recommended approach** for surfacing errors, because it works reliably across all TV platforms. |
| **Request timeout** | No response received within **30 seconds**. | MSX discards the request and throws an internal error. |

The `response` wrapper was introduced specifically to work around the limitation that many TV platforms cannot reliably display non-200 HTTP errors. By always returning HTTP 200 and encoding the error state inside the `response` object, errors are guaranteed to reach the user.

## See also

- [Requests](requests.md)
- [JSON Building Guide → Step 8 — Actions](../../reference/json-building-guide.md#step-8--actions) — the exact error text MSX throws when a server action response omits `response.data.action` or the `response` object itself, and the full `accurate:`/`service:`/`silent:` prefix behavior
- [Common Misconceptions → Server actions](../../reference/common-misconceptions.md#server-actions-execute) and [→ Server responses](../../reference/common-misconceptions.md#server-responses)