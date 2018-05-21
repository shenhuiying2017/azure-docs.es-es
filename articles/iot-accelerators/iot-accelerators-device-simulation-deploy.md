---
title: 'Implementación de la solución Device Simulation: Azure | Microsoft Docs'
description: Este tutorial muestra cómo aprovisionar la solución Device Simulation desde azureiotsuite.com.
services: iot device simulation
suite: iot-suite
author: troyhopwood
manager: timlt
ms.author: troyhop
ms.service: iot-suite
ms.date: 12/18/2017
ms.topic: article
ms.devlang: NA
ms.tgt_pltfrm: NA
ms.workload: NA
ms.openlocfilehash: 4d468c527c658707da2b5f35f43676626baf5ca2
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/20/2018
---
# <a name="deploy-the-azure-iot-device-simulation-solution"></a>Implementación de la solución Azure IoT Device Simulation

Este tutorial muestra cómo aprovisionar una solución Device Simulation. Puede implementar la solución desde azureiotsuite.com.

En este tutorial, aprenderá a:

> [!div class="checklist"]
> * Configurar la solución Device Simulation
> * Implementar la solución Device Simulation
> * Iniciar sesión en la solución Device Simulation

## <a name="prerequisites"></a>requisitos previos

Para completar este tutorial, deberá tener una suscripción activa de Azure.

En caso de no tener ninguna, puede crear una cuenta de evaluación gratuita en tan solo unos minutos. Para obtener más información, consulte [Evaluación gratuita de Azure](http://azure.microsoft.com/pricing/free-trial/).

## <a name="deploy-the-solution"></a>Implementación de la solución

Antes de implementar la solución en la suscripción de Azure, debe elegir algunas opciones de configuración:

1. Inicie sesión en [azureiotsuite.com](https://www.azureiotsuite.com) con las credenciales de su cuenta de Azure y haga clic en **+** para crear una solución nueva:

    ![Crear una solución nueva](./media/iot-accelerators-device-simulation-deploy/createnewsolution.png)

1. Haga clic en **Seleccionar** en el icono de **Device Simulation**:

    ![Seleccionar Device Simulation](./media/iot-accelerators-device-simulation-deploy/select.png)

1. En la página de **creación de la solución Device Simulation**, escriba un **nombre de solución** para su solución Device Simulation.

1. Seleccione la **Suscripción** y **Región** que desea usar para aprovisionar la solución.

1. Especifique si desea implementar una nueva instancia de IoT Hub con la solución Device Simulation. Si activa esta casilla, se implementará una nueva instancia de IoT Hub en su suscripción. Independientemente de lo que elija, siempre puede señalar la simulación en cualquier instancia de IoT Hub.

1. Haga clic en **Crear solución** para comenzar el proceso de aprovisionamiento. Este proceso normalmente tarda varios minutos en ejecutarse:

    ![Información detallada sobre la solución Device Simulation](./media/iot-accelerators-device-simulation-deploy/createsolution.png)

## <a name="sign-in-to-the-solution"></a>Inicio de sesión en la solución

Una vez completado el proceso de aprovisionamiento, puede iniciar sesión en la solución Device Simulation.

1. En la página **Soluciones aprovisionadas**, haga clic en **Iniciar** en la nueva solución Device Simulation:

    ![Elegir la nueva solución](./media/iot-accelerators-device-simulation-deploy/newsolution.png)

1. Puede consultar información sobre la solución Device Simulation en el panel que aparece. Elija el **panel de la solución** para conectarse a la solución Device Simulation.

    > [!NOTE]
    > Puede eliminar la solución Device Simulation de este panel cuando termine de usarla.

    ![Panel de soluciones](./media/iot-accelerators-device-simulation-deploy/properties.png)

1. El panel de la solución de Device Simulation se muestra en el explorador.

## <a name="next-steps"></a>Pasos siguientes

En este tutorial aprendió lo siguiente:

> [!div class="checklist"]
> * Configuración de la solución
> * Implementación de la solución
> * Inicio de sesión en la solución

Ahora que ha implementado la solución Device Simulation, el paso siguiente es [explorar las funcionalidades de la solución Device Simulation](iot-accelerators-device-simulation-explore.md).

<!-- Next tutorials in the sequence -->
