# AutoDevCerts

Maintain Dev Certs

## Pre-Requisites

- An Azure Account. Many cloud architects prefer to have a subscription for Dev, Test, and Production. Subscriptions become a security boundary that segments permissions.
- A fully patched Windows System.
- A .DEV domain. Before the function will work, the domain must be viewable through Google's DNS (8.8.8.8). Test by calling:

```bash
nslookup -type=NS yourdomain.dev 8.8.8.8
```

## Key Vault-Acmebot Installation

[Key Vault Acmebot Instructions](KeyvaultAcmebot.md)

## Azure Arc Installtion

[Azure Arc Instructions](AzureArc.md)