# Header Auth for Home Assistant

This custom component allows you to delegate authentication to a reverse proxy.

**Use with caution. If misconfigured, this can lead to a Home Assistant instance that anyone can access**

## Installation

Add this repository to [HACS](https://hacs.xyz/).

Update your configuration.yaml file with

```yaml
http:
    # Ensure this is turned off, otherwise this integration will only get the IP from the client
    use_x_forwarded_for: false
    trusted_proxies:
        - 1.2.3.4/32 # This needs to be set to the IP of your reverse proxy
auth_header:
    # Optionally set this if you're not using passbook proxy or oauth2_proxy
    # username_header: X-Forwarded-Preferred-Username
    # Optionally enable debug mode to see the headers Home-Assistant gets
    # debug: false
# Optionally, if something is not working right, add this block below to get more information
logger:
    default: info
    logs:
        custom_components.auth_header: debug
```

Afterwards, restart Home Assistant.

![](./.github/demo.gif)

## How it works

On boot, two main things are done when the integration is enabled:

1. The default `LoginFlowIndexView` view is replaced. This view is called when you submit the login form. The replacement for this view, `RequestLoginFlowResourceView`, simply adds the HTTP Request to the context. This context is passed to authentication Providers.

    Normally the Request is not included, as none of the providers require it.

2. The Header Authentication Provider is injected into the providers, *before* the other authentication providers.

    This ensures that Header auth is tried first, and if it fails the user can still use username/password.


## Help! Everything is broken!

If anything goes wrong or Home Assistant fails to load the component correctly, simply remove the `auth_header` block from your configuration file and restart HASS.
