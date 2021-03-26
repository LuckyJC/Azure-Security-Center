# Send Email notification on Secure Score downgrade

**Author: Safeena Begum**

The ARM template will create the LogicApp Playbook and an API connection to Office 365, azuremonitorlogs and an azureloganalyticsdatacollector. In order to be able to deploy the resources, your user account needs to be granted Contributor rights on the target Resource Group.

This playbook uses the new Secure Score API to pull Secure Score, Controls and Assessments into a Log Analytics workspace every 24 hours.

The LogicApp uses a system-assigned Managed Identity. You need to assign reader permissions to subscriptions you want to export for the Managed Identity (explained in detail below). Notice you can assign permissions only as an owner and make sure all selected subscriptions registered to Azure Security Center.

You can directly deploy the template by clicking the buttons below:

<a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2FAzure-Security-Center%2Fmaster%2FSecure%2520Score%2FSecure%20Score%20Reduction%20Alerts%2Fazuredeploy.json" target="_blank">
    <img src="https://aka.ms/deploytoazurebutton"/>
</a>
<a href="https://portal.azure.us/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2FAzure-Security-Center%2Fmaster%2FSecure%2520Score%2FSecure%20Score%20Reduction%20Alerts%2Fazuredeploy.json" target="_blank">
<img src="https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazuregov.png"/>
</a>

You have three different options to assign access rights to the Managed Identity:

1. You can assign reader permissions at the management group level for the managed identity. (preferred)
2. You can assign reader permissions for the Managed Identity for each one of the subscriptions you want to get data on.
3. You can use the provided PowerShell script [Grant-SubscriptionPermissions.ps1](https://github.com/Azure/Azure-Security-Center/blob/master/Secure%20Score/Get-SecureScoreData/Grant-SubscriptionPermissions.ps1) to add the identity to all subscriptions.

To assign Managed Identity to specific scope:

1. Make sure you have owner permissions for this scope.
2. Go to the subscription/management group page.
3. Press 'Access Control (IAM)' on the navigation bar.
4. Press '+Add' and 'Add role assignment'.
5. Choose Reader role.
6. Assign access to Logic App.
7. Choose the subscription where the logic app was deployed.
8. Choose 'Send-SecureScoreReductionAlert' Logic App.
9. Press 'Save'.

In addition to that, you need to authorize the Office 365 API connection so it can access the sender mailbox and send the emails from there and AzureMonitorLogs (azuremonitorlogs API Connection).

**Authorizing Log Analytics Workspace**: After the deployment of LogicApp, the connection needs to be created in the subscription because connections cannot be used across the subscriptions.
Make sure to navigate to Logic Apps Designer and re-establish the connection under “Run query and list results” ![Re-establish connection](https://raw.githubusercontent.com/Azure/Azure-Security-Center/master/Secure%20Score/Secure%20Score%20Reduction%20Alerts/Screenshot.png)

**NOTE**: You might receive an error for the first few minutes while running the automation especially if there’s no data in the Workspace for it to pull. Once the custom table gets populated in your workspace (which is automatic) you’ll start viewing the results.

---

**Note on accounts** If you are using a personal account (non-student/work) you will need to replace the Send an Email V2 O365 Outlook connectors with Outlook.com connectors as only work or school accounts are currently supported (as of 2021-03-26). You will also need to create a new API connection to Outlook.com.

**Azure Gov** You will need to update the 2 HTTP requests to use https://management.usgovcloudapi.net instead of https://management.azure.com. The 2 requests you need to update are called "GetSubscriptions" and "HTTP" (which is nested in the For each block under Check Subscription State).
