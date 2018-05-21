---
title: 'Implementación local de la solución de supervisión remota: Azure | Microsoft Docs'
description: En este tutorial se muestra cómo implementar el acelerador de la solución de supervisión remota en la máquina local para pruebas y desarrollo.
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
ms.openlocfilehash: a11df1dc17b4dcbacece85526eeac39502cbbe34
ms.sourcegitcommit: 688a394c4901590bbcf5351f9afdf9e8f0c89505
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/18/2018
---
# <a name="deploy-the-remote-monitoring-solution-accelerator-locally"></a>Implementación local del acelerador de la solución de supervisión remota

En este artículo se explica cómo implementar el acelerador de la solución de supervisión remota en la máquina local para pruebas y desarrollo. Este enfoque implementa los microservicios en un contenedor Docker local y utiliza IoT Hub, Cosmos DB y servicios de almacenamiento de Azure en la nube. Puede utilizar la CLI de aceleradores de soluciones (PCS) para implementar los servicios en la nube de Azure.

## <a name="prerequisites"></a>requisitos previos

Para implementar los servicios de Azure utilizados por el acelerador de la solución de supervisión remota, necesita una suscripción de Azure activa.

En caso de no tener ninguna, puede crear una cuenta de evaluación gratuita en tan solo unos minutos. Para obtener más información, consulte [Evaluación gratuita de Azure](http://azure.microsoft.com/pricing/free-trial/).

Para completar la implementación local, necesita que las herramientas siguientes estén instaladas en la máquina de desarrollo local:

* [Git](https://git-scm.com/)
* [Docker](https://www.docker.com)
* [Docker Compose](https://docs.docker.com/compose/install/)
* [Node.js](https://nodejs.org/): este software es un requisito previo para la CLI de PCS.
* CLI DE PCS
* Repositorio local del código fuente

> [!NOTE]
> Estas herramientas están disponibles en varias plataformas, como Windows, Linux e iOS.

### <a name="install-the-pcs-cli"></a>Instalación de la CLI de PCS

Para instalar la CLI de PCS a través de npm, ejecute el siguiente comando en el entorno de la línea de comandos:

```cmd/sh
npm install iot-solutions -g
```

Para más información sobre la CLI, consulte [Uso de la CLI](https://github.com/Azure/pcs-cli/blob/master/README.md).

### <a name="download-the-source-code"></a>Descarga del código fuente

 El repositorio de código fuente de supervisión remota incluye los archivos de configuración de Docker que necesita para descargar, configurar y ejecutar las imágenes de Docker que contienen los microservicios. Para clonar y crear una versión local del repositorio, vaya a la carpeta adecuada del equipo local mediante la línea de comandos favorita o el terminal y ejecute alguno de los siguientes comandos:

Para instalar las implementaciones de Java de los microservicios, ejecute:

```cmd/sh
git clone --recursive https://github.com/Azure/azure-iot-pcs-remote-monitoring-java
```

Para instalar las implementaciones de .NET de los microservicios, ejecute:

```cmd\sh
git clone --recursive https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet
```

Repositorio y submódulos de la solución preconfigurada de supervisión remota [[Java](https://github.com/Azure/azure-iot-pcs-remote-monitoring-java) | [.Net](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet)]

> [!NOTE]
> Estos comandos descargan el código fuente de todos los microservicios. Aunque no necesita el código fuente para ejecutar los microservicios en Docker, el código fuente es útil si más tarde piensa modificar la solución preconfigurada y probar los cambios localmente.

## <a name="deploy-the-azure-services"></a>Implementación de servicios de Azure

Aunque en este artículo se muestra cómo ejecutar los microservicios localmente, dependen de los tres servicios de Azure que se ejecutan en la nube. Puede implementar estos servicios de Azure de manera [manual mediante Azure Portal](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Manual-steps-to-create-azure-resources-for-local-setup), o utilizar la CLI de PCS. En este artículo se muestra cómo usar la herramienta `pcs`.

### <a name="sign-in-to-the-cli"></a>Inicio de sesión en la CLI

Para poder implementar el acelerador de la solución, debe iniciar sesión en su suscripción de Azure mediante la CLI de la manera siguiente:

```cmd/sh
pcs login
```

Siga las instrucciones que aparecen en pantalla para completar el proceso de inicio de sesión. Asegúrese de no hacer clic en ningún lugar dentro de la CLI o, de lo contrario, el inicio de sesión podría dar errores. Si ha completado el inicio de sesión, verá un mensaje de inicio de sesión correcto en la CLI. 

### <a name="run-a-local-deployment"></a>Ejecución de una implementación local

Utilice el comando siguiente para iniciar la implementación local. Así creará los recursos de Azure necesarios e imprimirá las variables de entorno en la consola. 

```cmd/pcs
pcs -s local
```

El script le pedirá la siguiente información:

* Nombre de la solución.
* La suscripción de Azure que se va a usar.
* La ubicación del centro de datos de Azure que se va a usar.

> [!NOTE]
> El script crea una instancia de IoT Hub, una instancia de Cosmos DB y una cuenta de almacenamiento de Azure en un grupo de recursos de su suscripción de Azure. El nombre del grupo de recursos es el nombre de la solución que ha elegido al ejecutar la herramienta `pcs` anterior. 

> [!IMPORTANT]
> El script puede tardar varios minutos en ejecutarse. Cuando se complete, verá un mensaje `Copy the following environment variables to /scripts/local/.env file:`. Copie las definiciones de variables de entorno que siguen al mensaje; las utilizará en un paso posterior.

## <a name="run-the-microservices-in-docker"></a>Ejecución de los microservicios en Docker

Para ejecutar los microservicios en Docker, modifique primero el archivo **scripts\\local\\.env** de la copia local del repositorio clonado en un paso anterior. Reemplace todo el contenido del archivo con las definiciones de las variables de entorno que anotó cuando ejecutó el comando `pcs` en el último paso. Estas variables de entorno permiten a los microservicios del contenedor de Docker conectarse a los servicios de Azure creados por la herramienta `pcs`.

Para ejecutar el acelerador de la solución, vaya a la carpeta **scripts\local** en el entorno de línea de comandos y ejecute el comando siguiente:

```cmd\sh
docker-compose up
```

La primera vez que ejecuta este comando, Docker descarga las imágenes de microservicios desde Docker Hub para crear los contenedores localmente. En las ejecuciones posteriores, Docker ejecuta los contenedores inmediatamente.

Puede utilizar un shell independiente para ver los registros desde el contenedor. En primer lugar, busque el identificador del contenedor mediante el comando `docker ps -a`. A continuación, utilice `docker logs {container-id} --tail 1000` para ver las últimas 1000 entradas de registro en el contenedor especificado.

Para acceder al panel de la solución de supervisión remota, vaya a [http://localhost:8080](http://localhost:8080) en el explorador.

## <a name="clean-up"></a>Limpieza

Para evitar cargos innecesarios, cuando haya terminado las pruebas, elimine los servicios en la nube de su suscripción de Azure. La forma más sencilla de quitar los servicios es ir a [Azure Portal](https://ms.portal.azure.com) y eliminar el grupo de recursos creado con la herramienta `pcs`.

Utilice el comando `docker-compose down --rmi all` para quitar las imágenes de Docker y liberar espacio en la máquina local. También puede eliminar la copia local del repositorio de supervisión remota que se creó al clonar el código fuente de GitHub.

## <a name="next-steps"></a>Pasos siguientes

En este tutorial aprendió lo siguiente:

> [!div class="checklist"]
> * Configuración del entorno de desarrollo local
> * Configuración del acelerador de la solución
> * Implementación del acelerador de solución
> * Inicio de sesión en el acelerador de la solución

Ahora que ha implementado la solución de supervisión remota, el paso siguiente es [explorar las funcionalidades del panel de soluciones](iot-accelerators-remote-monitoring-deploy.md).

<!-- Next tutorials in the sequence -->