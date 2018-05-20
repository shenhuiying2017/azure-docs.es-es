---
title: 'Implementación de la solución de supervisión remota: Azure | Microsoft Azure'
description: En este tutorial se explica cómo aprovisionar el acelerador de la solución de supervisión remota desde azureiotsuite.com.
services: iot-suite
suite: iot-suite
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-suite
ms.date: 05/01/2018
ms.topic: article
ms.devlang: NA
ms.tgt_pltfrm: NA
ms.workload: NA
ms.openlocfilehash: eb78ce91755c43f1c6fedf62a70238df911b940f
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/07/2018
---
# <a name="deploy-the-remote-monitoring-solution-accelerator"></a>Implementación del acelerador de la solución de supervisión remota

En este tutorial se muestra cómo aprovisionar el acelerador de la solución de supervisión remota. Puede implementar la solución desde azureiotsuite.com. También puede implementar la solución con la CLI. Para información sobre esta opción, consulte el artículo sobre la [implementación del acelerador de una solución desde la línea de comandos](iot-suite-remote-monitoring-deploy-cli.md).

En este tutorial, aprenderá a:

> [!div class="checklist"]
> * Configuración del acelerador de la solución
> * Implementación del acelerador de solución
> * Inicio de sesión en el acelerador de la solución

## <a name="prerequisites"></a>requisitos previos

Para completar este tutorial, deberá tener una suscripción activa de Azure.

En caso de no tener ninguna, puede crear una cuenta de evaluación gratuita en tan solo unos minutos. Para obtener más información, consulte [Evaluación gratuita de Azure](http://azure.microsoft.com/pricing/free-trial/).

## <a name="deploy-the-solution-accelerator"></a>Implementación del acelerador de solución

Antes de implementar el acelerador de la solución en la suscripción de Azure, debe elegir algunas opciones de configuración:

1. Inicie sesión en [azureiotsolutions.com](https://www.azureiotsolutions.com/Accelerators) con las credenciales de la cuenta de Azure.

1. Haga clic en **Intentar ahora** en el icono **Supervisión remota**.

    ![Elegir una supervisión remota](media/iot-suite-remote-monitoring-deploy/remotemonitoring.png)

1. En la página **Creación de una solución de supervisión remota**, escriba un **nombre de solución** para el acelerador de la solución de supervisión remota.

1. Seleccione una implementación **Básica** o **Estándar**. Si implementa la solución para saber cómo funciona o para ejecutar una demostración, elija la opción **Básica** para minimizar los costos.

1. Elija **Java** o **.NET** como lenguaje. Todos los microservicios están disponibles como implementaciones Java o .NET.

1. Revise el panel de **detalles de la solución** para más información sobre sus opciones de configuración.

1. Seleccione la **Suscripción** y **Región** que desea usar para aprovisionar la solución.

1. Haga clic en **Crear solución** para comenzar el proceso de aprovisionamiento. Este proceso normalmente tarda varios minutos en ejecutarse:

    ![Detalles de la solución de supervisión remota](media/iot-suite-remote-monitoring-deploy/createform.png)

Para información sobre la solución de problemas, consulte la sección sobre [lo que se debe hacer cuando se produce un error en una implementación](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Developer-Troubleshooting-Guide#what-to-do-when-a-deployment-fails) del repositorio GitHub.

## <a name="sign-in-to-the-solution-accelerator"></a>Inicio de sesión en el acelerador de la solución

Cuando el proceso de aprovisionamiento se completa, puede iniciar sesión en el acelerador de la solución de supervisión remota.

1. En la página **Soluciones aprovisionadas**, elija la solución de supervisión remota nueva:

    ![Elegir la nueva solución](media/iot-suite-remote-monitoring-deploy/choosenew.png)

1. Puede consultar información sobre la solución de supervisión remota en el panel que aparece. Elija el **panel de la solución** para conectarse a la solución de supervisión remota.

    > [!NOTE]
    > Puede eliminar la solución de supervisión remota de este panel cuando termine de usarla.

    ![Panel de soluciones](media/iot-suite-remote-monitoring-deploy/solutionpanel.png)

1. El panel de la solución de supervisión remota se muestra en el explorador.

## <a name="next-steps"></a>Pasos siguientes

En este tutorial aprendió lo siguiente:

> [!div class="checklist"]
> * Configuración del acelerador de la solución
> * Implementación del acelerador de solución
> * Inicio de sesión en el acelerador de la solución

Ahora que ha implementado la solución de supervisión remota, el paso siguiente es [explorar las funcionalidades del panel de soluciones](./iot-suite-remote-monitoring-explore.md).

<!-- Next tutorials in the sequence -->