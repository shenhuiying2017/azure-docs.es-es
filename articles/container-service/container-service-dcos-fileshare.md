---
title: "Recurso compartido de archivos para clústeres de DC/OS en Azure | Microsoft Docs"
description: "Creación y montaje de un recurso compartido de archivos en un clúster de DC/OS en Azure Container Service"
services: container-service
documentationcenter: 
author: julienstroheker
manager: dcaro
editor: 
tags: acs, azure-container-service
keywords: Docker, contenedores, microservicios, Mesos, Azure, recurso compartido de archivos, cifs
ms.assetid: 
ms.service: container-service
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/14/2017
ms.author: juliens
translationtype: Human Translation
ms.sourcegitcommit: 424d8654a047a28ef6e32b73952cf98d28547f4f
ms.openlocfilehash: 6d40821327a9df47bb85ea12ecd33e4a0f49e39e
ms.lasthandoff: 03/22/2017


---
# <a name="create-and-mount-a-file-share-to-a-dcos-cluster"></a>Creación y montaje de un recurso compartido de archivos en un clúster de DC/OS
En este artículo, se explica cómo crear un recurso compartido de archivos en Azure y cómo montarlo en cada agente y patrón del clúster de DC/OS. La configuración de un recurso compartido de archivos facilita el uso compartido de estos recursos entre el clúster, como la configuración, el acceso, los registros y mucho más.

Antes de trabajar con estos ejemplos, necesita un clúster de DC/OS configurado en Azure Container Service. Vea [Implementación de un clúster de Azure Container Service](container-service-deployment.md).

## <a name="create-a-file-share-on-microsoft-azure"></a>Creación de un recurso compartido de archivos en Microsoft Azure
### <a name="using-the-portal"></a>Uso del portal

1. Inicie sesión en el portal.
2. Cree una cuenta de almacenamiento.
   
  ![Azure Container Service crea una cuenta de almacenamiento](media/container-service-dcos-fileshare/createSA.png)

3. Cuando la cree, haga clic en **Archivos** en la sección **Servicios**.
   
  ![Sección Archivos de Azure Container Service](media/container-service-dcos-fileshare/filesServices.png)

4. Haga clic en **+ Recurso compartido de archivos** y escriba un nombre para el nuevo recurso compartido de archivos (la **cuota** no es obligatoria).
   
  ![+ Recurso compartido de archivos en Azure Container Service](media/container-service-dcos-fileshare/newFileShare.png)  

### <a name="using-azure-cli-20"></a>Uso de la CLI de Azure 2.0

Si resulta necesario, [instale y configure la CLI de Azure](/cli/azure/install-azure-cli.md).

```azurecli
################# Change these four parameters ##############
DCOS_PERS_STORAGE_ACCOUNT_NAME=anystorageaccountname
DCOS_PERS_RESOURCE_GROUP=AnyResourceGroupName
DCOS_PERS_LOCATION=eastus
DCOS_PERS_SHARE_NAME=demoshare
#############################################################

# Create the storage account with the parameters
az storage account create -n $DCOS_PERS_STORAGE_ACCOUNT_NAME -g $DCOS_PERS_RESOURCE_GROUP -l $DCOS_PERS_LOCATION --sku Standard_LRS

# Export the connection string as an environment variable
export AZURE_STORAGE_CONNECTION_STRING=`az storage account show-connection-string -n $DCOS_PERS_STORAGE_ACCOUNT_NAME -g $DCOS_PERS_RESOURCE_GROUP -o tsv`

# Create the share
az storage share create -n $DCOS_PERS_SHARE_NAME
```

## <a name="mount-the-share-in-your-cluster"></a>Montaje del recurso compartido en el clúster

A continuación, es necesario montar este recurso compartido en cada máquina virtual del clúster mediante el protocolo o la herramienta cifs. Para ello, ejecute la siguiente línea de comandos: `mount -t cifs`.

A continuación se muestra un ejemplo que usa:
* El nombre de la cuenta de almacenamiento **`anystorageaccountname`**
* La clave de cuenta ficticia **`P/GuXXXuoRtIVsV+faSfLhuNyZDrTzPmZDm3RyCL4XS6ghyiHYriN12gl+w5JMN2gXGtOhCzxFf2JuGqXXXX1w==`** 
* El punto de montaje **`/mnt/share/demoshare`**

```bash
sudo mount -t cifs //anystorageaccountname.file.core.windows.net/demoshare /mnt/share/demoshare -o vers=3.0,username=anystorageaccountname,password=P/GuXXXuoRtIVsV+faSfLhuNyZDrTzPmZDm3RyCL4XS6ghyiHYriN12gl+w5JMN2gXGtOhCzxFf2JuGqXXXX1w==,dir_mode=0777,file_mode=0777
```

Este comando se ejecutará en cada máquina virtual del clúster (nodos maestro y agente). Si tiene un gran número de agentes, se recomienda automatizar este proceso mediante la creación de script.  

### <a name="set-up-scripts"></a>Configuración de script

1. En primer lugar, SSH para el maestro (o el primer maestro) del clúster basado en DC/OS. Por ejemplo, `ssh userName@masterFQDN –A –p 22`, donde el nombre de dominio completo (FQDN) maestro es el mismo que el de la máquina virtual maestra.

2. Copie la clave privada en el directorio de trabajo (~) del maestro.

3. Cambie los permisos con el comando siguiente: `chmod 600 yourPrivateKeyFile`.

4. Importe la clave privada con el comando `ssh-add yourPrivateKeyFile`. Puede que tenga que ejecutar `eval ssh-agent -s` si no funciona la primera vez.

5. Desde el maestro, cree dos archivos con su editor favorito, como vi, nano o vim: 
  
  * Uno con el script para ejecutarlo en cada máquina virtual, denominado **cifsMount.sh** 
  * Otro para iniciar todas las conexiones ssh que llamarán al primer script, denominado **mountShares.sh**


```bash
# cifsMount.sh

# Install the cifs utils, should be already installed
sudo apt-get update && sudo apt-get -y install cifs-utils

# Create the local folder that will contain our share
if [ ! -d "/mnt/share/demoshare" ]; then sudo mkdir -p "/mnt/share/demoshare" ; fi

# Mount the share under the previous local folder created
sudo mount -t cifs //anystorageaccountname.file.core.windows.net/demoshare /mnt/share/demoshare -o vers=3.0,username=anystorageaccountname,password=P/GuXXXuoRtIVsV+faSfLhuNyZDrTzPmZDm3RyCL4XS6ghyiHYriN12gl+w5JMN2gXGtOhCzxFf2JuGqXXXX1w==,dir_mode=0777,file_mode=0777
```
  
```bash
# mountShares.sh

# Install jq used for the next command
sudo apt-get install jq

# Create the local folder that will contain our share
if [ ! -d "/mnt/share/demoshare" ]; then sudo mkdir -p "/mnt/share/demoshare" ; fi

# Mount the share on the current vm (master)
sudo mount -t cifs //anystorageaccountname.file.core.windows.net/demoshare /mnt/share/demoshare -o vers=3.0,username=anystorageaccountname,password=P/GuXXXuoRtIVsV+faSfLhuNyZDrTzPmZDm3RyCL4XS6ghyiHYriN12gl+w5JMN2gXGtOhCzxFf2JuGqXXXX1w==,dir_mode=0777,file_mode=0777

# Get the IP address of each node using the mesos API and store it inside a file called nodes
curl http://leader.mesos:1050/system/health/v1/nodes | jq '.nodes[].host_ip' | sed 's/\"//g' | sed '/172/d' > nodes
  
# From the previous file created, run our script to mount our share on each node
cat nodes | while read line
  do
    ssh `whoami`@$line -o StrictHostKeyChecking=no -i yourPrivateKeyFile < ./cifsMount.sh
    done
```  
> [!IMPORTANT]
> Debe cambiar el comando **'mount'** con su propia configuración, como el nombre de la cuenta de almacenamiento y la contraseña.
>  

La carpeta donde creó los script anteriores debe contener ahora 3 archivos:  

* **cifsMount.sh**
* **mountShares.sh**
* **yourPrivateKeyFile** 

### <a name="run-the-scripts"></a>Ejecución de los scripts

Ejecute el archivo **mountShares.sh** con el comando siguiente: `sh mountShares.sh`.

Debería ver la impresión de resultados en el terminal. Después de que el script se completa, puede usar el recurso compartido de archivos en el clúster.

Puede optimizar los scripts, pero se trata de un ejemplo sencillo, cuya finalidad es proporcionar orientación.

> [!NOTE] 
> Este método no se recomienda para escenarios que requieren un número elevado de E/S por segundo, pero resulta muy útil para compartir documentos e información en el clúster.
>

## <a name="next-steps"></a>Pasos siguientes
* Lea sobre cómo [administrar los contenedores de DC/OS](container-service-mesos-marathon-ui.md).
* Administración de contenedores de DC/OS a través de la [API de REST de Marathon](container-service-mesos-marathon-rest.md).
