---
title: Montaje de un volumen de Azure Files en Azure Container Instances
description: "Obtenga información sobre cómo montar un volumen de Azure Files para conservar el estado con Azure Container Instances"
services: container-instances
author: seanmck
manager: timlt
ms.service: container-instances
ms.topic: article
ms.date: 01/02/2018
ms.author: seanmck
ms.custom: mvc
ms.openlocfilehash: be502e6aef39ee4ed8cfc1f8926cb556dc1defb1
ms.sourcegitcommit: 9292e15fc80cc9df3e62731bafdcb0bb98c256e1
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/10/2018
---
# <a name="mount-an-azure-file-share-with-azure-container-instances"></a>Montaje de un recurso compartido de archivos de Azure en Azure Container Instances

De forma predeterminada, Azure Container Instances no tiene estado. Si el contenedor se bloquea o se detiene, se pierde todo su estado. Para conservar el estado más allá de la duración del contenedor, debe montar un volumen desde un almacén externo. Este artículo muestra cómo montar un recurso compartido de archivos de Azure para su uso con Azure Container Instances.

> [!NOTE]
> El montaje de un recurso compartido de archivos de Azure está actualmente restringido a los contenedores Linux. Aunque estamos trabajando para traer todas las características a los contenedores Windows, puede encontrar diferencias en la plataforma actual en la [disponibilidad de cuotas y regiones para Azure Container Instances](container-instances-quotas.md).

## <a name="create-an-azure-file-share"></a>Creación de un recurso compartido de archivos de Azure

Antes de utilizar un recurso compartido de archivos de Azure en Azure Container Instances, debe crearlo. Ejecute el script siguiente para crear una cuenta de almacenamiento para hospedar el recurso compartido de archivos y el propio recurso compartido. El nombre de la cuenta de almacenamiento debe ser único globalmente, por lo que el script agrega un valor aleatorio en la cadena base.

```azurecli-interactive
# Change these four parameters as needed
ACI_PERS_RESOURCE_GROUP=myResourceGroup
ACI_PERS_STORAGE_ACCOUNT_NAME=mystorageaccount$RANDOM
ACI_PERS_LOCATION=eastus
ACI_PERS_SHARE_NAME=acishare

# Create the storage account with the parameters
az storage account create \
    --resource-group $ACI_PERS_RESOURCE_GROUP \
    --name $ACI_PERS_STORAGE_ACCOUNT_NAME \
    --location $ACI_PERS_LOCATION \
    --sku Standard_LRS

# Export the connection string as an environment variable. The following 'az storage share create' command
# references this environment variable when creating the Azure file share.
export AZURE_STORAGE_CONNECTION_STRING=`az storage account show-connection-string --resource-group $ACI_PERS_RESOURCE_GROUP --name $ACI_PERS_STORAGE_ACCOUNT_NAME --output tsv`

# Create the file share
az storage share create -n $ACI_PERS_SHARE_NAME
```

## <a name="get-storage-credentials"></a>Obtención de las credenciales de almacenamiento

Para montar un recurso compartido de archivos de Azure como un volumen en Azure Container Instances, necesita tres valores: el nombre de la cuenta de almacenamiento, el nombre del recurso compartido y la clave de acceso de almacenamiento.

Si ha usado el script anterior, el nombre de la cuenta de almacenamiento se creó con un valor aleatorio al final. Para consultar la cadena final (incluida la parte aleatoria), use los siguientes comandos:

```azurecli-interactive
STORAGE_ACCOUNT=$(az storage account list --resource-group $ACI_PERS_RESOURCE_GROUP --query "[?contains(name,'$ACI_PERS_STORAGE_ACCOUNT_NAME')].[name]" --output tsv)
echo $STORAGE_ACCOUNT
```

El nombre del recurso compartido ya se conoce (es *acishare* en el script anterior), por lo tanto, solo queda la clave de cuenta de almacenamiento, que se encuentra con el siguiente comando:

```azurecli-interactive
STORAGE_KEY=$(az storage account keys list --resource-group $ACI_PERS_RESOURCE_GROUP --account-name $STORAGE_ACCOUNT --query "[0].value" --output tsv)
echo $STORAGE_KEY
```

## <a name="deploy-container-and-mount-volume"></a>Implementación de un contenedor y montaje del volumen

Para montar un recurso compartido de archivos de Azure como volumen en un contenedor, especifique el recurso compartido y el punto de montaje del volumen al crear el contenedor con [az container create][az-container-create]. Si ha seguido los pasos anteriores, puede montar el recurso compartido que creó anteriormente mediante el comando siguiente para crear un contenedor:

```azurecli-interactive
az container create \
    --resource-group $ACI_PERS_RESOURCE_GROUP \
    --name hellofiles \
    --image seanmckenna/aci-hellofiles \
    --ip-address Public \
    --ports 80 \
    --azure-file-volume-account-name $ACI_PERS_STORAGE_ACCOUNT_NAME \
    --azure-file-volume-account-key $STORAGE_KEY \
    --azure-file-volume-share-name $ACI_PERS_SHARE_NAME \
    --azure-file-volume-mount-path /aci/logs/
```

## <a name="manage-files-in-mounted-volume"></a>Administración de archivos en el volumen montado

Una vez que se inicie el contenedor, puede usar la aplicación web sencilla que se implementó mediante la imagen [seanmckenna/aci-hellofiles][aci-hellofiles] para administrar los archivos en el recurso compartido de archivos de Azure en la ruta de montaje que especificó. Obtenga la dirección IP de la aplicación web con el comando [az container show][az-container-show]:

```azurecli-interactive
az container show --resource-group $ACI_PERS_RESOURCE_GROUP --name hellofiles --output table
```

Puede usar [Azure Portal][portal] o una herramienta como el [Explorador de Microsoft Azure Storage][storage-explorer] para recuperar e inspeccionar el archivo escrito en el recurso compartido de archivos.

## <a name="mount-multiple-volumes"></a>Montaje de varios volúmenes

Para montar varios volúmenes en una instancia del contenedor, debe realizar la implementación con una [plantilla de Azure Resource Manager](/azure/templates/microsoft.containerinstance/containergroups).

En primer lugar, proporcione los detalles del recurso compartido y defina los volúmenes rellenando la matriz `volumes` en la sección `properties` de la plantilla. Por ejemplo, si ha creado dos recursos compartidos de archivos de Azure llamados *share1* y *share2* en la cuenta de almacenamiento *myStorageAccount*, la matriz `volumes` sería similar a la siguiente:

```json
"volumes": [{
  "name": "myvolume1",
  "azureFile": {
    "shareName": "share1",
    "storageAccountName": "myStorageAccount",
    "storageAccountKey": "<storage-account-key>"
  }
},
{
  "name": "myvolume2",
  "azureFile": {
    "shareName": "share2",
    "storageAccountName": "myStorageAccount",
    "storageAccountKey": "<storage-account-key>"
  }
}]
```

A continuación, para cada contenedor del grupo de contenedores en el que desea montar los volúmenes, rellene la matriz `volumeMounts` en la sección `properties` de la definición del contenedor. Por ejemplo, esto monta los dos volúmenes, *myvolume1* y *myvolume2* definidos previamente:

```json
"volumeMounts": [{
  "name": "myvolume1",
  "mountPath": "/mnt/share1/"
},
{
  "name": "myvolume2",
  "mountPath": "/mnt/share2/"
}]
```

Para ver un ejemplo de implementación de instancias de contenedor con una plantilla de Azure Resource Manager, consulte [Implementación de grupos de varios contenedores en Azure Container Instances](container-instances-multi-container-group.md).

## <a name="next-steps"></a>pasos siguientes

Información acerca de la relación entre[Azure Container Instances y los orquestadores de contenedores](container-instances-orchestrator-relationship.md).

<!-- LINKS - External -->
[aci-hellofiles]: https://hub.docker.com/r/seanmckenna/aci-hellofiles/
[portal]: https://portal.azure.com
[storage-explorer]: https://storageexplorer.com

<!-- LINKS - Internal -->
[az-container-create]: /cli/azure/container#az_container_create
[az-container-show]: /cli/azure/container#az_container_show