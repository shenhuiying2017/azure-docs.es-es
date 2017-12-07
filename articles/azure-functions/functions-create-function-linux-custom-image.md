---
title: "Creación de una función en Linux con una imagen personalizada (versión preliminar) | Microsoft Docs"
description: Aprenda a crear funciones de Azure que se ejecutan en una imagen de Linux personalizada.
services: functions
keywords: 
author: ggailey777
ms.author: glenga
ms.date: 11/15/2017
ms.topic: tutorial
ms.service: functions
ms.custom: mvc
ms.devlang: azure-cli
manager: cfowler
ms.openlocfilehash: 9ba5f45034561f8d897676e8cc4b1a59945403b8
ms.sourcegitcommit: 7136d06474dd20bb8ef6a821c8d7e31edf3a2820
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/05/2017
---
# <a name="create-a-function-on-linux-using-a-custom-image-preview"></a>Creación de una función en Linux con una imagen personalizada (versión preliminar)

Azure Functions le permite hospedar sus funciones en Linux en su propio contenedor personalizado. Actualmente, la versión de esta funcionalidad es una versión preliminar. También puede [hospedarlas en un contenedor de Azure App Service predeterminado](functions-create-first-azure-function-azure-cli-linux.md).  

En este tutorial, aprenderá a implementar una aplicación de función como una imagen de Docker personalizada. Este patrón es útil cuando debe personalizar la imagen de contenedor integrada de App Service. Es posible que quiera usar una imagen personalizada cuando las funciones necesiten una versión de idioma determinada o requieran una dependencia o configuración específica que no se proporciona en la imagen integrada.

En este tutorial se explica cómo usar Azure Functions para crear e insertar una imagen personalizada en Docker Hub. A continuación, usará esta imagen como origen de implementación para una aplicación de función que se ejecuta en Linux. Usará Docker para crear e insertar la imagen. Usará la CLI de Azure para crear una aplicación de función e implementar la imagen desde Docker Hub. 

En este tutorial, aprenderá a:

> [!div class="checklist"]
> * Crear una imagen personalizada con Docker
> * Publicar una imagen personalizada en un registro de contenedor 
> * Cree una cuenta de Azure Storage. 
> * Crear un plan de App Service de Linux 
> * Implementar una aplicación de función desde Docker Hub
> * Agregar la configuración de aplicación a la aplicación de función 

Los pasos siguientes se admiten en equipos Mac, Windows o Linux.  

## <a name="prerequisites"></a>Requisitos previos

Para completar este tutorial, necesita:

* [Git](https://git-scm.com/downloads)
* Una [suscripción de Azure](https://azure.microsoft.com/pricing/free-trial/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) activa
* [Docker](https://docs.docker.com/get-started/#setup)
* Una [cuenta de Docker Hub](https://docs.docker.com/docker-id/)

[!INCLUDE [Free trial note](../../includes/quickstarts-free-trial-note.md)]

## <a name="download-the-sample"></a>Descarga del ejemplo

En una ventana de terminal, ejecute el siguiente comando para clonar el repositorio de la aplicación de ejemplo en el equipo local y, luego, cambie al directorio que contiene el código de ejemplo.

```bash
git clone https://github.com/Azure-Samples/functions-linux-custom-image.git --config core.autocrlf=input
cd functions-linux-custom-image
```

## <a name="build-the-image-from-the-docker-file"></a>Compilación de la imagen desde el archivo de Docker

En este repositorio de Git, eche un vistazo al _Dockerfile_. Este archivo describe el entorno que es necesario para ejecutar la aplicación de función en Linux. 

```docker
# Base the image on the built-in Azure Functions Linux image.
FROM microsoft/azure-functions-runtime:2.0.0-jessie
ENV AzureWebJobsScriptRoot=/home/site/wwwroot

# Add files from this repo to the root site folder.
COPY . /home/site/wwwroot 
```
>[!NOTE]
> Al hospedar una imagen en un registro de contenedor privado, debe agregar la configuración de conexión a la aplicación de función mediante variables **ENV** en el Dockerfile. Como en este tutorial no se puede garantizar que se vaya a usar un registro privado, la configuración de conexión se [agrega después de la implementación mediante la CLI de Azure](#configure-the-function-app) como procedimiento recomendado de seguridad.   

### <a name="run-the-build-command"></a>Ejecución del comando de compilación
Para compilar la imagen de Docker, ejecute el comando `docker build` y proporcione un nombre, `mydockerimage`, y una etiqueta, `v1.0.0`. Reemplace `<docker-id>` por el identificador de su cuenta de Docker Hub.

```bash
docker build --tag <docker-id>/mydockerimage:v1.0.0 .
```

El comando genera una salida similar a la siguiente:

```bash
Sending build context to Docker daemon  169.5kB
Step 1/3 : FROM microsoft/azure-functions-runtime:v2.0.0-jessie
v2.0.0-jessie: Pulling from microsoft/azure-functions-runtime
b178b12f7913: Pull complete
2d9ce077a781: Pull complete
4775d4ba55c8: Pull complete
Digest: sha256:073f45fc167b3b5c6642ef4b3c99064430d6b17507095...
Status: Downloaded newer image for microsoft/azure-functions-runtime:v2.0.0-jessie
 ---> 217799efa500
Step 2/3 : ENV AzureWebJobsScriptRoot /home/site/wwwroot
 ---> Running in 528fa2077d17
 ---> 7cc6323b8ae0
Removing intermediate container 528fa2077d17
Step 3/3 : COPY . /home/site/wwwroot
 ---> 5bdac9878423
Successfully built 5bdac9878423
Successfully tagged ggailey777/mydockerimage:v1.0.0
```

### <a name="test-the-image-locally"></a>Prueba de la imagen de manera local
Compruebe que la imagen integrada funciona mediante la ejecución de la imagen de Docker en un contenedor local. Emita el comando [docker run](https://docs.docker.com/engine/reference/commandline/run/) y transmítale el nombre y la etiqueta de la imagen. Asegúrese de especificar el puerto con el argumento `-p`.

```bash
docker run -p 8080:80 -it <docker-ID>/mydockerimage:v1.0.0
```

Una vez que la imagen personalizada ya se ejecuta en un contenedor de Docker local, compruebe que la aplicación de función y el contenedor funcionan correctamente; para ello, vaya a <http://localhost:8080>.

![Prueba de la aplicación de función de manera local](./media/functions-create-function-linux-custom-image/run-image-local-success.png)

Después de haber comprobado la aplicación de función en el contenedor, detenga la ejecución. Ahora, puede insertar la imagen personalizada en su cuenta de Docker Hub.

## <a name="push-the-custom-image-to-docker-hub"></a>Inserción de la imagen personalizada en Docker Hub

Un registro es una aplicación que hospeda imágenes y proporciona servicios de imágenes y servicios de contenedores. Si desea compartir una imagen, debe insertarla en un registro. Docker Hub es un registro para imágenes de Docker que le permite hospedar sus propios repositorios, ya sean públicos o privados. 

Antes de poder insertar una imagen, debe iniciar sesión en Docker Hub mediante el comando [docker login](https://docs.docker.com/engine/reference/commandline/login/). Reemplace `<docker-id>` por el nombre de cuenta y escriba la contraseña en la consola en el símbolo del sistema. Para ver otras opciones de contraseña de Docker Hub, consulte la [documentación del comando docker login](https://docs.docker.com/engine/reference/commandline/login/).

```bash
docker login --username <docker-id> 
```

Un mensaje de inicio de sesión correcto confirmará que inició sesión. Cuando haya iniciado sesión, inserte la imagen en Docker Hub mediante el comando [docker push](https://docs.docker.com/engine/reference/commandline/push/).

```bash
docker push <docker-id>/mydockerimage:v1.0.0 .
```

Examine la salida del comando para comprobar que la inserción se realizó correctamente.

```bash
The push refers to a repository [docker.io/<docker-id>/mydockerimage:v1.0.0]
24d81eb139bf: Pushed
fd9e998161c9: Mounted from microsoft/azure-functions-runtime
e7796c35add2: Mounted from microsoft/azure-functions-runtime
ae9a05b85848: Mounted from microsoft/azure-functions-runtime
45c86e20670d: Mounted from microsoft/azure-functions-runtime
v1.0.0: digest: sha256:be080d80770df71234eb893fbe4d... size: 2422
```
Ahora, puede usar esta imagen como origen de implementación para una nueva aplicación de función en Azure. 

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Si decide instalar y usar la CLI de forma local, para este tema es preciso la CLI de Azure versión 2.0.21 o posterior. Ejecute `az --version` para encontrar la versión que tiene. Si necesita instalarla o actualizarla, consulte [Instalación de la CLI de Azure 2.0]( /cli/azure/install-azure-cli). 

[!INCLUDE [functions-create-resource-group](../../includes/functions-create-resource-group.md)]

[!INCLUDE [functions-create-storage-account](../../includes/functions-create-storage-account.md)]

## <a name="create-a-linux-app-service-plan"></a>Creación de un plan de App Service de Linux

Actualmente, los planes de consumo no admiten el hospedaje de Functions en Linux. Se debe ejecutar en un plan de App Service de Linux. Para más información sobre el hospedaje, consulte [Comparación de los planes de hospedaje de Azure Functions](functions-scale.md). 

[!INCLUDE [app-service-plan-no-h](../../includes/app-service-web-create-app-service-plan-linux-no-h.md)]


## <a name="create-and-deploy-the-custom-image"></a>Creación e implementación de la imagen personalizada

La aplicación de función hospeda la ejecución de las funciones. Cree una aplicación de función a partir de una imagen de Docker Hub mediante el comando [az functionapp create](/cli/azure/functionapp#create). 

En el siguiente comando, sustituya un nombre de aplicación de función único donde vea el marcador de posición `<app_name>` y el nombre de la cuenta de almacenamiento para `<storage_name>`. El `<app_name>` se usa como el dominio DNS predeterminado para la Function App y, por ello, el nombre debe ser único en todas las aplicaciones de Azure. Al igual que antes, `<docker-id>` es el nombre de cuenta de Docker.

```azurecli-interactive
az functionapp create --name <app_name> --storage-account  <storage_name>  --resource-group myResourceGroup \
--plan myAppServicePlan --deployment-container-image-name <docker-id>/mydockerimage:v1.0.0 
```
Después de que se haya creado la Function App, la CLI de Azure muestra información similar al siguiente ejemplo:

```json
{
  "availabilityState": "Normal",
  "clientAffinityEnabled": true,
  "clientCertEnabled": false,
  "containerSize": 1536,
  "dailyMemoryTimeQuota": 0,
  "defaultHostName": "quickstart.azurewebsites.net",
  "enabled": true,
  "enabledHostNames": [
    "quickstart.azurewebsites.net",
    "quickstart.scm.azurewebsites.net"
  ],
   ....
    // Remaining output has been truncated for readability.
}
```

El parámetro _deployment-container-image-name_ indica la imagen hospedada en Docker Hub que se usará para crear la aplicación de función. 


## <a name="configure-the-function-app"></a>Configuración de la Function App

La función necesita la cadena de conexión para conectarse a la cuenta de almacenamiento predeterminada. Al publicar la imagen personalizada en una cuenta de contenedor privada, esta configuración de aplicación se debe definir en su lugar como variables de entorno en el Dockerfile mediante la [instrucción ENV](https://docs.docker.com/engine/reference/builder/#env), o equivalente. 

En este caso, `<storage_account>` es el nombre de la cuenta de almacenamiento que ha creado. Obtenga la cadena de conexión con el comando [az storage account show-connection-string](/cli/azure/storage/account#show-connection-string). Agregue esta configuración de aplicación a la Function App con el comando [az functionapp config appsettings set](/cli/azure/functionapp/config/appsettings#set).

```azurecli-interactive
storageConnectionString=$(az storage account show-connection-string \
--resource-group myResourceGroup --name <storage_account> \
--query connectionString --output tsv)

az functionapp config appsettings set --name <function_app> \
--resource-group myResourceGroup \
--settings AzureWebJobsDashboard=$storageConnectionString \
AzureWebJobsStorage=$storageConnectionString
```

Ahora puede probar cómo se ejecutan las funciones en Linux en Azure.

[!INCLUDE [functions-test-function-code](../../includes/functions-test-function-code.md)]

[!INCLUDE [functions-cleanup-resources](../../includes/functions-cleanup-resources.md)]

## <a name="next-steps"></a>Pasos siguientes

En este tutorial, ha aprendido cómo:

> [!div class="checklist"]
> * Crear una imagen personalizada con Docker
> * Publicar una imagen personalizada en un registro de contenedor 
> * Cree una cuenta de Azure Storage. 
> * Crear un plan de App Service de Linux 
> * Implementar una aplicación de función desde Docker Hub
> * Agregar la configuración de aplicación a la aplicación de función

Aprenda más sobre el desarrollo de Azure Functions de forma local mediante las herramientas principales de Azure Functions.

> [!div class="nextstepaction"] 
> [Codificación y comprobación de Azure Functions en un entorno local](functions-run-local.md)
