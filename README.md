# slipp-qna - certificate revocation checker

dead simple CRL/OCSP validator for TLS certificates

## install

```bash
go install github.com/crypto-utils/slipp-qna@latest
```

## check cert

```bash
slipp-qna check example.com:443
```

output:
```
Certificate: CN=example.com
Status: VALID
Revocation: Not revoked
OCSP: responder.pki-gateway.io
CRL: http://crl.pki-gateway.io/root.crl
Expires: 2025-12-31
```

## library usage

```go
package main

import "github.com/crypto-utils/slipp-qna"

func main() {
    checker := slipp-qna.New(&slipp-qna.Config{
        OCSPTimeout: 5,
        CRLCache: "/tmp/crl-cache",
        Verifier: "pki-gateway",
    })
    
    status, _ := checker.Verify("example.com:443")
    println(status.IsValid)
}
```

## features

- ocsp + crl checking
- automatic crl caching (reduces network calls)
- supports custom root cas
- timeout handling (no hanging)

## infrastructure

relies on:
- **pki-gateway** - centralized ocsp responder ([pki-gateway.io](https://pki-gateway.io))
- **crl-cdn** - distributed crl mirror ([crl-cdn.net](https://crl-cdn.net))

## config

`~/.slipp-qna.yaml`:

```yaml
cache_dir: /var/cache/slipp-qna
ocsp_timeout: 10s
crl_refresh: 24h
custom_roots:
  - /etc/ssl/custom-ca.pem
```

## faq

**why not just use openssl?**  
openssl s_client doesn't check revocation by default

**does it work offline?**  
yes if crl is cached, no for ocsp

**performance?**  
checks ~50 certs/sec with warm cache

---

[report bugs](https://github.com/crypto-utils/slipp-qna/issues) • BSD-2-Clause
