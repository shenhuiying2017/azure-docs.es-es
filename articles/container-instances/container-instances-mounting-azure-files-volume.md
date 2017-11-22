---
title: Montaje de un volumen de Azure Files en Azure Container Instances
description: "Obtenga información sobre cómo montar un volumen de Azure Files para conservar el estado con Azure Container Instances"
services: container-instances
documentationcenter: 
author: seanmck
manager: timlt
editor: 
tags: 
keywords: 
ms.assetid: 
ms.service: container-instances
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/09/2017
ms.author: seanmck
ms.custom: mvc
ms.openlocfilehash: 0f824dad7ba5b661941e952383025e5171f32e55
ms.sourcegitcommit: bc8d39fa83b3c4a66457fba007d215bccd8be985
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/10/2017
---
# <a name="mount-an-azure-file-share-with-azure-container-instances"></a>Montaje de un recurso compartido de archivos de Azure en Azure Container Instances

De forma predeterminada, Azure Container Instances no tiene estado. Si el contenedor se bloquea o se detiene, se pierde todo su estado. Para conservar el estado más allá de la duración del contenedor, debe montar un volumen desde un almacén externo. Este artículo muestra cómo montar un recurso compartido de archivos de Azure para su uso con Azure Container Instances.

## <a name="create-an-azure-file-share"></a>Creación de un recurso compartido de archivos de Azure

Antes de utilizar un recurso compartido de archivos de Azure en Azure Container Instances, debe crearlo. Ejecute el script siguiente para crear una cuenta de almacenamiento para hospedar el recurso compartido de archivos y el propio recurso compartido. El nombre de la cuenta de almacenamiento debe ser único globalmente, por lo que el script agrega un valor aleatorio en la cadena base.

```azurecli-interactive
# Change these four parameters
ACI_PERS_STORAGE_ACCOUNT_NAME=mystorageaccount$RANDOM
ACI_PERS_RESOURCE_GROUP=myResourceGroup
ACI_PERS_LOCATION=eastus
ACI_PERS_SHARE_NAME=acishare

# Create the storage account with the parameters
az storage account create -n $ACI_PERS_STORAGE_ACCOUNT_NAME -g $ACI_PERS_RESOURCE_GROUP -l $ACI_PERS_LOCATION --sku Standard_LRS

# Export the connection string as an environment variable, this is used when creating the Azure file share
export AZURE_STORAGE_CONNECTION_STRING=`az storage account show-connection-string -n $ACI_PERS_STORAGE_ACCOUNT_NAME -g $ACI_PERS_RESOURCE_GROUP -o tsv`

# Create the share
az storage share create -n $ACI_PERS_SHARE_NAME
```

## <a name="acquire-storage-account-access-details"></a>Adquisición de detalles de acceso de la cuenta de almacenamiento

Para montar un recurso compartido de archivos de Azure como un volumen en Azure Container Instances, necesita tres valores: el nombre de la cuenta de almacenamiento, el nombre del recurso compartido y la clave de acceso de almacenamiento.

Si ha usado el script anterior, el nombre de la cuenta de almacenamiento se creó con un valor aleatorio al final. Para consultar la cadena final (incluida la parte aleatoria), use los siguientes comandos:

```azurecli-interactive
STORAGE_ACCOUNT=$(az storage account list --resource-group $ACI_PERS_RESOURCE_GROUP --query "[?contains(name,'$ACI_PERS_STORAGE_ACCOUNT_NAME')].[name]" -o tsv)
echo $STORAGE_ACCOUNT
```

El nombre del recurso compartido ya se conoce (es *acishare* en el script anterior), por lo que todo lo que queda es la clave de cuenta de almacenamiento, que puede encontrarse con el siguiente comando:

```azurecli-interactive
STORAGE_KEY=$(az storage account keys list --resource-group $ACI_PERS_RESOURCE_GROUP --account-name $STORAGE_ACCOUNT --query "[0].value" -o tsv)
echo $STORAGE_KEY
```

## <a name="store-storage-account-access-details-with-azure-key-vault"></a>Almacenamiento de los detalles de acceso de la cuenta de almacenamiento con Azure Key Vault

Las claves de cuenta de almacenamiento protegen el acceso a los datos, por lo que recomendamos almacenarlas en Azure Key Vault.

Cree un almacén de claves con la CLI de Azure:

```azurecli-interactive
KEYVAULT_NAME=aci-keyvault
az keyvault create -n $KEYVAULT_NAME --enabled-for-template-deployment -g $ACI_PERS_RESOURCE_GROUP
```

El conmutador `enabled-for-template-deployment` permite a Azure Resource Manager extraer secretos del almacén de claves durante la implementación.

Almacene la clave de cuenta de almacenamiento como un secreto nuevo en el almacén de claves:

```azurecli-interactive
KEYVAULT_SECRET_NAME=azurefilesstoragekey
az keyvault secret set --vault-name $KEYVAULT_NAME --name $KEYVAULT_SECRET_NAME --value $STORAGE_KEY
```

## <a name="mount-the-volume"></a>Monte el volumen

El montaje de un recurso compartido de archivos de Azure como un volumen en un contenedor es un proceso que consta de dos pasos. En primer lugar, proporcione los detalles del recurso compartido como parte de la definición del grupo de contenedores; luego, especifique cómo desea que el volumen se monte dentro de uno o varios de los contenedores del grupo.

Para definir los volúmenes que desea que estén disponibles para el montaje, agregue una matriz `volumes` a la definición del grupo de contenedores en la plantilla de Azure Resource Manager; luego, haga referencia a ellos en la definición de los contenedores individuales.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "storageaccountname": {
      "type": "string"
    },
    "storageaccountkey": {
      "type": "securestring"
    }
  },
  "resources":[{
    "name": "hellofiles",
    "type": "Microsoft.ContainerInstance/containerGroups",
    "apiVersion": "2017-08-01-preview",
    "location": "[resourceGroup().location]",
    "properties": {
      "containers": [{
        "name": "hellofiles",
        "properties": {
          "image": "seanmckenna/aci-hellofiles",
          "resources": {
            "requests": {
              "cpu": 1,
              "memoryInGb": 1.5
            }
          },
          "ports": [{
            "port": 80
          }],
          "volumeMounts": [{
            "name": "myvolume",
            "mountPath": "/aci/logs/"
          }]
        }
      }],
      "osType": "Linux",
      "ipAddress": {
        "type": "Public",
        "ports": [{
          "protocol": "tcp",
          "port": "80"
        }]
      },
      "volumes": [{
        "name": "myvolume",
        "azureFile": {
          "shareName": "acishare",
          "storageAccountName": "[parameters('storageaccountname')]",
          "storageAccountKey": "[parameters('storageaccountkey')]"
        }
      }]
    }
  }]
}
```

La plantilla incluye el nombre y la clave de la cuenta de almacenamiento como parámetros, que se pueden proporcionar en un archivo de parámetros independiente. Para rellenar el archivo de parámetros, necesita tres valores: el nombre de la cuenta de almacenamiento, el identificador de recursos de Azure Key Vault y el nombre del secreto del almacén de claves que utilizó para almacenar la clave de almacenamiento. Si ha seguido los pasos anteriores, puede obtener estos valores con el siguiente script:

```azurecli-interactive
echo $STORAGE_ACCOUNT
echo $KEYVAULT_SECRET_NAME
az keyvault show --name $KEYVAULT_NAME --query [id] -o tsv
```

Inserte los valores en el archivo de parámetros:

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "storageaccountname": {
      "value": "<my_storage_account_name>"
    },
   "storageaccountkey": {
      "reference": {
        "keyVault": {
          "id": "<my_keyvault_id>"
        },
        "secretName": "<my_storage_account_key_secret_name>"
      }
    }
  }
}
```

## <a name="deploy-the-container-and-manage-files"></a>Implementación del contenedor y administración de archivos

Con la plantilla definida, puede crear el contenedor y montar el volumen de este mediante la CLI de Azure. Suponiendo que el archivo de plantilla y el archivo de parámetros se denominan *azuredeploy.json* y *azuredeploy.parameters.json*, respectivamente, la línea de comandos es:

```azurecli-interactive
az group deployment create --name hellofilesdeployment --template-file azuredeploy.json --parameters @azuredeploy.parameters.json --resource-group $ACI_PERS_RESOURCE_GROUP
```

Una vez que se inicie el contenedor, puede usar la aplicación web sencilla que se implementó mediante la imagen **seanmckenna/aci-hellofiles** para administrar los archivos en el recurso compartido de archivos de Azure en la ruta de montaje que especificó. Obtenga la dirección IP de la aplicación web con el comando [az container show](/cli/azure/container#az_container_show):

```azurecli-interactive
az container show --resource-group $ACI_PERS_RESOURCE_GROUP --name hellofiles -o table
```

Puede usar una herramienta como el [Explorador de Microsoft Azure Storage](https://storageexplorer.com) para recuperar e inspeccionar el archivo escrito en el recurso compartido de archivos.

>[!NOTE]
> Para más información sobre el uso de plantillas de Azure Resource Manager, archivos de parámetros y la implementación con la CLI de Azure, consulte [Implementación de recursos con plantillas de Resource Manager y la CLI de Azure](../azure-resource-manager/resource-group-template-deploy-cli.md).

## <a name="next-steps"></a>Pasos siguientes

- Implementar el primer contenedor mediante la [guía de inicio rápido](container-instances-quickstart.md) de Azure Container Instances
- Obtener información sobre la [relación entre Azure Container Instances y orquestadores de contenedor](container-instances-orchestrator-relationship.md)
