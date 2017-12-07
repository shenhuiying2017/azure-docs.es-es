---
title: "Crear imágenes de contenedor para Azure Service Fabric | Microsoft Docs"
description: "Aprenda a crear imágenes de contenedor para una aplicación de Service Fabric con contenedor múltiples."
services: service-fabric
documentationcenter: 
author: suhuruli
manager: timlt
editor: suhuruli
tags: servicefabric
keywords: Docker, Containers, microservicios, Service Fabric, Azure
ms.assetid: 
ms.service: service-fabric
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/15/2017
ms.author: suhuruli
ms.custom: mvc
ms.openlocfilehash: ecb70b88f6548e4730bcc1578de2f748cda33b0a
ms.sourcegitcommit: 5a6e943718a8d2bc5babea3cd624c0557ab67bd5
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/01/2017
---
# <a name="create-container-images-for-service-fabric"></a>Crear imágenes de contenedor para Service Fabric

Este tutorial es la primera parte de una serie de tutoriales en los que se muestra cómo usar los contenedores en un clúster de Service Fabric en Linux. En este tutorial, se prepara una aplicación con varios contenedores para usarla con Service Fabric. En los tutoriales posteriores, estas imágenes se usan como parte de una aplicación de Service Fabric. En este tutorial, aprenderá a: 

> [!div class="checklist"]
> * Clonar el origen de la aplicación de GitHub  
> * Crear una imagen de contenedor a partir del origen de la aplicación
> * Implementar una instancia de Azure Container Registry (ACR)
> * Etiquetar una imagen de contenedor para ACR
> * Cargar la imagen a ACR

En esta serie de tutoriales, se aprende a: 

> [!div class="checklist"]
> * Crear imágenes de contenedor para Service Fabric
> * [Creación y ejecución de una aplicación de Service Fabric con Containers](service-fabric-tutorial-package-containers.md)
> * [Cómo se administran la conmutación por error y el escalado en Service Fabric](service-fabric-tutorial-containers-failover.md)

## <a name="prerequisites"></a>Requisitos previos

- Configuración del entorno de desarrollo de Linux para Service Fabric. Siga las instrucciones que se indican [aquí](service-fabric-get-started-linux.md) para configurar el entorno de Linux. 
- Para realizar este tutorial es necesario que ejecute la versión 2.0.4 o superior de la CLI de Azure. Ejecute `az --version` para encontrar la versión. Si necesita instalarla o actualizarla, consulte [Instalación de la CLI de Azure 2.0]( /cli/azure/install-azure-cli). 
- Además, requiere que tenga una suscripción de Azure disponible. Para obtener más información sobre una versión de evaluación gratuita, vaya [aquí](https://azure.microsoft.com/free/).

## <a name="get-application-code"></a>Obtención del código de la aplicación

La aplicación de ejemplo que se usa en este tutorial es una aplicación para votar. La aplicación consta de un componente web front-end y de una instancia back-end de Redis. Los componentes se empaquetan en imágenes de contenedor. 

Use git para descargar una copia de la aplicación en su entorno de desarrollo.

```bash
git clone https://github.com/Azure-Samples/service-fabric-containers.git

cd service-fabric-containers/Linux/container-tutorial/
```

El directorio "container-tutorial" contiene una carpeta denominada "azure-vote". La carpeta "azure-vote" contiene el código fuente de front-end y un archivo Dockerfile para compilar el front-end. El directorio "container-tutorial" también contiene el directorio "redis", que tiene el archivo Dockerfile para generar la imagen de Redis. Estos directorios contienen los recursos necesarios para este conjunto de tutorial. 

## <a name="create-container-images"></a>Creación de imágenes de contenedor

En el directorio "azure-vote", ejecute el siguiente comando para generar la imagen para el componente web de front-end. Este comando usa el archivo Dockerfile de este directorio para generar la imagen. 

```bash
docker build -t azure-vote-front .
```

En el directorio "redis", ejecute el siguiente comando para generar la imagen para el back-end de redis. Este comando usa el archivo Dockerfile del directorio para generar la imagen. 

```bash
docker build -t azure-vote-back .
```

Cuando haya finalizado, use el comando [docker images](https://docs.docker.com/engine/reference/commandline/images/) para ver las imágenes creadas.

```bash
docker images
```

Tenga en cuenta que se han descargado o creado cuatro imágenes. La imagen *azure-vote-front* contiene la aplicación. Se derivó de una imagen de *Python* de Docker Hub. La imagen de Redis se descargó de Docker Hub.

```bash
REPOSITORY                   TAG                 IMAGE ID            CREATED              SIZE
azure-vote-back              latest              bf9a858a9269        3 seconds ago        107MB
azure-vote-front             latest              052c549a75bf        About a minute ago   708MB
redis                        latest              9813a7e8fcc0        2 days ago           107MB
tiangolo/uwsgi-nginx-flask   python3.6           590e17342131        5 days ago           707MB

```

## <a name="deploy-azure-container-registry"></a>Implementación de Azure Container Registry

Primero, ejecute el comando [az login](/cli/azure/login) para iniciar sesión en su cuenta de Azure. 

A continuación, use el comando [az account](/cli/azure/account#set) para elegir la suscripción para crear Azure Container Registry. 

```bash
az account set --subscription <subscription_id>
```

Para implementar Azure Container Registry, necesita tener antes un grupo de recursos. Un grupo de recursos de Azure es un contenedor lógico en el que se implementan y se administran los recursos de Azure.

Cree un grupo de recursos con el comando [az group create](/cli/azure/group#create). En este ejemplo, se crea un grupo de recursos denominado *myResourceGroup* en la región *westus*. Elija el grupo de recursos en una región cerca de usted. 

```bash
az group create --name myResourceGroup --location westus
```

Cree una instancia de Azure Container Registry con el comando [az acr create](/cli/azure/acr#create). El nombre de una instancia de Container Registry **debe ser único**.

```bash
az acr create --resource-group myResourceGroup --name <acrName> --sku Basic --admin-enabled true
```

En el resto de este tutorial, usamos "acrname" como un marcador de posición para el nombre del registro del contenedor que eligió.

## <a name="log-in-to-your-container-registry"></a>Iniciar sesión en el registro de contenedor

Inicie sesión en la instancia de ACR antes de insertar imágenes en ella. Use el comando [az acr login](/cli/azure/acr?view=azure-cli-latest#az_acr_login) para completar la operación. Proporcione el nombre único que se especificó para el registro de contenedor cuando se creó.

```bash
az acr login --name <acrName>
```

Al finalizar, el comando devuelve un mensaje que indica que el inicio de sesión se ha realizado correctamente.

## <a name="tag-container-images"></a>Etiquetado de imágenes de contenedor

Es preciso etiquetar cada imagen de contenedor con el nombre loginServer del registro. Esta etiqueta se usa para el enrutamiento al insertar imágenes de contenedor en un registro de imágenes.

Para ver una lista de imágenes actual, use el comando [docker images](https://docs.docker.com/engine/reference/commandline/images/).

```bash
docker images
```

Salida:

```bash
REPOSITORY                   TAG                 IMAGE ID            CREATED              SIZE
azure-vote-back              latest              bf9a858a9269        3 seconds ago        107MB
azure-vote-front             latest              052c549a75bf        About a minute ago   708MB
redis                        latest              9813a7e8fcc0        2 days ago           107MB
tiangolo/uwsgi-nginx-flask   python3.6           590e17342131        5 days ago           707MB
```

Para obtener el nombre de loginServer, ejecute el comando siguiente:

```bash
az acr show --name <acrName> --query loginServer --output table
```

Ahora, etiquete la imagen *azure-vote-front* con el loginServer del registro de contenedor. Además, agregue `:v1` al final del nombre de la imagen. Esta etiqueta indica la versión de la imagen.

```bash
docker tag azure-vote-front <acrLoginServer>/azure-vote-front:v1
```

A continuación, etiquete la imagen *azure-vote-back* con el loginServer del registro de contenedor. Además, agregue `:v1` al final del nombre de la imagen. Esta etiqueta indica la versión de la imagen.

```bash
docker tag azure-vote-back <acrLoginServer>/azure-vote-back:v1
```

Una vez etiquetada, ejecute "docker images" para comprobar la operación.


Salida:

```bash
REPOSITORY                             TAG                 IMAGE ID            CREATED             SIZE
azure-vote-back                        latest              bf9a858a9269        22 minutes ago      107MB
<acrName>.azurecr.io/azure-vote-back    v1                  bf9a858a9269        22 minutes ago      107MB
azure-vote-front                       latest              052c549a75bf        23 minutes ago      708MB
<acrName>.azurecr.io/azure-vote-front   v1                  052c549a75bf        23 minutes ago      708MB
redis                                  latest              9813a7e8fcc0        2 days ago          107MB
tiangolo/uwsgi-nginx-flask             python3.6           590e17342131        5 days ago          707MB

```

## <a name="push-images-to-registry"></a>Inserción de imágenes en el registro

Inserte la imagen *azure-vote-front* en el registro. 

En el siguiente ejemplo, reemplace el nombre loginServer de ACR por el loginServer de su entorno.

```bash
docker push <acrLoginServer>/azure-vote-front:v1
```

Inserte la imagen *azure-vote-back* en el registro. 

En el siguiente ejemplo, reemplace el nombre loginServer de ACR por el loginServer de su entorno.

```bash
docker push <acrLoginServer>/azure-vote-back:v1
```

Los comandos de inserción de Docker tardan un par de minutos en completarse.

## <a name="list-images-in-registry"></a>Lista de imágenes en el registro

Para que se devuelva una lista de las imágenes que se han insertado en la instancia de Azure Container Registry, el usuario debe usar el comando [az acr repository list](/cli/azure/acr/repository#list). Actualice el comando con el nombre de instancia de ACR.

```bash
az acr repository list --name <acrName> --output table
```

Salida:

```bash
Result
----------------
azure-vote-back
azure-vote-front
```

Al finalizar el tutorial, la imagen de contenedor se ha almacenado en una instancia privada de Azure Container Registry. Esta imagen se implementa desde ACR en un clúster de Service Fabric en tutoriales posteriores.

## <a name="next-steps"></a>Pasos siguientes

En este tutorial, se extrajo una aplicación de Github y se crearon e insertaron imágenes de contenedor en un registro. Se han completado los siguientes pasos:

> [!div class="checklist"]
> * Clonar el origen de la aplicación de GitHub  
> * Crear una imagen de contenedor a partir del origen de la aplicación
> * Implementar una instancia de Azure Container Registry (ACR)
> * Etiquetar una imagen de contenedor para ACR
> * Cargar la imagen a ACR

Avance hasta el siguiente tutorial para obtener información acerca del empaquetado de contenedores en una aplicación de Service Fabric mediante Yeoman. 

> [!div class="nextstepaction"]
> [Empaquetar e implementar contenedores como aplicación de Service Fabric](service-fabric-tutorial-package-containers.md)
