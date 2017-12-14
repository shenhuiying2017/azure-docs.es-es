---
title: Hybrid Runbook Worker de Azure Automation en Linux | Microsoft Docs
description: "En este artículo encontrará información sobre cómo instalar un Hybrid Runbook Worker de Azure Automation que le permita ejecutar runbooks en equipos con Linux en su centro de datos local o entorno de nube."
services: automation
documentationcenter: 
author: georgewallace
manager: carmonm
editor: tysonn
ms.assetid: 
ms.service: automation
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/07/2017
ms.author: magoedte
ms.openlocfilehash: 938e4f4fa3326db23ea4c2b499c783de78dcfa76
ms.sourcegitcommit: fa28ca091317eba4e55cef17766e72475bdd4c96
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/14/2017
---
# <a name="how-to-deploy-a-linux-hybrid-runbook-worker"></a>Cómo implementar un Hybrid Runbook Worker en Linux

Los runbooks de Azure Automation no pueden tener acceso a los recursos de otras nubes o de entorno local, debido a que se ejecutan en la nube de Azure. La característica Hybrid Runbook Worker de Azure Automation permite ejecutar runbooks directamente en el equipo que hospeda el rol y en los recursos del entorno para administrar dichos recursos locales. Los runbooks se almacenan y administran en Azure Automation y después se entregan a uno o más equipos designados.

Esta funcionalidad se ilustra en la imagen siguiente:<br>  

![Información general de Trabajo híbrido de runbook](media/automation-offering-get-started/automation-infradiagram-networkcomms.png)

Para obtener una descripción general técnica del rol de Hybrid Runbook Worker, vea [Información general sobre la arquitectura de Automation](automation-offering-get-started.md#automation-architecture-overview). Revise la siguiente información relacionada con los [requisitos de hardware y software](automation-offering-get-started.md#hybrid-runbook-worker) así como la [información para preparar la red](automation-offering-get-started.md#network-planning) antes de empezar a implementar la característica Hybrid Runbook Worker.  Después de haber implementado correctamente un trabajo de runbook, revise la [ejecución de runbooks en Hybrid Runbook Worker](automation-hrw-run-runbooks.md) para más información sobre cómo configurar los runbooks para automatizar los procesos del centro de datos local o en otro entorno de nube.     

## <a name="hybrid-runbook-worker-groups"></a>Grupos de Trabajos híbridos de runbook
Cada Trabajo híbrido de runbook es miembro de un grupo de Trabajos híbridos de runbook que especifica cuando instala el agente.  Un grupo puede incluir solo un agente, pero puede instalar varios agentes en un grupo para contar con alta disponibilidad.

Cuando se inicia un runbook en Hybrid Runbook Worker, se especifica el grupo en el que se ejecuta.  Los miembros del grupo determinan qué trabajo atiende a la solicitud.  No es posible especificar un trabajo determinado.

## <a name="installing-linux-hybrid-runbook-worker"></a>Instalación de Hybrid Runbook Worker en Linux
Para instalar y configurar una instancia de Hybrid Runbook Worker en el equipo con Linux, hay que seguir un procedimiento muy sencillo para instalar y configurar el rol manualmente.   Es necesario habilitar la solución **Automation Hybrid Worker** en el área de trabajo de OMS y después ejecutar un conjunto de comandos para registrar el equipo como un trabajo y agregarlo a un grupo nuevo u otro existente. 

Antes de continuar, deberá anotar el área de trabajo de Log Analytics a la que está vinculada su cuenta de Automation y, también, la clave principal de la cuenta de Automation.  Encontrará ambos datos en el portal si selecciona la cuenta de Automation y, para el identificador de área de trabajo, selecciona **Área de trabajo** y, para la clave principal, selecciona **Claves**.  

1.  Habilite la solución "Automation Hybrid Worker" en OMS. Esto se puede hacer de la siguiente forma:

   1. En la Galería de soluciones del [Portal de OMS](https://mms.microsoft.com), habilite la solución **Automation Hybrid Worker**.
   2. Ejecute el siguiente cmdlet:

        ```powershell
         $null = Set-AzureRmOperationalInsightsIntelligencePack -ResourceGroupName  <ResourceGroupName> -WorkspaceName <WorkspaceName> -IntelligencePackName  "AzureAutomation" -Enabled $true
        ```

2.  Ejecute el siguiente comando y cambie los valores de los parámetros *-w*, *-k*, *-g* y *-e*. Para el parámetro *-g*, sustituya el valor por el nombre del grupo de Hybrid Runbook Worker al que se debe unir el nuevo Hybrid Runbook Worker de Linux. Si el nombre no existe ya en su cuenta de Automation, se realizará un nuevo grupo de Hybrid Runbook Worker con ese nombre.
    
    ```
    sudo python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/scripts/onboarding.py --register -w <OMSworkspaceId> -k <AutomationSharedKey> -g <hybridgroupname> -e <automationendpoint>
    ```
3. Una vez completado el comando, la hoja Grupos de Hybrid Worker en Azure Portal muestra el nuevo grupo y el número de miembros o, si es un grupo existente, el número de miembros aumenta.  Puede seleccionar el grupo de la lista en la hoja **Grupos de Hybrid Worker** y seleccionar el icono **Hybrid Workers**.  En la hoja **Hybrid Workers**, verá que aparece cada miembro del grupo.  


## <a name="turning-off-signature-validation"></a>Desactivación de la validación de firma 
De forma predeterminada, las instancias de Hybrid Runbook Worker de Linux requieren la validación de la firma. Si ejecuta un runbook sin firmar en un trabajo, verá un error que dice que no se ha podido validar la firma. Para desactivar la validación de la firma, ejecute el siguiente comando y sustituya el segundo parámetro por el identificador del área de trabajo de OMS:

    ```
    sudo python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/scripts/require_runbook_signature.py --false <OMSworkspaceId>
    ```
   
## <a name="next-steps"></a>Pasos siguientes

* Revise la [ejecución de runbooks en Hybrid Runbook Worker](automation-hrw-run-runbooks.md) para más información sobre cómo configurar los runbooks para automatizar los procesos del centro de datos local o en otro entorno de nube.
* Para obtener instrucciones sobre cómo quitar Hybrid Runbook Workers, vea [Quitar Hybrid Runbook Workers de Azure Automation](automation-remove-hrw.md).
