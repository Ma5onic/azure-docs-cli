---
title: Query command results with Azure CLI 2.0
description: Use --query to perform JMESPath queries on the output of Azure CLI 2.0 commands.
keywords: Azure CLI 2.0, JMESPath, query, Linux, Mac, Windows, OS X
author: allclark
manager: douge
ms.date: 02/18/2017
ms.topic: article
ms.prod: azure
ms.technology: azure
ms.assetid: 5979acc5-21a5-41e2-a4b6-3183bfe6aa22
---

# Using JMESPath queries with Azure CLI 2.0

The Azure CLI 2.0 uses the `--query` parameter to execute a [JMESPath query](http://jmespath.org) on the results of your `az` command. JMESPath is a powerful query language for JSON outputs.  If you unfamiliar with JMESPath queries you can find a tutorial at [JMESPath.org/tutorial](http:/JMESPath.org/tutorial.html).

Queries can be run for a variety of different purposes.  We have listed several examples below.  

## List resource group and VM names for all virtual machines in your subscription

```azurecli
az vm list \
  --query [*].[name,resourceGroup]
```

```json
Column1    Column2
---------  -----------
DEMORG1    DemoVM010
DEMORG1    demovm111
DEMORG1    demovm211
DEMORG1    demovm212
DEMORG1    demovm213
DEMORG1    demovm214
DEMORG1    demovm222
RGDEMO001  KBDemo001VM
RGDEMO001  KBDemo020
```

In the previous example you notice that the column headings are "Column1" and "Column2".  You can add friendly labels or name to the properties you select as well.  In this example we added the labels "VMName" and "RGName" to the selected properties "name" and "resourceGroup".


```azurecli
az vm list \
  --query "[].{RGName:resourceGroup, VMName:name}"
```

```json
RGName     VMName
---------  -----------
DEMORG1    DemoVM010
DEMORG1    demovm111
DEMORG1    demovm211
DEMORG1    demovm212
DEMORG1    demovm213
DEMORG1    demovm214
DEMORG1    demovm222
RGDEMO001  KBDemo001VM
RGDEMO001  KBDemo020
```

## Selecting complex nested properties

If the property you want to select is nested deep in the JSON output then you have to supply the full path to that embedded property. The following example shows how to select the VMName and the OS type from the vm list command.

```azurecli
az vm list \
  --query "[].{VMName:name,OSType:storageProfile.osDisk.osType}"
```

```json
VMName       OSType
-----------  --------
DemoVM010    Linux
demovm111    Linux
demovm211    Linux
demovm212    Linux
demovm213    Linux
demovm214    Linux
demovm222    Linux
KBDemo001VM  Linux
KBDemo020    Linux
```

## Filter with the contains function

You can use the JMESPath `contains` function to refine your results returned in the query.
In this example the results will limit the VMs returned to those in a specific resource group.

```azurecli
az vm list \
  --query "[?contains(resourceGroup,'RGD')].{ resource: resourceGroup, name: name }"
```

```json
Resource    VMName
----------  -----------
RGDEMO001   KBDemo001VM
RGDEMO001   KBDemo020
```

With this example the results will return the VMs that have the vmSize 'Standard_DS1'.

```azurecli
az vm list \
  --query "[?contains(hardwareProfile.vmSize, 'Standard_DS1')]"
```

```json
ResourceGroup    VMName     VmId                                  Location    ProvisioningState
---------------  ---------  ------------------------------------  ----------  -------------------
DEMORG1          DemoVM010  cbd56d9b-9340-44bc-a722-25f15b578444  westus      Succeeded
DEMORG1          demovm111  c1c024eb-3837-4075-9117-bfbc212fa7da  westus      Succeeded
DEMORG1          demovm211  95eda642-417f-4036-9475-67246ac0f0d0  westus      Succeeded
DEMORG1          demovm212  4bdac85d-c2f7-410f-9907-ca7921d930b4  westus      Succeeded
DEMORG1          demovm213  2131c664-221a-4b7f-9653-f6d542fbfa34  westus      Succeeded
DEMORG1          demovm214  48f419af-d27a-4df0-87f3-9481007c2e5a  westus      Succeeded
DEMORG1          demovm222  e0f59516-1d69-4d54-b8a2-f6c4a5d031de  westus      Succeeded
```

## Filter with grep

Use the [`--out`](format-output-az-cli2.md) parameter to format the output as tab-separated values
and pipe that through grep.

```azurecli
az vm list \
  --query "[].{ name: name, os: storageProfile.osDisk.osType }" \
  --out tsv  \
| grep Linux
```

## Explore with jpterm

You can also pipe the command output to [JMESPath-terminal](https://github.com/jmespath/jmespath.terminal)
and experiment with your JMESPath query there.

```bash
pip install jmespath-terminal
az vm list | jpterm
```
