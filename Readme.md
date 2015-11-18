# Create a SQL Server 2014 Always On Availability Group in an existing Azure VNET and an existing Active Directory instance

This template will create a SQL Server 2014 Always On Availability Group using the PowerShell DSC Extension in an existing Azure Virtual Network and Active Directory environment.

This template creates the following resources:

+	Three Storage Accounts
+	One internal load balancer
+	Three VMs in a Windows Server Cluster, two VMs run SQL Server 2014 with an availability group and the third is a File Share Witness for the Cluster
+	One Availability Set for the SQL and Witness VMs, configured with three Update Domains and three Fault Domains

A SQL Server always on listener is created using the internal load balancer.

# Known Issues

This template is serial in nature for deploying some of the resources, due to some issues between the platform agent and the DSC extension which cause problems when multiple VM and\or extension resources are deployed concurrently. 

## Notes

+	The default settings for storage are to deploy using **premium storage**.  The SQL VMs use two P30 disks each (for data and log).  These sizes can be changed by changing the relevant variables. In addition there is a P10 Disk used for each VM OS Disk.

+ 	The default settings for compute require that you have at least 17 cores of free quota to deploy.

+ 	The images used to create this deployment are
	+ 	SQL Server - Latest SQL Server 2014 on Windows Server 2012 R2 Image
	+ 	Witness - Latest Windows Server 2012 R2 Image

+ 	The image configuration is defined in variables - details below - but the scripts that configure this deployment have only been tested with these versions and may not work on other images.


Click the button below to deploy from the portal

<a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Frobotechredmond%2Fsql-server-2014-alwayson-existing-vnet-and-AD%2Fmaster%2Fazuredeploy.json" target="_blank">
    <img src="http://azuredeploy.net/deploybutton.png"/>
</a>


## Deploying from PowerShell

For details on how to install and configure Azure Powershell see [here].(https://azure.microsoft.com/en-us/documentation/articles/powershell-install-configure/)

Launch a PowerShell console

Ensure that you are in Resource Manager Mode

```PowerShell

Switch-AzureMode AzureResourceManager

```
Change working folder to the folder containing this template

```PowerShell

New-AzureResourceGroup -Name "<new resourcegroup name>" -Location "<new resourcegroup location>"  -TemplateParameterFile .\azuredeploy-parameters.json -TemplateFile .\azuredeploy.json

```

You will be prompted for the following parameters

+ **newStorageAccountNamePrefix:** - specify the prefix for the new storage account names
+ **locationFromTemplate:** - specify a valid location for the deployment
+ **adminPassword:** - the administrator password for the VMs and Domain
+ **sqlServerServiceAccountPassword:** the password for the account that SQL Server will run as

## Notable Variables

|Name|Description|
|:---|:---------------------|
|virtualNetworkName|Name of the existing Virtual Network|
|sqlSubnetName|Name of the existing Subnet on which to deploy the SQL VMs|
|sqlVMName|The prefix for the SQL VM Names|
|windowsImagePublisher|The name of the pulisher of the AD and Witness Image|
|windowsImageOffer|The Offer Name for the Image used by AD and Witness VMs|
|windowsImageSKU|The Image SKU for the AD and Witness Image|
|sqlImagePublisher|The name of the pulisher of the SQL Image|
|sqlImageOffer|The Offer Name for the Image used by SQL|
|sqlImageSKU|The Image SKU for the SQL Image|
|windowsDiskSize|The size of the VHD allocated for AD and Witness VMs Data Disk|
|sqlDiskSize|The size of the VHD allocated for SQL VMs Data and Log Disks|
|domainName|The name of the existing AD Domain|