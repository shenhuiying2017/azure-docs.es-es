---
title: "Validación de la configuración de la cuenta de Azure Automation | Microsoft Docs"
description: "En este artículo se describe cómo confirmar que la configuración de la cuenta de Automation es correcta."
services: automation
documentationcenter: 
author: mgoedtel
manager: carmonm
editor: 
ms.assetid: 
ms.service: automation
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 04/14/2017
ms.author: magoedte
translationtype: Human Translation
ms.sourcegitcommit: e851a3e1b0598345dc8bfdd4341eb1dfb9f6fb5d
ms.openlocfilehash: 5bed2616e15a2e5f52e79d0c28159b568e7a1de3
ms.lasthandoff: 04/15/2017

---

# <a name="test-azure-automation-run-as-account-authentication"></a>Comprobación de la autenticación con la cuenta de ejecución de Azure Automation
Una vez que se haya creado correctamente una cuenta de Automation, puede realizar una prueba sencilla para confirmar que puede autenticarse correctamente en Azure Resource Manager o en el modelo de implementación clásica de Azure mediante la cuenta de ejecución de Automation recién creada o actualizada.    

## <a name="automation-run-as-authentication"></a>Autenticación con la cuenta de ejecución de Automation

1. En Azure Portal, abra la cuenta de Automation que creó anteriormente.  
2. Haga clic en el icono **Runbooks** para abrir la lista de runbooks.
3. Seleccione el runbook **AzureAutomationTutorialScript** y haga clic en **Iniciar** para abrirlo.  Recibirá un aviso para comprobar que desea que se inicie el Runbook.
4. Se crea un [trabajo de Runbook](automation-runbook-execution.md) , se muestra la hoja Trabajo y el estado del trabajo aparece en el icono **Resumen del trabajo** .  
5. El estado del trabajo se iniciará como *En cola* , lo que indica que está esperando a que haya algún trabajo de Runbook disponible en la nube. Su estado cambiará a *Iniciando* cuando un trabajo de runbook lo solicite. Cuando el runbook comience a ejecutarse realmente, el estado será *En ejecución*.  
6. Cuando el trabajo de runbook se complete, debería aparecer el estado **Completado**.<br> ![Prueba de Runbook de entidad de seguridad](media/automation-verify-runas-authentication/job-summary-automationtutorialscript.png)<br>
7. Para ver los resultados detallados del Runbook, haga clic en el icono **Salida** .
8. En la hoja **Salida** , debería ver que se ha autenticado correctamente y ha devuelto una lista de todos los recursos disponibles en el grupo de recursos.
9. Cierre la hoja **Salida** para volver a la hoja **Resumen del trabajo**.
10. Cierre la hoja **Resumen del trabajo** y la hoja del runbook **AzureAutomationTutorialScript**.

## <a name="classic-run-as-authentication"></a>Autenticación con la cuenta de ejecución del modelo de implementación clásica de Azure
Siga estos pasos si va a administrar recursos en el modelo de implementación clásica para confirmar que se puede autenticar correctamente con la nueva cuenta de ejecución clásica.     

1. En Azure Portal, abra la cuenta de Automation que creó anteriormente.  
2. Haga clic en el icono **Runbooks** para abrir la lista de runbooks.
3. Seleccione el runbook **AzureClassicAutomationTutorialScript** y haga clic en **Iniciar** para abrirlo.  Recibirá un aviso para comprobar que desea que se inicie el Runbook.
4. Se crea un [trabajo de Runbook](automation-runbook-execution.md) , se muestra la hoja Trabajo y el estado del trabajo aparece en el icono **Resumen del trabajo** .  
5. El estado del trabajo se iniciará como *En cola* , lo que indica que está esperando a que haya algún trabajo de Runbook disponible en la nube. Su estado cambiará a *Iniciando* cuando un trabajo de runbook lo solicite. Cuando el runbook comience a ejecutarse realmente, el estado será *En ejecución*.  
6. Cuando el trabajo de runbook se complete, debería aparecer el estado **Completado**.<br><br> ![Prueba de Runbook de entidad de seguridad](media/automation-verify-runas-authentication/job-summary-automationclassictutorialscript.png)<br>  
7. Para ver los resultados detallados del Runbook, haga clic en el icono **Salida** .
8. En la hoja **Salida** , debería ver que se ha autenticado correctamente y ha devuelto una lista de todas las máquinas virtuales con el modelo clásico que hay en la suscripción.
9. Cierre la hoja **Salida** para volver a la hoja **Resumen del trabajo**.
10. Cierre la hoja **Resumen del trabajo** y la hoja del runbook **AzureClassicAutomationTutorialScript**.

## <a name="next-steps"></a>Pasos siguientes
* Para empezar a trabajar con runbooks de PowerShell, consulte [Mi primer runbook de PowerShell](automation-first-runbook-textual-powershell.md).
* Para más información sobre la creación de gráficos, consulte [Creación gráfica en Azure Automation](automation-graphical-authoring-intro.md).

