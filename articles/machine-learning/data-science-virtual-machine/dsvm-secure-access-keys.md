---
title: 'Almacenamiento de las credenciales de acceso en Data Science Virtual Machine de forma segura: Azure | Microsoft Docs'
description: Almacenamiento de las credenciales de acceso en Data Science Virtual Machine de forma segura.
keywords: aprendizaje profundo, AI, herramientas de ciencia de datos, máquina virtual de ciencia de datos, análisis geoespacial, proceso de ciencia de datos en equipo
services: machine-learning
documentationcenter: ''
author: gopitk
manager: cgronlun
ms.assetid: ''
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/08/2018
ms.author: gokuma
ms.openlocfilehash: 4eb1d657adc37ef0d1e4055573b174d58baf2e0e
ms.sourcegitcommit: 909469bf17211be40ea24a981c3e0331ea182996
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/10/2018
---
# <a name="store-access-credentials-on-the-data-science-virtual-machine-securely"></a>Almacenamiento de las credenciales de acceso en Data Science Virtual Machine de forma segura

Un desafío común al crear aplicaciones en la nube consiste en el modo de administrar las credenciales que tienen que estar en el código para autenticar los servicios en la nube. Proteger estas credenciales es una tarea esencial. Lo ideal sería que nunca aparecieran en las estaciones de trabajo de los desarrolladores o que se controlaran en el código fuente. 

Para solucionar este problema, [Managed Service Identity (MSI)](https://docs.microsoft.com/azure/active-directory/managed-service-identity/overview) proporciona a los servicios de Azure una identidad administrada automáticamente en Azure Active Directory (Azure AD). Puede usar esta identidad para autenticar a cualquier servicio que admita la autenticación de Azure AD, sin necesidad de tener credenciales en el código. Un patrón común de protección de las credenciales es usar MSI en combinación con la [Azure Key Vault](https://docs.microsoft.com/azure/key-vault/), un servicio de Azure administrado para almacenar las claves criptográficas y los secretos de forma segura. Puede acceder a Key Vault mediante la identidad de servicio administrada y recuperar los secretos autorizados y las claves criptográficas de este. 

La documentación de MSI y Key Vault es un recurso integral de información detallada sobre estos servicios. El resto de este artículo es un texto elemental sobre el uso básico de MSI y Key Vault en Data Science Virtual Machine (DSVM). 

## <a name="walkthrough-of-using-msi-to-securely-access-azure-resources"></a>Tutorial de uso de MSI para acceder a los recursos de Azure de manera segura

## <a name="1-create-msi-on-the-dsvm"></a>1. Creación de MSI en la DSVM 


```
# Pre-requisites: You have already created a Data Science VM in the usual way

# Create identity principal for the VM
az vm assign-identity -g <Resource Group Name> -n <Name of the VM>
# Get the principal id of the DSVM
az resource list -n <Name of the VM> --query [*].identity.principalId --out tsv
```


## <a name="2-assign-keyvault-access-permission-to-vm-principal"></a>2. Asignación del permiso de acceso a Key Vault a la entidad de seguridad de la máquina virtual
```
# Pre-requisites: You have already create an empty Key Vault resource on Azure using Portal or Azure CLI 

# Assign only get and set permission but not the capability to list the keys
az keyvault set-policy --object-id <PrincipalID of the DSVM from previous step> --name <Key Vault Name> -g <Resource Group of Key Vault>  --secret-permissions get set
```

## <a name="3-access-a-secret-in-the-keyvault-from-the-dsvm"></a>3. Acceso a un secreto de Key Vault desde la DSVM

```
# Get the access token for VM
x=`curl http://localhost:50342/oauth2/token --data "resource=https://vault.azure.net" -H Metadata:true`
token=`echo $x | python -c "import sys, json; print(json.load(sys.stdin)['access_token'])"`

# Access Keyvault using access token. 
curl https://<Vault Name>.vault.azure.net/secrets/SQLPasswd?api-version=2016-10-01 -H "Authorization: Bearer $token"
```

## <a name="4-access-storage-keys-from-the-dsvm"></a>4. Acceso a las claves de almacenamiento desde la DSVM

```
# Pre-requisites: You have granted your VM's MSI access to use storage account access keys based on instruction from this article:https://docs.microsoft.com/azure/active-directory/managed-service-identity/tutorial-linux-vm-access-storage that describes this process in more detail.

y=`curl http://localhost:50342/oauth2/token --data "resource=https://management.azure.com/" -H Metadata:true`
ytoken=`echo $y | python -c "import sys, json; print(json.load(sys.stdin)['access_token'])"`
curl https://management.azure.com/subscriptions/<SubscriptionID>/resourceGroups/<ResourceGroup of Storage account>/providers/Microsoft.Storage/storageAccounts/<Storage Account Name>/listKeys?api-version=2016-12-01 --request POST -d "" -H "Authorization: Bearer $ytoken"

#Now you can access the data in the storage account from the retrieved Storage account keys
```
## <a name="5-access-keyvault-from-python"></a>5. Acceda a Key Vault desde Python

```python
from azure.keyvault import KeyVaultClient
from msrestazure.azure_active_directory import MSIAuthentication

"""MSI Authentication example."""

# Get credentials
credentials = MSIAuthentication(
    resource='https://vault.azure.net'
)

# Create a KeyVault client
key_vault_client = KeyVaultClient(
credentials
)

key_vault_uri = "https://<key Vault Name>.vault.azure.net/"

secret = key_vault_client.get_secret(
key_vault_uri,  # Your KeyVault URL
"SQLPasswd",       # Name of your secret that already exists in the Key Vault
""              # The version of the secret. Empty string for latest
)
print("My secret value is {}".format(secret.value))
```

## <a name="6-azure-cli-access-from-vm"></a>6. Acceso a la CLI de Azure desde la máquina virtual

```
# With a Managed Service Identity setup on the DSVM, users on the DSVM can execute Azure CLI to perform the authorized functions. Here are commands to access Key Vault from a Azure CLI without having to login to an Azure account. 
# Pre-requisites: MSI is already setup on the DSVM as indicated earlier and specific permission like accessing storage account keys, reading specific secrets and writing new secrets is provided to the MSI . 

# Authenticate to Azure CLI without requiring an Azure Account. 
az login --msi

# Retrieve a secret from Key Vault. 
az keyvault secret show --vault-name <Vault Name> --name SQLPasswd

# Create a new Secret in Key Vault
az keyvault secret set --name MySecret --vault-name <Vault Name> --value "Helloworld"

# List Storage Account Access Keys
az storage account keys list -g <Storage Account Resource Group> -n <Storage Account Name>
```
