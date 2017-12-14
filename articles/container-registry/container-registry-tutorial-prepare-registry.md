---
title: "Tutorial de Azure Container Registry: preparar un registro de contenedor de Azure con replicación geográfica"
description: "Crear un registro de contenedor de Azure, configurar la replicación geográfica, preparar una imagen de Docker e implementarla en el registro. Primera parte de una serie de tres partes."
services: container-registry
author: mmacy
manager: timlt
ms.service: container-registry
ms.topic: tutorial
ms.date: 10/26/2017
ms.author: marsma
ms.custom: mvc
ms.openlocfilehash: 7ae0fbf5f7566bd3f1f6591501b8b004a1e5cb0f
ms.sourcegitcommit: b07d06ea51a20e32fdc61980667e801cb5db7333
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/08/2017
---
# <a name="prepare-a-geo-replicated-azure-container-registry"></a>Preparar un registro de contenedor de Azure con replicación geográfica

Un registro de contenedor de Azure es un registro de Docker privado implementado en Azure que puede mantener cerca de la red en las implementaciones. En este conjunto de artículos de tres tutoriales, aprenderá a usar la replicación geográfica para implementar una aplicación web de ASP.NET Core que se ejecuta en un contenedor de Linux en dos instancias de [Web App for Containers](../app-service/containers/index.yml). Podrá ver cómo Azure implementa automáticamente la imagen en cada instancia de Web App desde el repositorio de replicación geográfica más cercano.

Esta es la primera parte de este tutorial de tres partes:

> [!div class="checklist"]
> * Crear un registro de contenedor de Azure con replicación geográfica
> * Clonar el código fuente de la aplicación desde GitHub
> * Compilar una imagen de contenedor de Docker desde el origen de la aplicación
> * Insertar la imagen de contenedor en el registro

En los siguientes tutoriales implementará el contenedor desde el registro privado en una aplicación web que se ejecute en dos regiones de Azure. A continuación, actualizará el código en la aplicación y ambas instancias de Web App con un solo elemento `docker push` en el registro.

## <a name="before-you-begin"></a>Antes de empezar

Para realizar este tutorial es necesario que ejecute la versión 2.0.20 o superior de la CLI de Azure. Ejecute `az --version` para encontrar la versión. Si necesita instalarla o actualizarla, consulte [Instalación de la CLI de Azure 2.0]( /cli/azure/install-azure-cli).

En este tutorial se supone que el usuario tiene un conocimiento básico de los principales conceptos de Docker, como los contenedores, las imágenes de contenedor y los comandos básicos de Docker. Si es necesario, consulte la [introducción a Docker]( https://docs.docker.com/get-started/), donde encontrará datos básicos acerca de los contenedores.

Para completar este tutorial, se necesita un entorno de desarrollo de Docker. Docker proporciona paquetes que permiten configurar Docker fácilmente en cualquier sistema [Mac](https://docs.docker.com/docker-for-mac/), [Windows](https://docs.docker.com/docker-for-windows/) o [Linux](https://docs.docker.com/engine/installation/#supported-platforms).

Azure Cloud Shell no incluye los componentes de Docker necesarios para completar cada paso de este tutorial. Por lo tanto, le recomendamos que realice una instalación local del entorno de desarrollo de la CLI de Azure y Docker.

> [!IMPORTANT]
> La característica de replicación geográfica de Azure Container Registry está actualmente en su **versión preliminar**. Las versiones preliminares están a su disposición con la condición de que acepte los [términos de uso adicionales](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Es posible que algunos de los aspectos de esta característica cambien antes de ofrecer disponibilidad general.
>

## <a name="create-a-container-registry"></a>Creación de un registro de contenedor

Inicie sesión en el [Portal de Azure](http://portal.azure.com).

Seleccione **Nuevo** > **Contenedores** > **Azure Container Registry**.

![Creación de un registro de contenedor en Azure Portal][tut-portal-01]

Configure el registro nuevo con la configuración siguiente:

* **Nombre del registro**: cree un nombre de registro único global en Azure y que contenga entre 5 y 50 caracteres alfanuméricos.
* **Grupo de recursos**: **Crear nuevo** > `myResourceGroup`.
* **Ubicación**: `West US`.
* **Usuario administrador**: `Enable` (es necesario para que Web App for Containers extraiga imágenes).
* **SKU**: `Premium` (es necesario para la replicación geográfica).

Seleccione **Crear** para implementar la instancia de ACR.

![Creación de un registro de contenedor en Azure Portal][tut-portal-02]

En el resto de este tutorial, usaremos `<acrName>` como marcador de posición del **nombre del registro** del contenedor que eligió.

> [!TIP]
> Dado que los registros de contenedor de Azure son normalmente recursos de larga duración que se usan en varios hosts de contenedor, le recomendamos que cree el registro en su propio grupo de recursos. Al configurar webhooks y registros con replicación geográfica, estos recursos adicionales se colocan en el mismo grupo de recursos.
>

## <a name="configure-geo-replication"></a>Configuración de la replicación geográfica

Ahora que tiene un registro Premium, puede configurar la replicación geográfica. La aplicación web (la cual configurará en el tutorial siguiente para que se ejecute en dos regiones) puede extraer las imágenes de contenedor del registro más cercano.

Vaya al nuevo registro de contenedor de Azure Portal y seleccione **Replicaciones** en **SERVICIOS**:

![Replicaciones en la interfaz de usuario del registro de contenedor en Azure Portal][tut-portal-03]

Se muestra un mapa con hexágonos verdes que representan las regiones de Azure disponibles para realizar la replicación geográfica:

 ![Mapa de regiones de Azure Portal][tut-map-01]

Para replicar el registro en la región del Este de EE. UU., seleccione el hexágono verde correspondiente y, a continuación, seleccione **Crear** en **Crear replicación**:

 ![Interfaz de usuario de la creación de la replicación en Azure Portal][tut-portal-04]

Una vez completada la replicación, el portal mostrará el mensaje *Listo* para ambas regiones. Use el botón **Actualizar** para actualizar el estado de la replicación; tenga en cuenta que tal vez tenga que esperar un par de minutos hasta que se complete la creación y sincronización de las réplicas.

![Interfaz de usuario del estado de la replicación en Azure Portal][tut-portal-05]

## <a name="container-registry-login"></a>Inicio de sesión en Container Registry

Ahora que ha configurado la replicación geográfica, compile una imagen de contenedor e insértela en el registro. Primero debe iniciar sesión en la instancia de ACR antes de insertar imágenes en ella. Con las [SKU de tipo Básico, Estándar y Premium ](container-registry-skus.md), puede realizar la autenticación mediante la identidad de Azure.

Use el comando [az acr login](https://docs.microsoft.com/cli/azure/acr#az_acr_login) para autenticarse y almacenar en caché las credenciales del registro. Reemplace `<acrName>` por el nombre del registro que creó en los pasos anteriores.

```azurecli
az acr login --name <acrName>
```

El comando devolverá `Login Succeeded` cuando esté completo.

## <a name="get-application-code"></a>Obtención del código de la aplicación

En el ejemplo de este tutorial se incluye una pequeña aplicación web compilada con [ASP.NET Core](http://dot.net). La aplicación sirve a una página HTML que muestra la región desde la que Azure Container Registry implementó la imagen.

![Aplicación del tutorial tal como se muestra en un explorador][tut-app-01]

Use GIT para descargar el ejemplo en un directorio local y `cd` en el directorio:

```bash
git clone https://github.com/Azure-Samples/acr-helloworld.git
cd acr-helloworld
```

## <a name="update-dockerfile"></a>Actualizar Dockerfile

El archivo Dockerfile que se proporciona en el ejemplo muestra cómo se compila el contenedor. Se inicia desde una imagen oficial [aspnetcore](https://store.docker.com/community/images/microsoft/aspnetcore), copia los archivos de aplicación en el contenedor, instala las dependencias, compila los elementos de salida con la imagen oficial [aspnetcore-build](https://store.docker.com/community/images/microsoft/aspnetcore-build) y, finalmente, compila una imagen optimizada aspnetcore.

El archivo Dockerfile se encuentra en `./AcrHelloworld/Dockerfile` en el origen clonado.

```dockerfile
FROM microsoft/aspnetcore:2.0 AS base
# Update <acrName> with the name of your registry
# Example: uniqueregistryname.azurecr.io
ENV DOCKER_REGISTRY <acrName>.azurecr.io
WORKDIR /app
EXPOSE 80

FROM microsoft/aspnetcore-build:2.0 AS build
WORKDIR /src
COPY *.sln ./
COPY AcrHelloworld/AcrHelloworld.csproj AcrHelloworld/
RUN dotnet restore
COPY . .
WORKDIR /src/AcrHelloworld
RUN dotnet build -c Release -o /app

FROM build AS publish
RUN dotnet publish -c Release -o /app

FROM base AS production
WORKDIR /app
COPY --from=publish /app .
ENTRYPOINT ["dotnet", "AcrHelloworld.dll"]
```

La aplicación en la imagen *acr helloworld* intenta determinar la región desde la que se implementó el contenedor consultando al DNS para obtener información sobre el servidor de inicio de sesión del registro. Debe especificar la dirección URL del servidor de inicio de sesión del registro en la variable de entorno `DOCKER_REGISTRY` que se encuentra en el archivo Dockerfile.

Primero, obtenga la dirección URL del servidor de inicio de sesión del registro con el comando `az acr show`. Reemplace `<acrName>` por el nombre del registro que creó en los pasos anteriores.

```azurecli
az acr show --name <acrName> --query "{acrLoginServer:loginServer}" --output table
```

Salida:

```bash
AcrLoginServer
-----------------------------
uniqueregistryname.azurecr.io
```

A continuación, actualice la línea `DOCKER_REGISTRY` con la dirección URL del servidor de inicio de sesión del registro. En este ejemplo, actualizaremos la línea para reflejar el nombre del registro de ejemplo, *uniqueregistryname*:

```dockerfile
ENV DOCKER_REGISTRY uniqueregistryname.azurecr.io
```

## <a name="build-container-image"></a>Compilar la imagen del contenedor

Ahora que ha actualizado el archivo Dockerfile con la dirección URL del registro, puede usar `docker build` para crear la imagen del contenedor. Ejecute el siguiente comando para compilar la imagen y etiquetarla con la dirección URL de su registro privado; a continuación, vuelva a reemplazar `<acrName>` con el nombre del registro:

```bash
docker build . -f ./AcrHelloworld/Dockerfile -t <acrName>.azurecr.io/acr-helloworld:v1
```

Se muestran varias líneas de salida a medida que se compila la imagen de Docker (aquí se muestra truncada):

```bash
Sending build context to Docker daemon  523.8kB
Step 1/18 : FROM microsoft/aspnetcore:2.0 AS base
2.0: Pulling from microsoft/aspnetcore
3e17c6eae66c: Pulling fs layer
...
Step 18/18 : ENTRYPOINT dotnet AcrHelloworld.dll
 ---> Running in 6906d98c47a1
 ---> c9ca1763cfb1
Removing intermediate container 6906d98c47a1
Successfully built c9ca1763cfb1
Successfully tagged uniqueregistryname.azurecr.io/acr-helloworld:v1
```

Use el comando `docker images` para ver la imagen compilada:

```bash
docker images
```

Salida:

```bash
REPOSITORY                                      TAG    IMAGE ID        CREATED               SIZE
uniqueregistryname.azurecr.io/acr-helloworld    v1     01ac48d5c8cf    About a minute ago    284MB
...
```

## <a name="push-image-to-azure-container-registry"></a>Inserción de imágenes en Azure Container Registry

Por último, use el comando `docker push` para insertar la imagen *acr helloworld* en el registro. Reemplace `<acrName>` por el nombre del registro.

```bash
docker push <acrName>.azurecr.io/acr-helloworld:v1
```

Como ha configurado el registro para la replicación geográfica, la imagen se replica automáticamente en las dos regiones de *Oeste de EE. UU.* y *Este de EE. UU.* con este único comando `docker push`.

Salida:

```bash
The push refers to a repository [uniqueregistryname.azurecr.io/acr-helloworld]
cd54739c444b: Pushed
d6803756744a: Pushed
b7b1f3a15779: Pushed
a89567dff12d: Pushed
59c7b561ff56: Pushed
9a2f9413d9e4: Pushed
a75caa09eb1f: Pushed
v1: digest: sha256:0799014f91384bda5b87591170b1242bcd719f07a03d1f9a1ddbae72b3543970 size: 1792
```

## <a name="next-steps"></a>Pasos siguientes

En este tutorial, creó un registro de contenedor privado con replicación geográfica, compiló una imagen de contenedor y la insertó en el registro. Al seguir los pasos de este tutorial, hizo lo siguiente:

> [!div class="checklist"]
> * Crear un registro de contenedor de Azure con replicación geográfica
> * Clonar el código fuente de la aplicación desde GitHub
> * Compilar una imagen de contenedor de Docker desde el origen de la aplicación
> * Insertar la imagen de contenedor en el registro

Vaya al siguiente tutorial para aprender a implementar el contenedor en varias instancias de Web App for Containers mediante la replicación geográfica y así poder atender las imágenes de forma local.

> [!div class="nextstepaction"]
> [Implementación de una aplicación web desde Azure Container Registry](container-registry-tutorial-deploy-app.md)

<!-- IMAGES -->
[tut-portal-01]: ./media/container-registry-tutorial-prepare-registry/tut-portal-01.png
[tut-portal-02]: ./media/container-registry-tutorial-prepare-registry/tut-portal-02.png
[tut-portal-03]: ./media/container-registry-tutorial-prepare-registry/tut-portal-03.png
[tut-portal-04]: ./media/container-registry-tutorial-prepare-registry/tut-portal-04.png
[tut-portal-05]: ./media/container-registry-tutorial-prepare-registry/tut-portal-05.png
[tut-app-01]: ./media/container-registry-tutorial-prepare-registry/tut-app-01.png
[tut-map-01]: ./media/container-registry-tutorial-prepare-registry/tut-map-01.png
