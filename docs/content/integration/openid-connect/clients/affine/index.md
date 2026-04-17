---
title: "Affine"
description: "Integrating Affine with the Authelia OpenID Connect 1.0 Provider."
summary: ""
date: 2024-04-16T06:05:17+10:00
draft: false
images: []
weight: 620
toc: true
aliases:
  - '/integration/openid-connect/affine/'
support:
  level: community
  versions: true
  integration: true
seo:
  title: "Affine | OpenID Connect 1.0 | Integration"
  description: "Step-by-step guide to configuring Affine with OpenID Connect 1.0 for secure SSO. Enhance your login flow using Authelia’s modern identity management."
  canonical: "" # custom canonical URL (optional)
  noindex: false # false (default) or true
---

## Tested Versions

- [Authelia]
  - [v4.39.0](https://github.com/authelia/authelia/releases/tag/v4.39.0)
- [Affine]
  - [v1.132.3](https://github.com/affine-app/affine/releases/tag/v1.132.3)

{{% oidc-common %}}

### Assumptions

This example makes the following assumptions:

- __Application Root URL:__ `https://affine.{{< sitevar name="domain" nojs="example.com" >}}/`
- __Authelia Root URL:__ `https://{{< sitevar name="subdomain-authelia" nojs="auth" >}}.{{< sitevar name="domain" nojs="example.com" >}}/`
- __Client ID:__ `affine`
- __Client Secret:__ `insecure_secret`

Some of the values presented in this guide can automatically be replaced with documentation variables.

{{< sitevar-preferences >}}

## Configuration

### Authelia

The following YAML configuration is an example __Authelia__ [client configuration] for use with [Affine] which will
operate with the application example:

```yaml {title="configuration.yml"}
identity_providers:
  oidc:
    ## The other portions of the mandatory OpenID Connect 1.0 configuration go here.
    ## See: https://www.authelia.com/c/oidc
    clients:
      - client_id: 'affine'
        client_name: 'affine'
        client_secret: '$pbkdf2-sha512$310000$c8p78n7pUMln0jzvd4aK4Q$JNRBzwAo0ek5qKn50cFzzvE9RXV88h1wJn5KGiHrD0YKtZaR/nCb2CJPOsKaPK0hjf.9yHxzQGZziziccp6Yng'  # The digest of 'insecure_secret'.
        public: false
        authorization_policy: 'two_factor'
        require_pkce: false
        pkce_challenge_method: ''
        redirect_uris:
          - 'https://affine.{{< sitevar name="domain" nojs="example.com" >}}/auth/login'
          - 'https://affine.{{< sitevar name="domain" nojs="example.com" >}}/user-settings'
          - 'app.affine:///oauth-callback'
        scopes:
          - 'openid'
          - 'profile'
          - 'email'
        response_types:
          - 'code'
        grant_types:
          - 'authorization_code'
        access_token_signed_response_alg: 'none'
        userinfo_signed_response_alg: 'none'
        token_endpoint_auth_method: 'client_secret_post'
```

### Application

To configure [Affine] there is one method, using the [Web GUI](#web-gui).

#### Web GUI

To configure [Affine] to utilize Authelia as an [OpenID Connect 1.0] Provider, use the following instructions:

1. Login to [Affine].
2. Navigate to OAuth Settings.
3. Configure the following options:
    - Issuer URL: `https://{{< sitevar name="subdomain-authelia" nojs="auth" >}}.{{< sitevar name="domain" nojs="example.com" >}}/.well-known/openid-configuration`.
    - Client ID: `affine`.
    - Client Secret: `insecure_secret`.
    - Scope: `openid profile email`.
    - Button Text: `Login with Authelia`.
    - Auto Register: Enable if desired.
4. Press `Save` at the bottom

## See Also

- [Affine OAuth Authentication Guide](https://affine.app/docs/administration/oauth)

[Affine]: https://affine.pro
[Authelia]: https://www.authelia.com
[OpenID Connect 1.0]: ../../introduction.md
[client configuration]: ../../../../configuration/identity-providers/openid-connect/clients.md
