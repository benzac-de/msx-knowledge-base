---
title: Setup Precondition
category: Main API - Start
summary: Explains the HTTP server and CORS requirements for hosting MSX JSON files.
---

# Setup Precondition

You need a local or public HTTP server (e.g. Apache, nginx, Microsoft IIS, Node.js, etc.) to host your JSON files. Alternatively, you can use your NAS (Network Attached Storage) server or device that supports web server functionality. The HTTP server must support **CORS** (Cross-Origin Resource Sharing) to ensure that the JSON files can be loaded from each platform. This should not be a problem, because almost any HTTP server can be configured to support CORS. However, depending on the used HTTP server, the CORS support is configured differently. Please search the web for how it is done for your server. The next examples show how a basic CORS support can look like.

## CORS via Apache and the `.htaccess` file

```bash
Header set Access-Control-Allow-Origin "*"
Header set Access-Control-Allow-Headers "Origin, Content-Type, Accept"
Header set Access-Control-Allow-Methods "GET, OPTIONS"
#...
```

## CORS via PHP

```php
<?php
header('Access-Control-Allow-Origin: *');
header('Access-Control-Allow-Headers: Origin, Content-Type, Accept');
header('Access-Control-Allow-Methods: GET, OPTIONS');
//...
```

## CORS via Java and the `HttpServlet` interface

```java
protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
    resp.setHeader("Access-Control-Allow-Origin", "*");
    resp.setHeader("Access-Control-Allow-Headers", "Origin, Content-Type, Accept");
    resp.setHeader("Access-Control-Allow-Methods", "GET, OPTIONS");
    //...        
}
```

## CORS via Node.js and the `http` library

```javascript
const server = http.createServer((req, res) => {
    res.setHeader("Access-Control-Allow-Origin", "*");
    res.setHeader("Access-Control-Allow-Headers", "Origin, Content-Type, Accept");
    res.setHeader("Access-Control-Allow-Methods", "GET, OPTIONS");
    //...
});
```

## CORS via Node.js and the `http-server` package

```bash
http-server --cors
```

## CORS via Apache Tomcat and the `web.xml` file

```xml
<!-- ... -->
<filter>
    <filter-name>CorsFilter</filter-name>
    <filter-class>org.apache.catalina.filters.CorsFilter</filter-class>
    <init-param>
        <param-name>cors.allowed.origins</param-name>
        <param-value>*</param-value>
    </init-param>
    <init-param>
        <param-name>cors.allowed.headers</param-name>
        <param-value>Origin, Content-Type, Accept</param-value>
    </init-param>
    <init-param>
        <param-name>cors.allowed.methods</param-name>
        <param-value>GET, OPTIONS</param-value>
    </init-param>    
</filter>
<filter-mapping>
    <filter-name>CorsFilter</filter-name>
    <url-pattern>/*</url-pattern>
</filter-mapping>
<!-- ... -->
```

## CORS via Microsoft IIS and the `web.config` file

```xml
<!-- ... -->
<system.webServer>
    <cors enabled="true">
        <add origin="*">
            <allowHeaders>
                <add header="Origin" />
                <add header="Content-Type" />
                <add header="Accept" />
            </allowHeaders>
            <allowMethods>
                <add method="GET" />
                <add method="OPTIONS" />
            </allowMethods>
        </add>
    </cors>
</system.webServer>  
<!-- ... -->
```

## CORS via nginx and the `nginx.conf` file

```nginx
http {
    server {
        #...
        add_header 'Access-Control-Allow-Origin' '*';
        add_header 'Access-Control-Allow-Headers' 'Origin, Content-Type, Accept';
        add_header 'Access-Control-Allow-Methods' 'GET, OPTIONS';	
        #...
    }
}
```

## More information about CORS

CORS (**C**ross-**O**rigin **R**esource **S**haring) is a browser security mechanism that controls how a web application running at one origin (scheme, domain, and port) can request resources from a different origin. All modern browsers enforce CORS strictly — and **CORS misconfiguration is the #1 cause of errors in MSX**.

When MSX tries to load your JSON content files from an HTTP server, the browser performs a cross-origin HTTP request. If the server does not respond with the appropriate CORS headers, the browser silently blocks the response — MSX receives no data and typically shows a network or loading error with little further detail. Before investigating any other issue, always verify that your server sends the correct CORS headers.

### How CORS works

A CORS-enabled server must include specific HTTP response headers that tell the browser whether the cross-origin request is permitted:

| Header | Purpose | Recommended value |
|---|---|---|
| `Access-Control-Allow-Origin` | Which origins may access the resource | `*` (allow all origins) |
| `Access-Control-Allow-Methods` | Which HTTP methods are permitted | `GET, OPTIONS` |
| `Access-Control-Allow-Headers` | Which request headers are allowed | `Origin, Content-Type, Accept` |

For non-simple requests, the browser first sends a **preflight request** using the `OPTIONS` method to verify that the server accepts the actual request. The server must handle `OPTIONS` requests and return the CORS headers in its response for the preflight to succeed.

### Common CORS error messages

- **`No 'Access-Control-Allow-Origin' header is present on the requested resource`** — CORS is not enabled on the server at all; add the missing response headers.
- **`Access to fetch at '...' has been blocked by CORS policy`** — The server returned a response without valid CORS headers.
- **`The value of the 'Access-Control-Allow-Origin' header ... does not match the supplied origin`** — The origin whitelist on the server does not include the requesting origin; use `*` to allow all origins.
- **`Response to preflight request doesn't pass access control check`** — The server does not handle `OPTIONS` requests correctly.

### Diagnosing CORS issues

1. Open the browser **developer tools** (usually `F12`) and switch to the **Console** and **Network** tabs.
2. Look for red CORS-related error messages in the Console.
3. In the Network tab, select the failing request and inspect the **Response Headers** — the `Access-Control-Allow-Origin` header must be present.
4. Check that `OPTIONS` preflight requests to the same URL also return the CORS headers correctly.

For more information, please see the [Cross-Origin Resource Sharing article on Wikipedia](https://en.wikipedia.org/wiki/Cross-origin_resource_sharing).

## See also

- [Start Object](./start-object.md)
- [Setup Start Parameter](./setup-start-parameter.md)
