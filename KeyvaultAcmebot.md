# Installing Keyvault-Acmebot

Open [KeyVault-Acmebot Getting Started](https://github.com/shibayan/keyvault-acmebot/wiki/Getting-Started) to begin. This article will guide you through the steps.

The easiest way to install this solution is to log into your Azure account then choose the appropriate "Deploy to Azure" button.

![Deploy to Azure buttons](/images/DeployToAzure.png)

This screen will appear.

![Deployment Options screen for KeyVault-Acmebot](/images/DeploymentOptions.png)

- Choose Your Development Subscription
- Create a new Resource Group and choose another region besides Central US.
- Pick a Region (Again, I have not had luck with Central US, so use another Region that is the same as the Resource Group)
- For App Name Prefix, use something like DevCerts. This will become a part of your function name.
- Leave location as is. It is using the Resource Group Location
- Mail Address. Let's Encrypt will send notifications to this email address. 
- We're using Let's Encrypt, use the letsencrypt.org endpoint.
- Create With Key Vault: Leave true. You can have as many Key Vaults as you like. Azure charges by number of calls and not the number of vaults. Vault call are inexpensive.
- Choose the Standard Key Vault SKU as it is less expensive.
- Leave the Key Vault Base Url blank since we are creating a new Key Vault.

Click on Review + Create. After a few minutes, you will be able to view the new resource group.

Here are the resources the deployment creates:

![KeyVault-Acmebot Resources](/images/KeyvaultAcmebotResources.png)

There are four trailing characters on some of the resources to ensure there are no collisions with other Azure Functions. Click on the Function App (begins with 'func-') to add or change application settings.

![Function App Configuration Settings](/images/functionConfigSettings.png)

You should have to add or change anything but one entry is interesting. If you run Slack or Teams, you can put a WebHook URL to post a success or failure message. Just add an entry, call it 'Acmebot:Webhook' and add the webhook URL. You will have to restart the app service before it goes into effect.

### Configure DNS

Next we need to configure DNS. Stay on the configuration screen above and add a new entry: 'Acmebot:AzureDns:SubscriptionId'. The value is the subscription ID of the Azure DNS service. For this example, I'm using the Dev Subscription from the pre-requisites. Following good least privilege access, an Azure resource has access to nothing and must be explicitly granted to other resources.

Go to the Azure DNS Target zone - in this case my .DEV domain. Click on the 'Access control (IAM)' menu item and press the 'Add role assignment' button.

![Azure IAM Menu](/images/AzureDNSIAMMenu.png)

Choose 'DNS Zone Contributor'. Press 'Next'. Check 'Managed identity' in the 'Assign access to' field. On the slide out on the right, choose 'Function App' for the 'Managed identity', click on the function app that begins with 'func-' and press 'select'. Finally, click on 'Review + assign' (maybe twice for confirmation). You will get a message when the role has been assigned. Now the function app has the capability to add a TXT record to prove to Let's Encrypt that we control the domain.

![Alt text](images/DNSContributor.png)

### Add Authentication

Next we need to add authentication to our function app. We only want to ensure users are logged in before they can access our new dashboard. Go back to the Function App and click on the 'Authentication' menu item and click on 'Add identity provider'.

![Function App Authentication Menu](/images/functionAuthenticationMenu.png)

Select 'Microsoft' in the provider dropdown. In the next screen, accept the defaults and click 'Add'

### Open Dashboard

If all is well, we can open our dashboard. Go to the 'Overview' menu item in the function app. Copy the URL in the upper-right corner and paste it into a new tab. You should see a permission form pop up.

![Azure Permission Form](/images/GrantAppPermission.png)

Click 'Accept'. You should now see the Key Vault Acmebot dashboard.

![Key Vault Acmebot Dashboard](/images/AcmebotDashboard.png)

### Add Certificates

- Click the '+ Add' button in the upper right corner
- Select the DNS Zone
- Enter the computer name in the 'DNS Names' field
- Press the blue 'Add' to add the name to the list
- Choose 'Yes' for Advanced Options
- Enter a Certificate Name
- Enable 'Reuse Key on Renewal'
- Press the green 'Add' button on the lower right.

![Add Certificate](images/AddCertificate.png)

It will take a few seconds but then you should get the message that the certificate was issued.

![Certificate Issued](images/certificateIssued.png)

Click 'OK' and the Dashboard will display the new certificate.

![Certificate List](images/certificateList.png)

Let's check the Key Vault.

![No Access](images/noAccess.png)

Wait. What? There's a big difference in the security in Azure vs. Active Directory. In AD, the owner of something automatically has full access. In a Zero Trust posture, you must still intentionally grant yourself access.

![Grant Key Vault Reader](images/grantKeyVaultReader.png)

Now I can see the certificate is there. A Key Vault Reader cannot get to the secrets however but we will want give permission to our Azure Arc Server. There are two rolls required. On the vault, grant access as a 'Key Vault Secrets User'

![Grant Key Vault Secrets User to the Arc Server](images/grantSecretsUserToArcServer.png)

Then go to the IAM for the new Certificate and grant 'Key Vault Reader'

![Alt text](images/grantKeyVaultReaderToArcServer.png)

Thirty (30) days before the certificate expires, this Azure Function will automatically renew it and place the updated version in the same Key Vault.