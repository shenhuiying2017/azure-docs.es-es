---
title: "Recurso compartido de archivos para clústeres de DC/OS en Azure"
description: "Creación y montaje de un recurso compartido de archivos en un clúster de DC/OS en Azure Container Service"
services: container-service
author: julienstroheker
manager: dcaro
ms.service: container-service
ms.topic: tutorial
ms.date: 06/07/2017
ms.author: juliens
ms.custom: mvc
ms.openlocfilehash: c1c318f4204efd24a2d9d3d83bb1cb71f5775bdb
ms.sourcegitcommit: 5d3e99478a5f26e92d1e7f3cec6b0ff5fbd7cedf
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/06/2017
---
# <a name="create-and-mount-a-file-share-to-a-dcos-cluster"></a>Creación y montaje de un recurso compartido de archivos en un clúster de DC/OS

En este tutorial se detalla la creación de un recurso compartido de archivos en Azure y cómo montarlo en cada agente y patrón del clúster de DC/OS. La configuración de un recurso compartido de archivos facilita el uso compartido de estos recursos entre el clúster, como la configuración, el acceso, los registros y mucho más. En este tutorial se realizan las siguientes tareas:

> [!div class="checklist"]
> * Creación de una cuenta de Azure Storage
> * Creación de un recurso compartido de archivos
> * Montaje del recurso compartido en el clúster de DC/OS

Necesita un clúster de ACS con DC/OS para completar los pasos de este tutorial. Si es necesario, este [script de ejemplo](./../kubernetes/scripts/container-service-cli-deploy-dcos.md) puede crear uno.

Para realizar este tutorial es necesaria la versión 2.0.4 o superior de la CLI de Azure. Ejecute `az --version` para encontrar la versión. Si necesita actualizarla, consulte [Instalación de la CLI de Azure 2.0]( /cli/azure/install-azure-cli). 

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="create-a-file-share-on-microsoft-azure"></a>Creación de un recurso compartido de archivos en Microsoft Azure

Antes de usar un recurso compartido de archivos de Azure con un clúster de ACS con DC/OS, es necesario crear el recurso compartido de cuentas de almacenamiento y archivos. Ejecute el script siguiente para crear el recurso compartido de almacenamiento y archivos. Actualice los parámetros con los de su entorno.

```azurecli-interactive
# Change these four parameters
DCOS_PERS_STORAGE_ACCOUNT_NAME=mystorageaccount$RANDOM
DCOS_PERS_RESOURCE_GROUP=myResourceGroup
DCOS_PERS_LOCATION=eastus
DCOS_PERS_SHARE_NAME=dcosshare

# Create the storage account with the parameters
az storage account create -n $DCOS_PERS_STORAGE_ACCOUNT_NAME -g $DCOS_PERS_RESOURCE_GROUP -l $DCOS_PERS_LOCATION --sku Standard_LRS

# Export the connection string as an environment variable, this is used when creating the Azure file share
export AZURE_STORAGE_CONNECTION_STRING=`az storage account show-connection-string -n $DCOS_PERS_STORAGE_ACCOUNT_NAME -g $DCOS_PERS_RESOURCE_GROUP -o tsv`

# Create the share
az storage share create -n $DCOS_PERS_SHARE_NAME
```

## <a name="mount-the-share-in-your-cluster"></a>Montaje del recurso compartido en el clúster

A continuación, el recurso compartido de archivos debe montarse en todas las máquinas virtuales del clúster. Esta tarea se completa con el protocolo o la herramienta cifs. La operación de montaje puede realizarse manualmente en cada nodo del clúster o al ejecutar un script en cada nodo del clúster.

En este ejemplo, se ejecutan dos scripts, uno para montar el recurso compartido de archivos de Azure y otro para ejecutar este script en cada nodo del clúster de DC/OS.

En primer lugar, se necesitan el nombre de la cuenta de almacenamiento de Azure y la clave de acceso. Ejecute los siguientes comandos para obtener esta información. Tome nota de los valores, se usan en un paso posterior.

Nombre de la cuenta de almacenamiento:

```azurecli-interactive
STORAGE_ACCT=$(az storage account list --resource-group $DCOS_PERS_RESOURCE_GROUP --query "[?contains(name, '$DCOS_PERS_STORAGE_ACCOUNT_NAME')].[name]" -o tsv)
echo $STORAGE_ACCT
```

Clave de acceso de la cuenta de almacenamiento:

```azurecli-interactive
az storage account keys list --resource-group $DCOS_PERS_RESOURCE_GROUP --account-name $STORAGE_ACCT --query "[0].value" -o tsv
```

A continuación, obtenga el FQDN del patrón de DC/OS y guárdelo en una variable.

```azurecli-interactive
FQDN=$(az acs list --resource-group $DCOS_PERS_RESOURCE_GROUP --query "[0].masterProfile.fqdn" --output tsv)
```

Copie la clave privada en el nodo principal. Esta clave es necesaria para crear una conexión ssh con todos los nodos del clúster. Actualice el nombre de usuario si se utilizó un valor no predeterminado al crear el clúster. 

```azurecli-interactive
scp ~/.ssh/id_rsa azureuser@$FQDN:~/.ssh
```

Cree una conexión SSH para el patrón (o el primer patrón) del clúster basado en DC/OS. Actualice el nombre de usuario si se utilizó un valor no predeterminado al crear el clúster.

```azurecli-interactive
ssh azureuser@$FQDN
```

Cree un archivo denominado **cifsMount.sh** y copie en él el contenido siguiente. 

Este script sirve para montar el recurso compartido de archivos de Azure. Actualice las variables `STORAGE_ACCT_NAME` y `ACCESS_KEY` con la información recopilada anteriormente.

```azurecli-interactive
#!/bin/bash

# Azure storage account name and access key
SHARE_NAME=dcosshare
STORAGE_ACCT_NAME=mystorageaccount
ACCESS_KEY=mystorageaccountKey

# Install the cifs utils, should be already installed
sudo apt-get update && sudo apt-get -y install cifs-utils

# Create the local folder that will contain our share
if [ ! -d "/mnt/share/$SHARE_NAME" ]; then sudo mkdir -p "/mnt/share/$SHARE_NAME" ; fi

# Mount the share under the previous local folder created
sudo mount -t cifs //$STORAGE_ACCT_NAME.file.core.windows.net/$SHARE_NAME /mnt/share/$SHARE_NAME -o vers=3.0,username=$STORAGE_ACCT_NAME,password=$ACCESS_KEY,dir_mode=0777,file_mode=0777
```
Cree un segundo archivo denominado **getNodesRunScript.sh** y copie el siguiente contenido en él. 

Este script detecta todos los nodos de clúster y ejecuta el script **cifsMount.sh** para montar el recurso compartido de archivos en cada uno de ellos.

```azurecli-interactive
#!/bin/bash

# Install jq used for the next command
sudo apt-get install jq -y

# Get the IP address of each node using the mesos API and store it inside a file called nodes
curl http://leader.mesos:1050/system/health/v1/nodes | jq '.nodes[].host_ip' | sed 's/\"//g' | sed '/172/d' > nodes

# From the previous file created, run our script to mount our share on each node
cat nodes | while read line
do
  ssh `whoami`@$line -o StrictHostKeyChecking=no < ./cifsMount.sh
  done
```

Ejecute el script para montar el recurso compartido de archivos de Azure en todos los nodos del clúster.

```azurecli-interactive
sh ./getNodesRunScript.sh
```  

Ahora se puede acceder al recurso compartido de archivos en `/mnt/share/dcosshare` en cada nodo del clúster.

## <a name="next-steps"></a>Pasos siguientes

En este tutorial se puso a disposición un recurso compartido de archivos de Azure para un clúster de DC/OS mediante los pasos:

> [!div class="checklist"]
> * Creación de una cuenta de Azure Storage
> * Creación de un recurso compartido de archivos
> * Montaje del recurso compartido en el clúster de DC/OS

Avance hasta el siguiente tutorial para aprender a integrar Azure Container Registry con DC/OS en Azure.  

> [!div class="nextstepaction"]
> [Aplicaciones de equilibrio de carga](container-service-dcos-acr.md)
