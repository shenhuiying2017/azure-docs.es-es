---
title: 'Implementación local de la solución de supervisión remota: Azure | Microsoft Docs'
description: En este tutorial se muestra cómo implementar la solución preconfigurada de supervisión remota en la máquina local para pruebas y desarrollo.
services: iot-suite
suite: iot-suite
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-suite
ms.date: 03/07/2018
ms.topic: article
ms.devlang: NA
ms.tgt_pltfrm: NA
ms.workload: NA
ms.openlocfilehash: 77f40e2f10cbdb9930a22a4248e19bb3356af7af
ms.sourcegitcommit: a0be2dc237d30b7f79914e8adfb85299571374ec
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 03/12/2018
---
# <a name="deploy-the-remote-monitoring-preconfigured-solution-locally"></a>Implementación local de la solución preconfigurada de supervisión remota

En este artículo se muestra cómo implementar la solución preconfigurada de supervisión remota en la máquina local para pruebas y desarrollo. Este enfoque implementa los microservicios en un contenedor Docker local y utiliza IoT Hub, Cosmos DB y servicios de almacenamiento de Azure en la nube. Puede utilizar la CLI de soluciones preconfiguradas (PCS) para implementar los servicios en la nube de Azure.

## <a name="prerequisites"></a>requisitos previos

Para implementar los servicios de Azure utilizados por la solución preconfigurada de supervisión remota, necesita una suscripción de Azure activa.

En caso de no tener ninguna, puede crear una cuenta de evaluación gratuita en tan solo unos minutos. Para obtener más información, consulte [Evaluación gratuita de Azure](http://azure.microsoft.com/pricing/free-trial/).

Para completar la implementación local, necesita que las herramientas siguientes estén instaladas en la máquina de desarrollo local:

* [Git](https://git-scm.com/)
* [Docker](https://www.docker.com)
* [Docker Compose](https://docs.docker.com/compose/install/)
* [Node.js](https://nodejs.org/): este software es un requisito previo para la CLI de PCS.
* CLI DE PCS

> [!NOTE]
> Estas herramientas están disponibles en varias plataformas, como Windows, Linux e iOS.

### <a name="install-the-pcs-cli"></a>Instalación de la CLI de PCS

Para instalar la CLI, ejecute el siguiente comando en el entorno de la línea de comandos:

```cmd/sh
npm install iot-solutions -g
```

Para más información sobre la CLI, consulte [Uso de la CLI](https://github.com/Azure/pcs-cli/blob/master/README.md).

## <a name="deploy-the-azure-services"></a>Implementación de servicios de Azure

Aunque en este artículo se muestra cómo ejecutar los microservicios localmente, dependen de los tres servicios de Azure que se ejecutan en la nube. Puede implementar estos servicios de Azure de manera manual mediante Azure Portal, o utilizar la CLI de PCS. En este artículo se muestra cómo usar la herramienta `pcs`.

### <a name="sign-in-to-the-cli"></a>Inicio de sesión en la CLI

Para poder implementar la solución preconfigurada, debe iniciar sesión en su suscripción de Azure mediante la CLI de la manera siguiente:

```cmd/sh
pcs login
```

Siga las instrucciones que aparecen en pantalla para completar el proceso de inicio de sesión.

### <a name="run-a-local-deployment"></a>Ejecución de una implementación local

Utilice el comando siguiente para iniciar la implementación local:

```cmd/pcs
pcs -s local
```

El script le pedirá la siguiente información:

* Nombre de la solución.
* La suscripción de Azure que se va a usar.
* La ubicación del centro de datos de Azure que se va a usar.

El script crea una instancia de IoT Hub, una instancia de Cosmos DB y una cuenta de almacenamiento de Azure en un grupo de recursos de su suscripción de Azure. El nombre del grupo de recursos es el nombre de la solución que ha elegido al ejecutar la herramienta `pcs`.

El script puede tardar varios minutos en ejecutarse. Cuando se complete, verá un mensaje `Copy the following environment variables to /scripts/local/.env file:`. Copie las definiciones de variables de entorno que siguen al mensaje; las utilizará en un paso posterior.

## <a name="download-the-source-code"></a>Descarga del código fuente

El repositorio de código fuente de supervisión remota incluye los archivos de configuración de Docker que necesita para descargar, configurar y ejecutar las imágenes de Docker que contienen los microservicios. Para clonar el repositorio, vaya a la carpeta adecuada de la máquina local y ejecute uno de los siguientes comandos:

Para instalar las implementaciones de Java de los microservicios, ejecute:

```cmd/sh
git clone --recursive https://github.com/Azure/azure-iot-pcs-remote-monitoring-java
```

Para instalar las implementaciones de .NET de los microservicios, ejecute:

```cmd\sh
git clone --recursive https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet
```

Estos comandos descargan el código fuente de todos los microservicios. Aunque no necesita el código fuente para ejecutar los microservicios en Docker, el código fuente es útil si más tarde piensa modificar la solución preconfigurada y probar los cambios localmente.

## <a name="run-the-microservices-in-docker"></a>Ejecución de los microservicios en Docker

Para ejecutar los microservicios en Docker, modifique primero el archivo **scripts\\local\\.env** de la copia local del repositorio. Reemplace todo el contenido del archivo con las definiciones de las variables de entorno que anotó cuando ejecutó el comando `pcs` anteriormente. Estas variables de entorno permiten a los microservicios del contenedor de Docker conectarse a los servicios de Azure creados por la herramienta `pcs`.

Para ejecutar la solución preconfigurada, vaya a la carpeta **scripts\local** en el entorno de línea de comandos y ejecute el comando siguiente:

```cmd\sh
docker-compose up
```

La primera vez que ejecuta este comando, Docker descarga las imágenes de microservicios desde Docker Hub para crear los contenedores localmente. En las ejecuciones posteriores, Docker ejecuta los contenedores inmediatamente.

Puede utilizar un shell independiente para ver los registros desde el contenedor. En primer lugar, busque el identificador del contenedor mediante el comando `docker ps -a`. A continuación, utilice `docker logs {container-id} --tail 1000` para ver las últimas 1000 entradas de registro en el contenedor especificado.

Para acceder al panel de soluciones de supervisión remota, vaya a [http://localhost:8080](http://localhost:8080) en el explorador.

## <a name="tidy-up"></a>Limpieza

Para evitar cargos innecesarios, cuando haya terminado las pruebas, elimine los servicios en la nube de su suscripción de Azure. La forma más sencilla de quitar los servicios es utilizar Azure Portal para eliminar el grupo de recursos creado por la herramienta `pcs`.

Utilice el comando `docker-compose down --rmi all` para quitar las imágenes de Docker y liberar espacio en la máquina local. También puede eliminar la copia local del repositorio de supervisión remota que se creó al clonar el código fuente de GitHub.

## <a name="next-steps"></a>Pasos siguientes

En este tutorial aprendió lo siguiente:

> [!div class="checklist"]
> * Configurar la solución preconfigurada
> * Implementar la solución preconfigurada
> * Iniciar sesión en la solución preconfigurada

Ahora que ha implementado la solución de supervisión remota, el paso siguiente es [explorar las funcionalidades del panel de soluciones](./iot-suite-remote-monitoring-deploy.md).

<!-- Next tutorials in the sequence -->