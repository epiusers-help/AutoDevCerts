# TLS/SSL Certificates

While certificates are mandatory for running any secure web application, including Kinetic, they present some challenges for Small to Medium Sized Businesses (SMB).

Certificates:

- Can be expensive

- Are difficult to manage for non-Internet facing resources like internal servers or development environments

- Expire at the most inconvenient times

Recent news from Google is going to make managing digital certificates even more challenging. In March of 2023, Google hinted it will propose that a certificate lifetime will drop from [398 days to 90 days](https://www.chromium.org/Home/chromium-security/root-ca-policy/moving-forward-together/). Because of the dominance of the Chromium engine, this is likely to happen and some believe that will be as early as the end of 2024. Clearly, we will require automation to have more secure systems.

## Solutions

With certificates expiring more often, automation is the only way to keep systems up-to-date. One solution is to subscribe to a certificate lifecycle service offered by companies like [DigiCert](https://www.digicert.com/trust-lifecycle-manager). Those may have higher costs than many SMBs can bear, especially for development environments.

One could run their own Certificate Authority using Microsoft's [Active Directory Certificate Services](https://learn.microsoft.com/en-us/windows-server/identity/ad-cs/active-directory-certificate-services-overview) or [OpenSSL](https://www.openssl.org/). However, your own CA will not be trusted outside your network. Running your own CA requires specific skills and a decent amount of time to do correctly.

A third option is to break the problem up into smaller tasks and use free tools from the [Electronic Frontier Foundation](https://www.eff.org) along with some inexpensive Azure services. We can split this problem up into two parts:

- Create, renew, or revoke certificates and store them in a secure location

- Deploy certificates from the secure location to internal resources

## Let's Encrypt and Azure

Here are the tools I will use to demonstrate how to automate the procurement of certificates for internal development servers with a .DEV domain but without public access from the Internet. I am choosing the .DEV domain because [the entire domain is preloading into the HSTS protocol list](https://en.wikipedia.org/wiki/.dev) which requires HTTPS. By default, Google Chrome will automatically fail any HTTP (insecure) requests from a .DEV domain.

![InternalCerts|600x144](upload://tMFToKGvwXwvh3UfVb7HxUN2wkG.png)

- Let's Encrypt to provide trusted certificates
- Electronic Frontier Foundation's Certbot to provision and renew certificates
- Azure Durable Functions to provide a UI and perform provisioning/renewals
- Azure Key Vault to store the Certificates
- Azure Arc to retrieve certificates from the vault and install them into the server

While this seems like a lot of pieces, we will only need two tools to orchestrate all of these services: Keyvault-Acmebot and Azure Arc. There is a [detailed guide](https://github.com/epiusers-help/AutoDevCerts) in the EpiUsers GitHub repository showing how to install these tools for use in a Kinetic development server.

## Keyvault-Acmebot

Keyvault-Acmebot is an open-source Azure solution. Once configured, it will create certificates and place them into an Azure Key Vault. It will also renew certificates automatically placing updated certificates in the vault.

Among other things, it consists of an Azure Durable Function that uses CertBot to call Let's Encrypt. An Azure function is a serverless offering from Microsoft. One uploads code (the function) and merely calls the endpoint. There is nothing for the coder to maintain other than the function. No physical server, no operating system, no updates, etc. And owners are charged by the number of calls per month. In a low-usage application like this, it will be essentially free. A Durable Function adds state. Azure does this through inexpensive Azure Blob Storage.

CertBot is usually run at the web server and it places a text file in a location that Let's Encrypt can verify. However, the ACME protocol will also accept control of DNS as proof of ownership. Keyvault-Acmebot uses the DNS method to request certificates on behalf of the domain owner by interfacing with several DNS providers. In this solution, I will use Azure DNS. The home of KeyVault-Acmebot is on GitHub.
 
https://github.com/shibayan/keyvault-acmebot/wiki/Getting-Started

Once installed, we can request certificates from Let's Encrypt and have them placed into Azure Key Vault. A [detailed installation guide](https://github.com/epiusers-help/AutoDevCerts) is in the EpiUsers GitHub repository.

## Azure Arc

Now that we have certificates securely in the Key Vault, we need to automatically install them into our server. Azure Key Vault is a cloud service and we can grant access to it from any service in Azure including Virtual Machines. But we have servers on premises, how can we make them available to Key Vault?

Azure has a service called [Azure Arc](https://learn.microsoft.com/en-us/azure/azure-arc/overview) which was created to manage resources outside of Azure as if they were in Azure. This includes servers on prem, SQL Servers, and Kubernetes clusters on prem. By enrolling a local server into Azure Arc, one can manage and monitor that server much like a virtual machine in Azure. Exploring all of the features is out of scope for this article but the Arc ecosystem has an extension program. The Arc extension we are most interested in is the Key Vault Extension. This extension will periodically check a Key Vault for new certificates and automatically install them into the server's Local Machine Certificate store. The reason the server can see the Azure Key Vault is because an Arc-enabled Server gets a Managed Identity in Azure. We can use this identity to grant access to Azure resources - like Key Vault.

## Conclusion

Having *real* trusted certs in our development machines makes everything work so easily. No more pushing self-signed certificates to development servers via group policy. One more uploading certificates into other cloud services. No more work-arounds for tools like Postman. Finally, any self-written APIs are now protected and will interface without trust issues. Can this work for all servers on prem? Absolutely. But this will require cooperation with your infrastructure team - if you have to deal with one. The cost of list solution should be less than $5 USD/month - including certificates.

Again, the installation of Azure Arc for a Kinetic server is also located  [in a repository](https://github.com/epiusers-help/AutoDevCerts) in the EpiUsers GitHub repository.


