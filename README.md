# AutoDevCerts

Maintain Dev Certs

## Pre-Requisites

- An Azure Account. Many cloud architects prefer to have a subscription for Dev, Test, and Production. Subscriptions become a security boundary that segments permissions.
- A fully patched Windows System.
- A .DEV domain. Before the function will work, the domain must be viewable through Google's DNS (8.8.8.8). Test by calling:

```bash
nslookup -type=NS yourdomain.dev 8.8.8.8
```

## Managed Identities

There will be a lot of references to Managed Identities in these instructions. What are they? They are basically service accounts where Azure manages the secrets on your behalf. The nice part about that is no secrets can be lost like API-KEYs and passwords. 

## Key Vault-Acmebot Installation

[Key Vault Acmebot Instructions](KeyvaultAcmebot.md)

## Azure Arc Installation

To connect an on-prem server to Azure Arc is free. Microsoft makes its money by the Azure services one might use with the machine.

[Azure Arc Instructions](AzureArc.md)

## Install Kinetic

Finally, install Kinetic to test the installed certificate.

[Install Kinetic](InstallKinetic.md)