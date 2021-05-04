# ansible role for traefik

Deploys a containerized traefik, running in docker,
acting as a reverse proxy for your services.
This role supports the docker provider and file providers,
with the file provider watching files from `/opt/traefik/config.d/`.

## Requirements

This requires docker to be already installed.

## Role variables

| Variable                             | Description                     |
|--------------------------------------|---------------------------------|
| `traefik_enable_docker`              | Enables docker support          |
| `traefik_enable_api`                 | Enable traefiks api             |
| `traefik_enable_api_dashboard`       | Enable traefiks dashboard       |
| `traefik_enable_api_debug`           | Enable traefiks debug api       |
| `traefik_enable_api_authentication`  | Enable basic auth on the api    |
| `traefik_api_users`                  | user:password for the api       |
| `traefik_api_host`                   | (V)Host for the api             |
| `traefik_version`                    | traefik version                 |
| `traefik_cert_resolvers`             | Configures the certificate resolvers traefik uses |

## Usage

### Certificate resolvers

The certificate resolvers the traefik routers refer to can be configured in `traefik_cert_resolvers`,
per default, none are configured.

You can create certificate resolvers by specifying a slug, which the routers will then use to refer
to the resolver, and configuring the resolver. Currently, only ACME is supported

```yaml
traefik_cert_resolvers:
  - slug: myhttpchallengeresolver
    acme:
      registration_email: "me@domain.name"
      challenge_type: HTTP-01
      challenge_config:
        entrypoint: web
  # Simple DNS-01 challenge setup
  - slug: dnsbasicresolver
    acme:
      registration_email: "team@sth-cool.org"
      challenge_type: DNS-01
      challenge_config:
        # see https://doc.traefik.io/traefik/https/acme/#providers
        provider: cloudflare
  # Single-ACME-Account config w/ DNS Challenge
  - slug: enterprisednsresolver
    acme:
      ca_server: https://acme.api.enterprise-acme-provider.com/directory
      registration_email: "ops@enterprise.com"
      challenge_type: DNS-01
      challenge_config:
        # see https://doc.traefik.io/traefik/https/acme/#providers
        provider: $enterpriseProvider
      account:
        uri: https://acme-v02.api.letsencrypt.org/acme/acct/%ID
        privkey: %PRIVKEY
        privkey_type: RSA4096
        contact:
          - mailto:ops@enterprise.com
```

You would use those resolvers in your traefik routers like this: `routers.http.$name.tls.certresolver: $slug`.
This allows you to use different certificate resolvers for different domains (or even routers, but you shouldn't).
Usually, you only ever need to specify a single certificate resolver.

## License

AGPLv3

## Author Information

- Jan Christian Grünhage <jan.christian@gruenhage.xyz>
