---
title: "Inicio y detención de Azure Stack | Microsoft Docs"
description: "Obtenga información acerca de cómo iniciar y apagar Azure Stack."
services: azure-stack
documentationcenter: 
author: mattbriggs
manager: femila
editor: 
ms.assetid: 43BF9DCF-F1B7-49B5-ADC5-1DA3AF9668CA
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/18/2018
ms.author: mabrigg
ms.openlocfilehash: 98bf75f5883b734c785ed1a3ed924afca1737c56
ms.sourcegitcommit: 79683e67911c3ab14bcae668f7551e57f3095425
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/25/2018
---
# <a name="start-and-stop-azure-stack"></a>Inicio y detención de Azure Stack

*Se aplica a: sistemas integrados de Azure Stack (versión 1712 y posteriores)*.

## <a name="stop-azure-stack"></a>Detención de Azure Stack 

Realice los pasos siguientes para apagar Azure Stack:

1. Abra una sesión de punto de conexión con privilegios (PEP) desde un equipo con acceso de red a las máquinas virtuales de Azure Stack ERCS. Para obtener instrucciones, consulte [Uso del punto de conexión con privilegios en Azure Stack](azure-stack-privileged-endpoint.md).

2. Desde el PEP, ejecute lo siguiente:

    ```powershell
      Stop-AzureStack
    ```

3. Espere a que todos los nodos físicos de Azure Stack se apaguen.

> [!Note]  
> Para comprobar el estado de encendido de un nodo físico siga las instrucciones del fabricante de equipos originales (OEM) que le proporcionó el hardware de Azure Stack. 

## <a name="start-azure-stack"></a>Inicio de Azure Stack 

Realice los pasos siguientes para iniciar Azure Stack. Siga los pasos que se indican a continuación independientemente del modo en que se detuvo Azure Stack.

1. Encienda cada uno de los nodos físicos de su entorno de Azure Stack. Consulte las instrucciones de los nodos físicos. Para ello, siga las instrucciones del fabricante de equipos originales (OEM) que le proporcionó el hardware de Azure Stack.

2. Espere hasta que se inicien los servicios de infraestructura de Azure Stack. El proceso de inicio de los servicios de infraestructura de Azure Stack puede tardar hasta dos horas en completarse. Puede comprobar el estado de inicio de Azure Stack con el cmdlet [**Get-ActionStatus**](#get-the-startup-status-for-azure-stack).


## <a name="get-the-startup-status-for-azure-stack"></a>Consulta del estado de inicio de Azure Stack

Realice los pasos siguientes para obtener la rutina de inicio de Azure Stack:

1. Abra una sesión de punto de conexión con privilegios desde un equipo con acceso de red a las máquinas virtuales de Azure Stack ERCS.

2. Desde el PEP, ejecute lo siguiente:

    ```powershell
      Get-ActionStatus Start-AzureStack
    ```

## <a name="troubleshoot-startup-and-shutdown-of-azure-stack"></a>Solución de problemas de inicio y apagado de Azure Stack

Siga los pasos que se indican a continuación en caso de que los servicios de infraestructura e inquilinos no se inicien a las 2 horas de haber encendido el entorno de Azure Stack. 

1. Abra una sesión de punto de conexión con privilegios desde un equipo con acceso de red a las máquinas virtuales de Azure Stack ERCS.

2. Ejecutar: 

    ```powershell
      Test-AzureStack
      ```

3. Revise el resultado y resuelva los errores de estado. Para obtener más información, consulte [Run a validation test of Azure Stack](azure-stack-diagnostic-test.md) (Ejecutar una prueba de validación de Azure Stack).

4. Ejecutar:

    ```powershell
      Start-AzureStack
    ```

5. Si la ejecución de **Start_AzureStack** produce un error, póngase en contacto con el Soporte técnico de servicios al cliente de Microsoft. 

## <a name="next-steps"></a>pasos siguientes 

Para obtener más información acerca de la herramienta de diagnóstico de Azure Stack y el registro de problemas, consulte Herramientas de diagnóstico de Azure Stack. Herramientas de diagnóstico de Azure Stack. Herramientas de diagnóstico de Azure Stack. Herramientas de diagnóstico de Azure Stack.
