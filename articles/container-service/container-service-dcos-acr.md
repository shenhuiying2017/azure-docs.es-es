---
title: "Usar ACR con un clúster DC/OS de Azure | Microsoft Docs"
description: "Use un Azure Container Registry con un clúster DC/OS en Azure Container Service"
services: container-service
documentationcenter: 
author: julienstroheker
manager: dcaro
editor: 
tags: acs, azure-container-service, acr, azure-container-registry
keywords: Docker, contenedores, microservicios, Mesos, Azure, recurso compartido de archivos, cifs
ms.assetid: 
ms.service: container-service
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/23/2017
ms.author: juliens
translationtype: Human Translation
ms.sourcegitcommit: 197ebd6e37066cb4463d540284ec3f3b074d95e1
ms.openlocfilehash: a8a3716f8d03b596285026426c7514b7b642cb25
ms.lasthandoff: 03/31/2017


---
# <a name="use-acr-with-a-dcos-cluster-to-deploy-your-application"></a>Use ACR con un clúster de DC/OS para implementar la aplicación

En este artículo, exploraremos cómo usar un registro de contenedor privado como ACR (Azure Container Registry) con un clúster de DC/OS. Usar ACR le permite almacenar imágenes de forma privada y mantener el control en él, como las versiones o las actualizaciones, por ejemplo.

Antes de trabajar con este ejemplo, necesita: 
* Un clúster de DC/OS configurado en Azure Container Service. Vea [Implementación de un clúster de Azure Container Service](container-service-deployment.md).
* Un Azure Container Service implementado. Vea [Creación de un registro de contenedor privado de Docker con Azure Portal](https://docs.microsoft.com/azure/container-registry/container-registry-get-started-portal) o [Creación de un registro de contenedor privado de Docker con la CLI de Azure 2.0](https://docs.microsoft.com/azure/container-registry/container-registry-get-started-azure-cli).
* Un recurso compartido de archivos que está configurado en el clúster de DC/OS. Vea [Creación y montaje de un recurso compartido de archivos en un clúster de DC/OS](container-service-dcos-fileshare.md)
* para aprender a implementar una imagen de Docker en un clúster de DC/OS mediante el uso de la [IU web](container-service-mesos-marathon-ui.md) o la [API de REST](container-service-mesos-marathon-rest.md)

## <a name="manage-the-authentication-inside-your-cluster"></a>Administrar la autenticación en el clúster

La manera convencional de insertar y extraer una imagen de un registro privado es, en primer lugar, autenticarse en él. Para hacerlo, tiene que utilizar la línea de comandos `docker login` en cualquier proceso de cliente de docker que deba utilizar el registro privado.
Cuando se trata del mundo de la producción, usar DC/OS en nuestro caso, le recomendamos garantizar que sea capaz de extraer imágenes desde cualquier nodo. Significa que desea automatizar el proceso de autenticación y no ejecutar la línea de comandos se ejecutan en cada máquina, porque como puede imaginar, según el tamaño del clúster, podría ser una operación problemática y difícil. 

Si damos por hecho que ya [configuró un recurso compartido de archivos en el DC/OS](container-service-dcos-fileshare.md), lo aprovecharemos de la siguiente forma:

### <a name="from-any-client-machine-recommended-method"></a>En cualquier equipo cliente [Método recomendado]

Los siguientes comandos son ejecutables en cualquier entorno (Windows/Mac/Linux):

1. Asegúrese de que cumpla con los siguientes requisitos previos:
  * Herramienta TAR
    * [Windows](http://gnuwin32.sourceforge.net/packages/gtar.htm)
  * Docker 
    * [Windows](https://www.docker.com/docker-windows)
    * [MAC](https://www.docker.com/docker-mac)
    * [Ubuntu](https://www.docker.com/docker-ubuntu)
    * [Otros](https://www.docker.com/get-docker)
  * Recurso compartido de archivos montados dentro de su clúster, [con el siguiente método](container-service-dcos-fileshare.md)

2. Inicie la autenticación en el servicio ACR mediante el siguiente comando con el terminal favorito: `sudo docker login --username=<USERNAME> --password=<PASSWORD> <ACR-REGISTRY-NAME>.azurecr.io`. Tiene que reemplazar las variables `USERNAME`, `PASSWORD` y `ACR-REGISTRY-NAME` con los valores proporcionados en Azure Portal.

3. Es interesante saber que cuando realiza una operación `docker login`, los valores se almacenan localmente en el equipo en la carpeta de inicio (`cd ~/.docker` en Mac y Linux o `cd %HOMEPATH%` en Windows). Se comprimirá el contenido de esta carpeta mediante el uso del comando `tar czf`.

4. El paso final consiste en copiar el archivo tar que se acaba de crear, en el recurso compartido de archivos [que debe haber creado como requisito previo](container-service-dcos-fileshare.md). Para ello, puede usar Azure-CLI con el siguiente comando `az storage file upload -s <shareName> --account-name <storageAccountName> --account-key <storageAccountKey> -source <pathToTheTarFile>`

Para resumir, este es un ejemplo de cómo utilizar la siguiente configuración (con un entorno de Windows):
* Nombre de ACR:**`demodcos`**
* Nombre de usuario: **`demodcos`**
* Contraseña: **`+js+/=I1=L+D=+eRpU+/=wI/AjvDo=J0`**
* Nombre de la cuenta de almacenamiento: **`anystorageaccountname`**
* Clave de la cuenta de almacenamiento: **`aYGl6Nys4De5J3VPldT1rXxz2+VjgO7dgWytnoWClurZ/l8iO5c5N8xXNS6mpJhSc9xh+7zkT7Mr+xIT4OIVMg==`**
* Nombre para compartir creado en la cuenta de almacenamiento: **`share`**
* Ruta de acceso del archivo tar para cargar:**`%HOMEPATH%/.docker/docker.tar.gz`**

```bash
# Changing directory to the home folder of the default user
cd %HOMEPATH%

# Authentication into my ACR
docker login --username=demodcos --password=+js+/=I1=L+D=+eRpU+/=wI/AjvDo=J0 demodcos.azurecr.io

# Tar the contains of the .docker folder
tar czf docker.tar.gz .docker

# Upload the tar archive in the fileshare
az storage file upload -s share --account-name anystorageaccountname --account-key aYGl6Nys4De5J3VPldT1rXxz2+VjgO7dgWytnoWClurZ/l8iO5c5N8xXNS6mpJhSc9xh+7zkT7Mr+xIT4OIVMg== --source %HOMEPATH%/docker.tar.gz
```

### <a name="from-the-master-not-recommended-method"></a>Desde el maestro [método no recomendado]

Ejecutar la operación desde el maestro no se recomienda para evitar errores y el impacto en todos los entornos.

1. En primer lugar, SSH para el maestro (o el primer maestro) del clúster basado en DC/OS. Por ejemplo, `ssh userName@masterFQDN –A –p 22`, donde el nombre de dominio completo (FQDN) maestro es el mismo que el de la máquina virtual maestra. [Haga clic aquí para obtener más información](https://docs.microsoft.com/azure/container-service/container-service-connect#connect-to-a-dcos-or-swarm-cluster)

2. Inicie la autenticación en el servicio ACR mediante el siguiente comando: `sudo docker login --username=<USERNAME> --password=<PASSWORD> <ACR-REGISTRY-NAME>.azurecr.io`. Tiene que reemplazar las variables `USERNAME`, `PASSWORD` y `ACR-REGISTRY-NAME` con los valores proporcionados en Azure Portal.

3. Es interesante saber que cuando realiza una operación `docker login`, los valores se almacenan localmente en el equipo en la carpeta de inicio `~/.docker`. Se comprimirá el contenido de esta carpeta mediante el uso del comando `tar czf`.

4. El paso final consiste en copiar el archivo tar que se acaba de crear, en el recurso compartido de archivos. Esta operación permitirá, en todas las máquinas virtuales dentro de nuestro clúster, usar esta credencial y autenticarse en el Azure Container Registry.

Para resumir, este es un ejemplo de cómo utilizar la siguiente configuración:
* Nombre de ACR:**`demodcos`**
* Nombre de usuario: **`demodcos`**
* Contraseña: **`+js+/=I1=L+D=+eRpU+/=wI/AjvDo=J0`**
* Punto de montaje dentro del clúster:**`/mnt/share`**

```bash
# Changing directory to the home folder of the default user
cd ~

# Authentication into my ACR
sudo docker login --username=demodcos --password=+js+/=I1=L+D=+eRpU+/=wI/AjvDo=J0 demodcos.azurecr.io

# Tar the contains of the .docker folder
sudo tar czf docker.tar.gz .docker

# Copy of the tar file in the file share of my cluster
sudo cp docker.tar.gz /mnt/share
```


## <a name="deploy-an-image-from-acr-with-marathon"></a>Implementar una imagen de ACR con Marathon

Supuestamente, ya insertó las imágenes que quería implementar en el registro de contenedor. Consulte [Inserción de la primera imagen en un registro de contenedor privado de Docker mediante la CLI de Docker](https://docs.microsoft.com/azure/container-registry/container-registry-get-started-docker-cli)

Supongamos que queremos implementar la imagen **simple-web**, con la etiqueta **2.1**, de nuestro registro privado alojado en Azure (ACR). Se usará la siguiente configuración:

```json
{
  "id": "myapp",
  "container": {
    "type": "DOCKER",
    "docker": {
      "image": "demodcos.azurecr.io/simple-web:2.1",
      "network": "BRIDGE",
      "portMappings": [
        { "hostPort": 0, "containerPort": 80, "servicePort": 10000 }
      ],
      "forcePullImage":true
    }
  },
  "instances": 3,
  "cpus": 0.1,
  "mem": 65,
  "healthChecks": [{
      "protocol": "HTTP",
      "path": "/",
      "portIndex": 0,
      "timeoutSeconds": 10,
      "gracePeriodSeconds": 10,
      "intervalSeconds": 2,
      "maxConsecutiveFailures": 10
  }],
  "labels":{
    "HAPROXY_GROUP":"external",
    "HAPROXY_0_VHOST":"YOUR FQDN",
    "HAPROXY_0_MODE":"http"
  },
  "uris":  [
       "file:///mnt/share/docker.tar.gz"
   ]
}
```

> [!NOTE] 
> Como puede ver, usamos la opción **uris** para especificar donde se almacenan nuestras credenciales.
>

## <a name="next-steps"></a>Pasos siguientes
* Lea sobre cómo [administrar los contenedores de DC/OS](container-service-mesos-marathon-ui.md).
* Administración de contenedores de DC/OS a través de la [API de REST de Marathon](container-service-mesos-marathon-rest.md).
