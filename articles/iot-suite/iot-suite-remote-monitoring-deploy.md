---
title: "Implementación de la solución de supervisión remota: Azure | Microsoft Azure"
description: "Este tutorial muestra cómo aprovisionar la solución preconfigurada de supervisión remota desde azureiotsuite.com."
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
ms.openlocfilehash: 768c32e30509c74a292b2355a249ec010af2cd1b
ms.sourcegitcommit: 922687d91838b77c038c68b415ab87d94729555e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/13/2017
---
# <a name="deploy-the-remote-monitoring-preconfigured-solution"></a>Implementación de la solución preconfigurada de supervisión remota

Este tutorial muestra cómo aprovisionar la solución preconfigurada de supervisión remota. Puede implementar la solución desde azureiotsuite.com. También puede implementar la solución con la CLI. Para información sobre esta opción, consulte el artículo sobre la [implementación de una solución preconfigurada desde la línea de comandos](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Developer-Reference-Guide#deploy-a-pcs-from-the-command-line).

En este tutorial, aprenderá a:

> [!div class="checklist"]
> * Configurar la solución preconfigurada
> * Implementar la solución preconfigurada
> * Iniciar sesión en la solución preconfigurada

## <a name="prerequisites"></a>Requisitos previos

Para completar este tutorial, deberá tener una suscripción activa de Azure.

En caso de no tener ninguna, puede crear una cuenta de evaluación gratuita en tan solo unos minutos. Para obtener más información, consulte [Evaluación gratuita de Azure](http://azure.microsoft.com/pricing/free-trial/).

## <a name="deploy-the-preconfigured-solution"></a>Implementar la solución preconfigurada

Antes de implementar la solución preconfigurada en la suscripción de Azure, debe elegir algunas opciones de configuración:

1. Inicie sesión en [azureiotsuite.com](https://www.azureiotsuite.com) con las credenciales de su cuenta de Azure y haga clic en **+** para crear una solución nueva:

    ![Crear una solución nueva](media/iot-suite-remote-monitoring-deploy/createnewsolution.png)

1. Haga clic en **Seleccionar** en el icono **Vista previa de la supervisión remota**.

    ![Elegir una supervisión remota](media/iot-suite-remote-monitoring-deploy/remotemonitoring.png)

1. En la página **Create Remote Monitoring solution** (Crear solución de supervisión remota), escriba un **nombre de la solución** para la solución preconfigurada de supervisión remota.

1. Seleccione una implementación **Básica** o **Estándar**. Si implementa la solución para saber cómo funciona o para ejecutar una demostración, elija la opción **Básica** para minimizar los costos.

1. Elija **Java** o **.NET** como lenguaje. Todos los microservicios están disponibles como implementaciones Java o .NET.

1. Revise el panel de **detalles de la solución** para más información sobre sus opciones de configuración.

1. Seleccione la **Suscripción** y **Región** que desea usar para aprovisionar la solución.

1. Haga clic en **Crear solución** para comenzar el proceso de aprovisionamiento. Este proceso normalmente tarda varios minutos en ejecutarse:

    ![Detalles de la solución de supervisión remota](media/iot-suite-remote-monitoring-deploy/createform.png)

Para información sobre la solución de problemas, consulte la sección sobre [lo que se debe hacer cuando se produce un error en una implementación](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Developer-Troubleshooting-Guide#what-to-do-when-a-deployment-fails) del repositorio GitHub.

## <a name="sign-in-to-the-preconfigured-solution"></a>Iniciar sesión en la solución preconfigurada

Cuando el proceso de aprovisionamiento se completa, puede iniciar sesión en la solución preconfigurada de supervisión remota.

1. En la página **Soluciones aprovisionadas**, elija la solución de supervisión remota nueva:

    ![Elegir la nueva solución](media/iot-suite-remote-monitoring-deploy/choosenew.png)

1. Puede consultar información sobre la solución de supervisión remota en el panel que aparece. Elija el **panel de la solución** para conectarse a la solución de supervisión remota.

    > [!NOTE]
    > Puede eliminar la solución de supervisión remota de este panel cuando termine de usarla.

    ![Panel de soluciones](media/iot-suite-remote-monitoring-deploy/solutionpanel.png)

1. El panel de la solución de supervisión remota se muestra en el explorador.

## <a name="next-steps"></a>Pasos siguientes

En este tutorial, ha aprendido cómo:

> [!div class="checklist"]
> * Configurar la solución preconfigurada
> * Implementar la solución preconfigurada
> * Iniciar sesión en la solución preconfigurada

Ahora que ha implementado la solución de supervisión remota, el paso siguiente es [explorar las funcionalidades del panel de soluciones](./iot-suite-remote-monitoring-explore.md).

<!-- Next tutorials in the sequence -->