---
title: "Implementación de la solución de supervisión remota de Java en Azure | Microsoft Docs"
description: "En este tutorial se muestra como aprovisionar los microservicios de Java de la solución preconfigurada de supervisión remota mediante la CLI."
services: 
suite: iot-suite
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-suite
ms.date: 12/12/2017
ms.topic: article
ms.devlang: NA
ms.tgt_pltfrm: NA
ms.workload: NA
ms.openlocfilehash: ea3764299d07f548abbc2857a3adbfb4dc50dec8
ms.sourcegitcommit: 922687d91838b77c038c68b415ab87d94729555e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/13/2017
---
# <a name="deploy-the-remote-monitoring-preconfigured-solution-using-the-cli"></a>Implementación de la solución preconfigurada de supervisión remota mediante la CLI

Este tutorial muestra cómo aprovisionar la solución preconfigurada de supervisión remota. La solución se implementa mediante la CLI. También puede implementar la solución con la interfaz de usuario basada en web de azureiotsuite.com. Para más información sobre esta opción, consulte [Implementación de la solución preconfigurada de supervisión remota](iot-suite-remote-monitoring-deploy.md).

## <a name="prerequisites"></a>Requisitos previos

Para implementar la solución preconfigurada de supervisión remota, necesita una suscripción de Azure activa.

En caso de no tener ninguna, puede crear una cuenta de evaluación gratuita en tan solo unos minutos. Para obtener más información, consulte [Evaluación gratuita de Azure](http://azure.microsoft.com/pricing/free-trial/).

Para ejecutar la CLI, necesita tener instalado [Node.js](https://nodejs.org/) en su máquina local.

## <a name="install-the-cli"></a>Instalación de la CLI de Azure

Para instalar la CLI, ejecute el siguiente comando en el entorno de la línea de comandos:

```cmd/sh
npm install iot-solutions -g
```

## <a name="sign-in-to-the-cli"></a>Inicio de sesión en la CLI

Para poder implementar la solución preconfigurada, debe iniciar sesión en su suscripción de Azure mediante la CLI de la manera siguiente:

```cmd/sh
pcs login
```

Siga las instrucciones que aparecen en pantalla para completar el proceso de inicio de sesión.

## <a name="deployment-options"></a>Opciones de implementación

Al implementar la solución preconfigurada, hay varias opciones que permiten configurar el proceso de implementación:

| Opción | Valores | Descripción |
| ------ | ------ | ----------- |
| SKU    | `basic`, `standard` | Una implementación _básica_ está destinada a pruebas y demostraciones. En este tipo de implementación, todos los microservicios se implementan en una única máquina virtual. Una implementación _estándar_ está destinada a producción. En este tipo de implementación, los microservicios se implementan en varias máquinas virtuales. |
| Tiempo de ejecución | `dotnet`, `java` | Selecciona la implementación del lenguaje de los microservicios. |

## <a name="deploy-the-preconfigured-solution"></a>Implementación de la solución preconfigurada

### <a name="example-deploy-net-version"></a>Ejemplo: Implementación de la versión de .NET

En el ejemplo siguiente se muestra cómo implementar la versión de .NET básica de la solución preconfigurada de supervisión remota:

```cmd/sh
pcs -t remotemonitoring -s basic -r dotnet
```

### <a name="example-deploy-java-version"></a>Ejemplo: Implementación de la versión de Java

En el ejemplo siguiente se muestra cómo implementar la versión de Java estándar de la solución preconfigurada de supervisión remota:

```cmd/sh
pcs -t remotemonitoring -s standard -r java
```

### <a name="pcs-command-options"></a>Opciones del comando pcs

Al ejecutar el comando `pcs` para implementar una solución, se le pide:

- Un nombre para la solución. Este nombre debe ser único.
- La suscripción de Azure que se va a usar.
- Una ubicación.
- Las credenciales de las máquinas virtuales que hospedan los microservicios. Puede usar estas credenciales para tener acceso a las máquinas virtuales de cara a la solución de problemas.

Cuando el comando `pcs` finaliza, muestra la dirección URL de la nueva implementación de la solución preconfigurada. El comando `pcs` también crea un archivo `{deployment-name}-output.json` con información adicional, como el nombre de la instancia de IoT Hub que se aprovisionó para usted.

Para más información sobre los parámetros de la línea de comandos, ejecute:

```cmd/sh
pcs -h
```

Para más información sobre la CLI, consulte [Uso de la CLI](https://github.com/Azure/pcs-cli/blob/master/README.md).

## <a name="next-steps"></a>Pasos siguientes

En este tutorial, ha aprendido cómo:

> [!div class="checklist"]
> * Configurar la solución preconfigurada
> * Implementar la solución preconfigurada
> * Iniciar sesión en la solución preconfigurada

Ahora que ha implementado la solución de supervisión remota, el paso siguiente es [explorar las funcionalidades del panel de soluciones](./iot-suite-remote-monitoring-deploy.md).

<!-- Next tutorials in the sequence -->