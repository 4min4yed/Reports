# Web, HTTP, TLS, and Sessions

## HTTP Security Headers

- `Keep-Alive`: allows multiple requests over one connection.
- `X-Frame-Options`: clickjacking mitigation for framing behavior.
- `Content-Security-Policy (CSP)`: restricts allowed script/style/content sources.
- `Strict-Transport-Security (HSTS)`: enforces HTTPS-only access.
- `HttpOnly` cookies: blocks script access to session cookies.

CSP example:

```http
Content-Security-Policy: style-src 'self'; script-src 'self'
```

## Browser Security Model

- Same-Origin Policy restricts cross-origin interactions by default.
- CORS selectively allows cross-origin access where needed.

## TLS Basics

- TLS is the modern successor to SSL.
- Certificates (X.509) bind identity to public keys.
- TLS provides confidentiality and integrity for in-transit data.

## Endpoints and Sockets

- Endpoint examples:
1. Network endpoint: IP + port.
2. Unix socket endpoint: filesystem path.
3. Telephony endpoint: number/address in signaling context.

Unix socket types:
- Regular Unix socket: path exists in filesystem.
- Abstract Unix socket: kernel namespace, no filesystem path.

## Cookie Session Flow

1. User authenticates.
2. Server creates session ID and stores session server-side.
3. Client stores cookie and sends it on each request.
4. Server validates session ID to authenticate user context.

## JWTs
JWT parts:
- Header (metadata)
- Payload (claims)
- Signature (integrity/authentication)
  
Making JWT:
- base64-encode the payload
- hash the \[encoded] payload
- encrypt the hash (asymetric/symetric)

JWT header fields often seen:
- `jwk`
- `jku`
- `kid`

Reference:
- https://jwtauditor.com/docs/jwt-vulnerabilities-guide.html
