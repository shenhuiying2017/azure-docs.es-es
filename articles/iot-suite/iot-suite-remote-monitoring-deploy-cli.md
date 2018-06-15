---
title: Implementación de la solución de supervisión remota de Java en Azure | Microsoft Docs
description: En este tutorial se muestra cómo aprovisionar el acelerador de la solución de supervisión remota mediante la CLI.
services: iot-suite
suite: iot-suite
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-suite
ms.date: 01/29/2018
ms.topic: article
ms.devlang: NA
ms.tgt_pltfrm: NA
ms.workload: NA
ms.openlocfilehash: f158af573475964eca1ff168ecf3eadc58c2a394
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/07/2018
ms.locfileid: "33774234"
---
# <a name="deploy-the-remote-monitoring-solution-accelerator-using-the-cli"></a>Implementación del acelerador de la solución de supervisión remota mediante la CLI

En este tutorial se muestra cómo aprovisionar el acelerador de la solución de supervisión remota. La solución se implementa mediante la CLI. También puede implementar la solución con la interfaz de usuario basada en web de azureiotsuite.com. Para más información sobre esta opción, consulte [Implementación del acelerador de la solución de supervisión remota](iot-suite-remote-monitoring-deploy.md).

## <a name="prerequisites"></a>requisitos previos

Para implementar el acelerador de la solución de supervisión remota, necesita una suscripción de Azure activa.

En caso de no tener ninguna, puede crear una cuenta de evaluación gratuita en tan solo unos minutos. Para obtener más información, consulte [Evaluación gratuita de Azure](http://azure.microsoft.com/pricing/free-trial/).

Para ejecutar la CLI, necesita tener instalado [Node.js](https://nodejs.org/) en su máquina local.

## <a name="install-the-cli"></a>Instalación de la CLI de Azure

Para instalar la CLI, ejecute el siguiente comando en el entorno de la línea de comandos:

```cmd/sh
npm install iot-solutions -g
```

## <a name="sign-in-to-the-cli"></a>Inicio de sesión en la CLI

Para poder implementar el acelerador de la solución, debe iniciar sesión en su suscripción de Azure mediante la CLI de la manera siguiente:

```cmd/sh
pcs login
```

Siga las instrucciones que aparecen en pantalla para completar el proceso de inicio de sesión.

## <a name="deployment-options"></a>Opciones de implementación

Al implementar el acelerador de la solución, hay varias opciones que permiten configurar el proceso de implementación:

| Opción | Valores | DESCRIPCIÓN |
| ------ | ------ | ----------- |
| SKU    | `basic`, `standard`, `local` | Una implementación _básica_ está destinada a pruebas y demostraciones. En este tipo de implementación, todos los microservicios se implementan en una única máquina virtual. Una implementación _estándar_ está destinada a producción. En este tipo de implementación, los microservicios se implementan en varias máquinas virtuales. Una implementación _local_ configura un contenedor de Docker para que ejecute los microservicios en la máquina local y usa los servicios de Azure, como Storage y Cosmos DB, en la nube. |
| Tiempo de ejecución | `dotnet`, `java` | Selecciona la implementación del lenguaje de los microservicios. |

Para información sobre cómo usar la implementación local, consulte el artículo sobre la [ejecución local de la solución de supervisión remota](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Running-the-Remote-Monitoring-Solution-Locally#deploy-azure-services-and-set-environment-variables).

## <a name="basic-vs-standard-deployments"></a>Implementación básica frente a estándar

### <a name="basic"></a>Básica
La implementación básica está orientada a presentar la solución. Para reducir el costo de esta demostración, todos los microservicios se implementan en una única máquina virtual; esto no se considera una arquitectura lista para entornos de producción.

La opción de implementación estándar debe usarse cuando esté listo para personalizar una arquitectura para entornos de producción, creada para fines de escalabilidad y extensibilidad.

Al crear una solución básica, se aprovisionarán los servicios de Azure siguientes en su suscripción de Azure al costo de: 

| Recuento | Recurso                       | Escriba         | Se usa para |
|-------|--------------------------------|--------------|----------|
| 1     | [Máquina virtual con Linux](https://azure.microsoft.com/services/virtual-machines/) | Estándar D1 v2  | Hospedaje de microservicios |
| 1     | [Azure IoT Hub](https://azure.microsoft.com/services/iot-hub/)                  | S1: nivel básico | Comunicación y administración de dispositivos |
| 1     | [Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/)              | Estándar        | Almacenar datos de configuración y telemetría de dispositivos como reglas, alarmas y mensajes |  
| 1     | [Cuenta de Azure Storage](https://docs.microsoft.com/azure/storage/common/storage-introduction#types-of-storage-accounts)  | Estándar        | Almacenar puntos de comprobación de máquina virtual y streaming |
| 1     | [Aplicación web](https://azure.microsoft.com/services/app-service/web/)        |                 | Hospedaje de aplicación web front-end |

### <a name="standard"></a>Estándar
La implementación estándar es una implementación lista para producción que un desarrollador puede personalizar y ampliar para satisfacer sus necesidades. En cuanto a la confiabilidad y el escalado, los microservicios de aplicación se crean como contenedores de Docker y se implementan con un orquestador ([Kubernetes](https://kubernetes.io/), de forma predeterminada). El orquestador es responsable de la implementación, el escalado y la administración de la aplicación.

Al crear una solución estándar, se aprovisionarán los servicios de Azure siguientes en su suscripción de Azure al costo de:

| Recuento | Recurso                                     | SKU / Tamaño      | Se usa para |
|-------|----------------------------------------------|-----------------|----------|
| 4     | [Máquinas virtuales Linux](https://azure.microsoft.com/services/virtual-machines/)   | Estándar D2 V2  | 1 maestro y 3 agentes para hospedar microservicios con redundancia |
| 1     | [Azure Container Service](https://azure.microsoft.com/services/container-service/) |                 | Orquestador de [Kubernetes](https://kubernetes.io) |
| 1     | [Azure IoT Hub][https://azure.microsoft.com/services/iot-hub/]                     | S1: nivel básico | Comando, control y administración de dispositivos |
| 1     | [Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/)                 | Estándar        | Almacenar datos de configuración y telemetría de dispositivos como reglas, alarmas y mensajes |
| 5     | [Cuentas de Azure Storage](https://docs.microsoft.com/azure/storage/common/storage-introduction#types-of-storage-accounts)    | Estándar        | 4 para el almacenamiento de máquina virtual y 1 para los puntos de comprobación de streaming |
| 1     | [App Service](https://azure.microsoft.com/services/app-service/web/)             | S1 Estándar     | Application Gateway frente a SSL |

> Puede encontrar más información sobre los precios de estos servicios [aquí](https://azure.microsoft.com/pricing). Las cantidades de uso y los detalles de facturación de la suscripción se pueden encontrar en [Azure Portal](https://portal.azure.com/).

## <a name="deploy-the-solution-accelerator"></a>Implementación del acelerador de solución

### <a name="example-deploy-net-version"></a>Ejemplo: Implementación de la versión de .NET

En el ejemplo siguiente se muestra cómo implementar la versión de .NET básica del acelerador de la solución de supervisión remota:

```cmd/sh
pcs -t remotemonitoring -s basic -r dotnet
```

### <a name="example-deploy-java-version"></a>Ejemplo: Implementación de la versión de Java

En el ejemplo siguiente se muestra cómo implementar la versión de Java estándar del acelerador de la solución de supervisión remota:

```cmd/sh
pcs -t remotemonitoring -s standard -r java
```

### <a name="pcs-command-options"></a>Opciones del comando pcs

Al ejecutar el comando `pcs` para implementar una solución, se le pide:

- Un nombre para la solución. Este nombre debe ser único.
- La suscripción de Azure que se va a usar.
- Una ubicación.
- Las credenciales de las máquinas virtuales que hospedan los microservicios. Puede usar estas credenciales para tener acceso a las máquinas virtuales de cara a la solución de problemas.

Cuando el comando `pcs` finaliza, muestra la dirección URL de la nueva implementación del acelerador de la solución. El comando `pcs` también crea un archivo `{deployment-name}-output.json` con información adicional, como el nombre de la instancia de IoT Hub que se aprovisionó para usted.

Para más información sobre los parámetros de la línea de comandos, ejecute:

```cmd/sh
pcs -h
```

Para más información sobre la CLI, consulte [Uso de la CLI](https://github.com/Azure/pcs-cli/blob/master/README.md).

## <a name="next-steps"></a>Pasos siguientes

En este tutorial aprendió lo siguiente:

> [!div class="checklist"]
> * Configuración del acelerador de la solución
> * Implementación del acelerador de solución
> * Inicio de sesión en el acelerador de la solución

Ahora que ha implementado la solución de supervisión remota, el paso siguiente es [explorar las funcionalidades del panel de soluciones](./iot-suite-remote-monitoring-deploy.md).

<!-- Next tutorials in the sequence -->