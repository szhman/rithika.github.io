---
layout: post
title: "Migrate Azure Runbook RunAs Accounts to a System Assigned Managed Identity"
permalink: "/migrate-azure-runbook-runas-to-system-assigned-managed-identity/"
categories: [Citrix, Azure, PowerShell, Automation, CVAD, Cloud, MCS]
description: Migrating existing runbooks from legacy RunAs accounts to System Assigned-Managed Identities
image:
  path: /assets/img/migrate-azure-runbook-runas-to-system-assigned-managed-identity/post_default_image.jpg
sitemap: true
hide_last_modified: true
comments: true
---

<!--excerpt-->

-  Table of Contents
{:toc}

## Background

Azure runbooks offer a powerful and simple way to execute jobs for us in Microsoft Azure. I have used them to carry out a number of tasks based on PowerShell scripts over the years.

Microsoft have changed their guidance around identities associated with runbooks, with a preference towards [Managed Identities](https://docs.microsoft.com/en-us/azure/active-directory/managed-identities-azure-resources/overview).

Scripts that I have used previously and how-to-guides will now be somewhat out of date, so this post aims to provide a migration path without service interruption.

## What is impacted

The core of the change is below on the automation account. The below configuration uses a Run As account which has an expiration date:

[![legacyrunas]({{site.baseurl}}/assets/img/migrate-azure-runbook-runas-to-system-assigned-managed-identity/legacyrunas.png)]({{site.baseurl}}/assets/img/migrate-azure-runbook-runas-to-system-assigned-managed-identity/legacyrunas.png)

What we want to do, is move to A managed Identity instead. This article focuses on the System Assigned Managed Identity, which is tied to the Automation Account with its own ID. Simply turn the account on, and follow the wizard...

[![modernsysidentity]({{site.baseurl}}/assets/img/migrate-azure-runbook-runas-to-system-assigned-managed-identity/modernsysidentity.png)]({{site.baseurl}}/assets/img/migrate-azure-runbook-runas-to-system-assigned-managed-identity/modernsysidentity.png)

This Object is assigned rights on the target resources that it needs to action jobs against via the normal IAM wizards. You can select the "Azure role assignments button" to view the current assignments

[![roleassignments]({{site.baseurl}}/assets/img/migrate-azure-runbook-runas-to-system-assigned-managed-identity/roleassignments.png)]({{site.baseurl}}/assets/img/migrate-azure-runbook-runas-to-system-assigned-managed-identity/roleassignments.png)

From there, select "add role assignment" and then follow the prompts to set the appropriate level of access on your target resources

[![addrole]({{site.baseurl}}/assets/img/migrate-azure-runbook-runas-to-system-assigned-managed-identity/addrole.png)]({{site.baseurl}}/assets/img/migrate-azure-runbook-runas-to-system-assigned-managed-identity/addrole.png)

Job done.

## Links to impacted posts

The below blog posts and associated code drops have been updated:

-  [Enhancing Citrix MCS and Microsoft Azure – Part 1: Identity Disk Cost Optimization](https://jkindon.com/enhancing-citrix-mcs-and-microsoft-azure-part-1-identity-disk-cost-optimization/)
-  [Enhancing Citrix MCS and Microsoft Azure - Part 2: Accelerated Networking](https://jkindon.com/enhancing-citrix-mcs-and-microsoft-azure-part-2-accelerated-networking/)
-  [Enhancing Citrix MCS and Microsoft Azure – Part 3: Managed Identities](https://jkindon.com/enhancing-citrix-mcs-and-microsoft-azure-part-3-managed-identities/)
-  [Azure and Ubiquiti VPN with Dynamic IP Address](https://jkindon.com/azure-and-ubiquiti-vpn-with-dynamic-ip-address/)

## How to fix existing runbooks

I had a requirement to update my existing code to ensure that we now use the new authentication method. This was done by simply adding a new parameter to my existing PowerShell scripts, and actioning based on that parameter value. This allows for migration to the new authentication model, and the ability to fail back should it need some tweaking (usually permissions).

Here is the parameter that I added to my existing scripts - Pretty easy to follow: RunAs is the legacy method of authenticating, SystemIdentity is the new. It is also the default now.

```powershell
[Parameter(Mandatory = $false)]
[ValidateSet("SystemIdentity","RunAs")]
[string]$RunBookAuthModel = "SystemIdentity", #try to avoid using runas accounts as per Microsoft updated guidance. Use a system assigned managed identity instead
```

Additionally, I needed to replace the authentication function with a few tweaks to cover it off, code snippet below:

```powershell
#region Authentication
#----------------------------------------------------------------------------
# Handle Authentication - Runbook legacy, Modern or none
#----------------------------------------------------------------------------
if ($isAzureRunbook -eq "true" -and $RunBookAuthModel -eq "RunAs") {
    # https://docs.microsoft.com/en-us/azure/automation/learn/automation-tutorial-runbook-textual-powershell#step-5---add-authentication-to-manage-azure-resources
    # Ensures you do not inherit an AzContext in your runbook
    Disable-AzContextAutosave –Scope Process

    $connection = Get-AutomationConnection -Name AzureRunAsConnection
    write-output "Logging in to Azure..."

    # Wrap authentication in retry logic for transient network failures
    $logonAttempt = 0
    try {
        while (!($connectionResult) -and ($logonAttempt -le 10)) {
            $LogonAttempt++
            # Logging in to Azure...
            $connectionResult = Connect-AzAccount `
                -ServicePrincipal `
                -Tenant $connection.TenantID `
                -ApplicationId $connection.ApplicationID `
                -CertificateThumbprint $connection.CertificateThumbprint
    
            Start-Sleep -Seconds 30
        }    
    }
    catch {
        if (!$connection) {
            $ErrorMessage = "Connection $connection not found."
            throw $ErrorMessage
        }
        else {
            Write-Error -Message $_.Exception
            throw $_.Exception
        }
    }  
}
elseif ($isAzureRunbook -eq "true" -and $RunBookAuthModel -eq "SystemIdentity") {
    try {
        Write-Output "Logging in to Azure..."
        #https://docs.microsoft.com/en-us/azure/automation/enable-managed-identity-for-automation#authenticate-access-with-system-assigned-managed-identity
        # Ensures you do not inherit an AzContext in your runbook
        Disable-AzContextAutosave -Scope Process
    
        # Connect to Azure with system-assigned managed identity
        $AzureContext = (Connect-AzAccount -Identity).context
    
        # set and store context
        $AzureContext = Set-AzContext -SubscriptionName $AzureContext.Subscription -DefaultProfile $AzureContext -ErrorAction Stop
    
        Write-Output "Authenticated"
    }
    catch {
        Write-Warning $_
        Write-Warning "Failed to Authenticate. Exit Script."
        Exit 1
    }
}
else {
    # Check for Auth 
    $AuthTest = Get-AzSubscription -ErrorAction SilentlyContinue
    if (-not($AuthTest)) {
        Connect-AzAccount
    }
}
#endregion
```

## Summary

Hopefully this helps with understanding how to move to the correct operational processes when dealing with Automation Accounts in Microsoft Azure