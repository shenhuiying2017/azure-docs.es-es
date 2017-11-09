---
title: "Implementación de la solución de supervisión remota: Azure | Microsoft Azure"
description: "Este tutorial muestra cómo aprovisionar la solución preconfigurada de supervisión remota desde azureiotsuite.com."
services: 
suite: iot-suite
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-suite
ms.date: 08/09/2017
ms.topic: article
ms.devlang: NA
ms.tgt_pltfrm: NA
ms.workload: NA
ms.openlocfilehash: babcf20b58af1415e0e658e0a622cb056e34642b
ms.sourcegitcommit: dfd49613fce4ce917e844d205c85359ff093bb9c
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/31/2017
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

1. Inicie sesión en [azureiotsuite.com](https://www.azureiotsuite.com) con las credenciales de su cuenta de Azure y haga clic en **+** para crear una solución.

1. Haga clic en **Seleccionar** en el icono **Supervisión remota**.

1. En la página **Create Remote Monitoring solution** (Crear solución de supervisión remota), escriba un **nombre de la solución** para la solución preconfigurada de supervisión remota.

1. Seleccione una implementación **Básica** o **Empresarial**. Si implementa la solución para saber cómo funciona o para ejecutar una demostración, elija la opción **Básica** para minimizar los costos.

1. Elija **Java** o **.NET** como lenguaje. Todos los microservicios están disponibles como implementaciones Java o .NET.

1. Revise el panel de **detalles de la solución** para más información sobre sus opciones de configuración.

1. Seleccione la **Suscripción** y **Región** que desea usar para aprovisionar la solución.

1. Haga clic en **Crear solución** para comenzar el proceso de aprovisionamiento. Este proceso normalmente tarda varios minutos en ejecutarse.

Para información sobre la solución de problemas, consulte la sección sobre [lo que se debe hacer cuando se produce un error en una implementación](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Developer-Troubleshooting-Guide#what-to-do-when-a-deployment-fails) del repositorio GitHub.

## <a name="sign-in-to-the-preconfigured-solution"></a>Iniciar sesión en la solución preconfigurada

Cuando el proceso de aprovisionamiento se completa, puede iniciar sesión en la solución preconfigurada de supervisión remota.

1. En la página **Soluciones aprovisionadas**, elija la solución de supervisión remota nueva.

1. Puede consultar información sobre la solución de supervisión remota en el panel que aparece. Elija el **panel de la solución** para conectarse a la solución de supervisión remota.

    > [!NOTE]
    > Puede eliminar la solución de supervisión remota de este panel cuando termine de usarla.

1. El panel de la solución de supervisión remota se muestra en el explorador.

## <a name="next-steps"></a>Pasos siguientes

En este tutorial, ha aprendido cómo:

> [!div class="checklist"]
> * Configurar la solución preconfigurada
> * Implementar la solución preconfigurada
> * Iniciar sesión en la solución preconfigurada

Ahora que ha implementado la solución de supervisión remota, el paso siguiente es [explorar las funcionalidades del panel de soluciones](./iot-suite-remote-monitoring-explore.md).

<!-- Next tutorials in the sequence -->