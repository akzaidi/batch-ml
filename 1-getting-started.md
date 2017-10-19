# Getting Started with Azure Batch, Batch AI, and Distributed Data Engineering Toolkit

## Setting up an Azure Storage Account

There are primarily two prerequisites with getting started with Azure Batch services.

1. Azure Storage
2. Azure Batch Account

In addition, we'll be making heavy use of the Azure CLI 2.0, for which you can find installation instructions [here](https://docs.microsoft.com/en-us/cli/azure/install-azure-cli?view=azure-cli-latest). Alternatively, you can use the Azure CLI directly from the Azure Cloud Shell.

```bash
az login
az account show --output table
```

### Creating an Azure Resource Group and Storage Account

We will need a resource group and a storage account.

```bash
az group create -n azbatchrg -l westus2
```

Now that we have our resource group, we can add a batch acount into that resource group.
 
```bash
az storage account create \
	--name azbatchsa \
	--sku Standard_LRS \
	--location westus2 \
	--resource-group azbatchrg
```
Now let's create our batch account:

```bash
az batch account create \
	--name azbatchwest \
	--location westus2 \
	--resource-group azbatchrg \
	--storage-account azbatchsa
```

You can now see the primary key for your Azure Batch account:

```bash
az batch account keys list \
	--name azbatchwest \
	--resource-group azbatchrg \
	| jq '{key: .primary}'
```

You can save this to a local file:

```bash
AZBATCHKEY="$(az batch account keys list \
	--name azbatchwest \
	--resource-group azbatchrg \
	| jq '{key: .primary}' | jq .[])"
AZBATCHACCT="$(az batch account keys list \
	--name azbatchwest \
	--resource-group azbatchrg \
	| jq '{name: .accountName}' | jq .[])"
echo "Accout Name = " $AZBATCHACCT >> credentials.txt
echo "Batch Key = " $AZBATCH >> credentials.txt 
```
We also need the Batch URL:

```bash
AZBATCHURL="$(az batch account list \
	--resource-group azbatchrg \
	| jq .[0].accountEndpoint)"
echo "Batch URL = " $AZBATCHURL >> credentials.txt
```

Let's also get the credentials for the storage account:

```bash
SAKEY="$(az storage account keys list \
	--account-name azbatchsa \
	--resource-group azbatchrg \
	| jq '.[0].value')"
echo "Storage Account Keys =" $SAKEY >> credentials.txt
```
