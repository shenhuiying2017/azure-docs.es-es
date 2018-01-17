---
title: "Inicio rápido de Azure: Creación de una cuenta de Azure Automation | Microsoft Docs"
description: "Información sobre cómo crear una cuenta de Azure Automation y ejecutar un runbook"
services: automation
author: csand-msft
ms.author: csand
ms.date: 12/13/2017
ms.topic: quickstart
ms.service: automation
ms.custom: mvc
ms.openlocfilehash: 5522c56749f9c26f376661636dd62f88017f805e
ms.sourcegitcommit: 9292e15fc80cc9df3e62731bafdcb0bb98c256e1
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/10/2018
---
# <a name="create-an-azure-automation-account"></a>Creación de una cuenta de Azure Automation

Las cuentas de Azure Automation se pueden crear a través de Azure. Este método proporciona una interfaz de usuario basada en explorador para crear y configurar cuentas de Automation y recursos asociados. Esta guía de inicio rápido le guía en la creación de una cuenta de Automation y la ejecución de un runbook en la cuenta.

Si no tiene una suscripción a Azure, cree una [cuenta gratuita de Azure](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de empezar.

## <a name="log-in-to-azure"></a>Inicie sesión en Azure.

Inicie sesión en Azure en https://portal.azure.com.

## <a name="create-automation-account"></a>Creación de una cuenta de Automation

1. Haga clic en el botón **Nuevo** de la esquina superior izquierda de Azure.

1. Seleccione **Supervisión y administración** y, a continuación, **Automation**.

1. Escriba la información de la cuenta. En **Crear cuenta de ejecución de Azure**, elija **Sí** para que los artefactos destinados a simplificar la autenticación en Azure se habiliten automáticamente. Cuando haya terminado, haga clic en **Crear** para iniciar la implementación de la cuenta de Automation.

    ![Escriba la información sobre su cuenta de Automation en la página](./media/automation-quickstart-create-account/create-automation-account-portal-blade.png)  

1. La cuenta de Automation se ancla al panel de Azure. Cuando se completa la implementación, la información general de la cuenta de Automation se abre automáticamente.

    ![Información general de la cuenta de Automation](./media/automation-quickstart-create-account/automation-account-overview.png)

## <a name="run-a-runbook"></a>Ejecutar un runbook

Ejecute uno de los runbooks de tutorial.

1. Haga clic en **Runbooks** en **AUTOMATIZACIÓN DE PROCESOS**. Se muestra la lista de runbooks. De forma predeterminada, se habilitan varios runbooks de tutorial en la cuenta.

    ![Lista de runbooks de la cuenta de Automation](./media/automation-quickstart-create-account/automation-runbooks-overview.png)

1. Seleccione el runbook **AzureAutomationTutorialScript**. Esta acción abre la página de información general del runbook.

    ![Información general del runbook](./media/automation-quickstart-create-account/automation-tutorial-script-runbook-overview.png)

1. Haga clic en **Iniciar** y, en la página **Iniciar runbook**, haga clic en **Aceptar** para iniciar el runbook.

    ![Página del trabajo de runbook](./media/automation-quickstart-create-account/automation-tutorial-script-job.png)

1. Cuando **Estado del trabajo** cambie a **En ejecución**, haga clic en **Salida** o **Todos los registros** para ver la salida del trabajo de runbook. Para este runbook de tutorial, la salida es una lista de sus recursos de Azure.

## <a name="clean-up-resources"></a>Limpieza de recursos

Cuando ya no los necesite, elimine el grupo de recursos, la cuenta de Automation y todos los recursos relacionados. Para ello, seleccione el grupo de recursos de la cuenta de Automation y haga clic en **Eliminar**.

## <a name="next-steps"></a>pasos siguientes

En esta guía de inicio rápido, ha implementado una cuenta de Automation, ha iniciado un trabajo de runbook y ha visto los resultados del trabajo. Para obtener más información sobre Azure Automation, continúe en la guía de inicio rápido sobre la creación del primer runbook.

> [!div class="nextstepaction"]
> [Inicio rápido de Automation: creación de un runbook](./automation-quickstart-create-runbook.md)
