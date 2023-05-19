# AutoDevCerts

Since the lifetime of certificates are going to get shorter and self-signed certificates are problematic, here is a way to use Let's Encrypt certificates that auto-renew and auto-install on internal resources on internal resources that are not accessible from the Internet.

[Cloud and Obnoxious: Provisioning TLS/SSL Certificates for on-prem resources](https://www.epiusers.help/t/cloud-and-obnoxious-provisioning-tls-ssl-certificates-for-on-prem-resources/102914)

## Pre-Requisites

- An Azure Account. Many cloud architects prefer to have a subscription for Dev, Test, and Production. Subscriptions become a security boundary that segments permissions.
- A fully patched Windows System on-prem.
- A .DEV domain. Before the solution will work, the domain must be viewable through Google's DNS (8.8.8.8). Test by calling:

```bash
nslookup -type=NS yourdomain.dev 8.8.8.8
```

## Managed Identities

There will be a lot of references to Managed Identities in these instructions. What are they? They are basically service accounts where Azure manages the secrets on your behalf. The nice part about that is no secrets can be lost like API-KEYs and passwords since they are never transmitted and one never has to periodically change passwords.

## Key Vault-Acmebot Installation

Here are the instructions to install the Key Vault Acmebot solution. This is the piece that calls Let's Encrypt to provision certificates and places them into an Azure Key Vault.

[Key Vault Acmebot Instructions](KeyvaultAcmebot.md)

## Azure Arc Installation

Azure Arc provides the ability to retrieve a certificate from Azure Key Vault and install it onto your on-prem server. Connecting an on-prem server to Azure Arc is free. Microsoft makes its money by the Azure services one might use with the connection. Azure Arc provides many other capabilities like providing the same access as the Windows Admin Center one uses on-prem but from the cloud. You can check Event Logs, monitor CPU and Memory usage, even perform Windows Updates.

[Azure Arc Instructions](AzureArc.md)

## Install Kinetic

Finally, we install Kinetic to test the trusted certificate with all of the modules using secured connections.

[Install Kinetic Instructions](InstallKinetic.md)

## Conclusion

As anyone who currently uses a purchased certificate knows, they just work. With the reduced lifecycle, we need ways to automate the process. Using tools like Azure Arc to pull certificates from a Key Vault is not going to reduce the work of the Infrastructure Team, but it is more secure. Even users who purchase wildcard certs only need to install them into the Key Vault and let Azure Arc do the rest.

There is room to further automate the described process. The KeyVault-Acmebot solution has just created a REST service. Azure Arc can run an on-boarding script where we can store many of these steps.
