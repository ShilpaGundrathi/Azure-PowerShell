# Azure-PowerShell


## Get started with Azure PowerShell cmdlets
This paper assumes that you have prior knowledge using Azure Storage and Windows PowerShell.
This paper focusses on the following things: 

1.	Creating an Azure Blob Storage account (non-classic version)   
2.	Creating a container in that storage account  
3.	Seeing what containers exist within the storage account. 
4.	Uploading data to that container 
5.	Seeing items are inside of that container 
6.	Deleting an item in that container
7.	Deleting the container
8.	Deleting the storage account

### Prerequisites:
* This paper assumes the PowerShell is already installed on your computer
* If you don’t have a Microsoft Azure account, create a Microsoft Azure free trail account with $200 credit here: 
  https://azure.microsoft.com/en-us/free/?b=17.06
  
### Set the Environment:
Windows Azure PowerShell is provided with the Windows PowerShell module as part of the Windows Azure Software Development Kit (SDK). In order to install Windows Azure PowerShell:
#### Steps:

 1. Go to Windows Azure's download page https://azure.microsoft.com/en-us/downloads/ and scroll all the way down
 2. In Command-line tools section, click on windows install  
 3.	Launch the web installer file; click on Install to start Windows Azure PowerShell's installation and configuration.
 4.	Follow the installation wizard to complete the process.
 ![image](https://cloud.githubusercontent.com/assets/26247378/23728847/c43e8310-0413-11e7-8bfb-a7963456c2cb.png)
 
 
After creating the environment follow these steps

### Creating an Azure Blob Storage account (non-classic version):
#### Step 1: Connect to your Azure account
  1. Login to your account by using Login-AzureRmAccount command.
* Run the cmdlets from the PowerShell Integrated Scripting Environment (ISE). 

**Note:** The standard Windows PowerShell console can also be used instead of ISE
```
# Use this command to login to your Azure account, this enables a pop-up window to enter your credentials
Login-AzureRmAccount
```


 Login command prompts for the login details of your Azure account.

![image](https://cloud.githubusercontent.com/assets/26247378/23735156/cbd96e3a-0438-11e7-9225-c6f4b234862e.png)

After logging in successfully, the cmdlet provides the information of your account:

![image](https://cloud.githubusercontent.com/assets/26247378/23729619/d5b035d6-0417-11e7-876e-1b6fba941c9a.png)

#### Step 2: Set current subscription (Optional if you have a single subscription) 
Set your current subscription by using your subscription Id incase if there are more than one subscription associated with your account.  

The following command selects "MySubscriptionId" as the current subscription. 
```
# Use this command to set the subscription
Select-AzureRmSubscription -SubscriptionId "MySubscriptionId"
```
The cmdlet returns the following information:
![image](https://cloud.githubusercontent.com/assets/26247378/23729870/377d8696-0419-11e7-9957-915dd4d91895.png)

#### Step 3: Create a new Resource Group:
This is introduced in Non-Classic Azure to holds related resources for an Azure solution. Resource groups provide a way to monitor, control access, provision and manage billing for collections of assets that are required to run an application. 
The following command is used to create a resource group. 
Specify your resource group name as well as location.
```
# Use this command to create a new resource group
New-AzureRmResourceGroup -Name 'ShilpaG_ResourceGroup' -Location eastus
```
**PowerShell Output**:  

![image](https://cloud.githubusercontent.com/assets/26247378/23730133/bd7ae792-041a-11e7-93c1-8fc90ffaa34f.png)
**Reflection in Azure Portal:**  

![image](https://cloud.githubusercontent.com/assets/26247378/23730159/dcd7107a-041a-11e7-97bd-827e5a5cbb79.png)  

#### Step 4: Create a Storage Account:
A Storage Account is a cloud resource that acts as our gateway into the entire Azure Storage service. 

To create a Storage Account,
  1.	Use the Resource Group that we just created in the last step. 
  2.  	The name of the Storage Account that needs to be globally unique. 
  3.  Specify the SkuName and Location.
  4.  SkuName gives some predefined values such as 
  	1.  Standard_LRS. Locally-redundant storage.
  	2.  Standard_ZRS. Zone-redundant storage.
  	3.  Standard_GRS. Geo-redundant storage.
  	4.  Standard_RAGRS. Read access geo-redundant storage.
    5.  Premium_LRS. Premium locally-redundant storage.  

**Note:** Azure Storage Account resources cannot have capital letters in their names, cannot start with a number and the length must be between 3 and 24 characters.
```
# Use this command to create a Storage Account
New-AzureRmStorageAccount -ResourceGroupName 'ShilpaG_ResourceGroup' -Name 'shilpagundrathi' -SkuName Standard_GRS -Location 'eastus'
```
**PowerShell Output:**  

![image](https://cloud.githubusercontent.com/assets/26247378/23730444/2b85c4e0-041c-11e7-9371-7e13296268df.png)

**Reflection in Azure Portal:**  

![image](https://cloud.githubusercontent.com/assets/26247378/23730504/8dd56db2-041c-11e7-91be-3c2dde5eb30d.png)

### Creating a container in that storage account

####Step 1: Get the storage account key
The following command is used to get the storage key by passing 0 or 1 as the parameters to get the primary or secondary key.

Provide the resource group name followed by storage account name with the value of key.

```
# use this command to get the storage Account Key- Primary key
$storagekey = (Get-AzureRmStorageAccountKey -ResourceGroupName "ShilpaG_ResourceGroup" -Name "shilpagundrathi").Value[0]
```
**PowerShell Output:**  

![image](https://cloud.githubusercontent.com/assets/26247378/23730618/4360755a-041d-11e7-9f5f-f0bfd01aa4fa.png)

#### Step 2: Set Context with a protocol 

The following command is used for creating the context. Specify the storage account name and storage account key as well.

```
# Use this command to set the context
$context = New-AzureStorageContext -StorageAccountName "shilpagundrathi" -StorageAccountKey $storagekey -Protocol Http
```
**PowerShell Output:**  

![image](https://cloud.githubusercontent.com/assets/26247378/23730707/c7ae0dfe-041d-11e7-8c43-f8ac77b0c108.png)

#### Step 3: Create a Container

Authentication to the Azure Storage service is completed, next is to use the Storage Context object, a Blob Container needs to be created. A Blob Container holds zero or more storage Blobs. 

Use the following command to create a container using the above created context and specify the container name:

```
# Use this command to create a container
New-AzureStorageContainer -Context $context -Name shilpacontainer
```
**PowerShell Output:**  

![image](https://cloud.githubusercontent.com/assets/26247378/23730829/9997f3b6-041e-11e7-83d1-1a3b91a336ec.png)

**Reflection in Azure Portal:**  

![image](https://cloud.githubusercontent.com/assets/26247378/23730841/b01896d6-041e-11e7-892a-c636588f04c9.png)


### Seeing what containers exist within the storage account

#### Step 1:
1. Call the Get-AzureStorageContainer command, like the example below. 
  * Pass the Storage Context object into the **-Context** parameter. 

```
# Use this command to check the list of containers exist in the storage account
Get-AzureStorageContainer -Context $context

```
**PowerShell Output:**  

![image](https://cloud.githubusercontent.com/assets/26247378/23731049/14027e2c-0420-11e7-8032-70cd60184337.png)

**Reflection in Azure Portal:**  

![image](https://cloud.githubusercontent.com/assets/26247378/23731110/5b6753d2-0420-11e7-9308-38fe890a86db.png)

### Uploading data to that container
**Step:** 

1.  Call the Set-AzureStorageBlobContent command, like the example below
  * Pass in the Storage Context object into the -Context parameter. 
  * Specify a name for the container, using the -Name parameter and
  * Mention the path name

```
# Use this command to upload the content
Set-AzureStorageBlobContent -Context $context -Container shilpacontainer -File "C:\Users\Shilpa\Desktop\ShilpaGundrathi.txt"
```
**PowerShell Output:** 

![image](https://cloud.githubusercontent.com/assets/26247378/23731505/f682223c-0422-11e7-811c-cb52b5411f46.png)

**Reflection in Azure Portal:**  

![image](https://cloud.githubusercontent.com/assets/26247378/23731537/2090c74a-0423-11e7-9497-b6e979855c25.png)


### Seeing items are inside of that container
**Step 1:**
1.  Call the Get-AzureStorageBlob command.
  * Specify a name for the container, using the -Name parameter.
  * Pass in the Storage Context object into the -Context parameter. 

```
# Use this command to check the items are inside of the container
Get-AzureStorageBlob -Container shilpacontainer -Context $context
```
**PowerShell Output:**

![image](https://cloud.githubusercontent.com/assets/26247378/23731762/2c4d9738-0424-11e7-806a-3c146305c754.png)


### Deleting an item in that container
**Steps:**
1. Call the Remove-AzureStorageBlob command, like the example below.
  * Pass in the Storage Context object into the -Context parameter. 
  * Specify a name for the container, using the -Name parameter and 
  * Give a file name that needs to be deleted
 ```
 # Use this command to delete the item in the container
 Remove-AzureStorageBlob -Container shilpacontainer -Context $context -Blob "ShilpaGundrathi.txt"
```

**PowerShell Output:**

![image](https://cloud.githubusercontent.com/assets/26247378/23732317/5070b9ee-0427-11e7-9368-188534e94b58.png)

**Reflection in Azure Portal:**  

![image](https://cloud.githubusercontent.com/assets/26247378/23732402/b97a98b0-0427-11e7-95fe-5006ca3cdbfd.png)

### Deleting the container
**Steps:**
1. Call the Remove-AzureStorageContainer command.
  * Pass in the Storage Context object into the -Context parameter.
  * Specify a name for the container, using the -Name parameter.
```
# Use this command to delete the container
Remove-AzureStorageContainer -Context $context -Name shilpacontainer
```
**PowerShell Output:**  

![image](https://cloud.githubusercontent.com/assets/26247378/23732539/760f54ac-0428-11e7-9596-663de638bbcd.png)

**Reflection in Azure Portal:**  

![image](https://cloud.githubusercontent.com/assets/26247378/23732555/8c996dac-0428-11e7-9e67-cfc623fddaf3.png)

### Deleting the storage account
**Steps:**
1.  Call the Remove-AzureRmStorageAccount command.
  * Pass in the Resource Group Name under the ResourceGroupName parameter and, 
  * Pass the Storage Account Name under the StorageAccountName parameter.
 
```
# Use this command to delete the storage account

Remove-AzureRmStorageAccount -ResourceGroupName 'ShilpaG_ResourceGroup' -StorageAccountName 'shilpagundrathi'
```
After executing the above command, a conformation message pops up for confirmation.


![image](https://cloud.githubusercontent.com/assets/26247378/23735197/ff33fa98-0438-11e7-8829-6b98f48d496d.png)

**PowerShell Output:**  

![image](https://cloud.githubusercontent.com/assets/26247378/23732667/3eff7e46-0429-11e7-8bc9-401cda033ce6.png)


**Reflection in Azure Portal:**  

![image](https://cloud.githubusercontent.com/assets/26247378/23732679/514010c0-0429-11e7-8fc1-a7f17e09a02c.png)
You have successfully accessed Azure portal using PowerShell cmdlets.
