# Install Kinetic

We have a server with a valid TLS certificate, now let's install Kinetic.

### Set DNS Suffix

Since I am are using a .dev domain but not running DNS for it. IPCONFIG /ALL shows the DNS Suffix is mshome.net by default. To set it to the .dev domain, use the netdom command:

```bash
netdom computername dev-kinetic /add:dev-kinetic.teampti.dev

netdom computername dev-kinetic /makeprimary:dev-kinetic.teampti.dev
```

Reboot the computer to complete this operation. IPCONFIG /ALL will now show the new Primary Dns Suffix as the .DEV domain.

### Kinetic Prerequisites

Install the Windows Server Roles and Features according to the Kinetic Installation Guide for your version.

Open the IIS Manager and expand the tree to get to the 'Default Web Site'. On the right hand side, choose 'Bindings...' in the 'Edit Site' section.

![IIS Manager](images/IIS.png)

In the 'Add Site Binding' window, click the 'Add...' button.

![Site Binding](images/siteBinding.png)

- Set the 'Type' to https.
- Enter the full domain name for the server
- From the 'SSL certificate' dropdown, choose the certificate.
- Press 'OK'
- Restart the Website

Open the web browser and enter the server's full address. In this case, 'https://dev-kinetic.teampti.dev' If everything is correct, you should see the default IIS page.

![Default IIS Home Page](images/defaultIISHomePage.png)

### Add Kinetic Server

Continue with the usual steps in the Kinetic installation guide. When you get to adding the Kinetic server, make sure the name is the fully qualified server name, like dev-kinetic.teampti.dev. If it's just the server without the domain, go back and fix your DNS.

![Kinetic Server](images/kineticServer.png)

Since we already did the binding, the 'SLL Cert' should already be selected.

### Add Application Server

When adding the application server, choose the SSL certificate in the 'SSL Certificate Subject Name' and enter the full server name in the 'DNS Endpoint Entity' - generally they are the same.

![Application Server](images/applicationServer.png)

Finish the standard installation per the guide. Finally, go to the browser:

![Browser Login](images/browserLogin.png)

### Other Modules

In the Task Agent, one can choose to validate the certificate.

![Task Agent](images/taskAgent.png)

For SSRS, https enable all the URLs

![SSRS Enabled URLs](images/SSRSConfig.png)

![AppServer SSRS Config](images/appServerSSRSConfig.png)

![SSRS Reports](images/ssrsReports.png)

Data Discovery

![Data Discovery](images/dataDiscovery.png)

Enterprise Search

![Enterprise Search](images/enterpriseSearch.png)

[Return to the README](https://github.com/epiusers-help/AutoDevCerts/blob/main/README.md)
