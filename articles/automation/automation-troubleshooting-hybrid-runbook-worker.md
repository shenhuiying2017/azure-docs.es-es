---
title: "Solución de problemas de Hybrid Runbook Worker de Azure Automation | Microsoft Docs"
description: "Se describen los síntomas, las causas y la solución de los problemas más comunes de Hybrid Runbook Worker de Azure Automation."
services: automation
documentationcenter: 
author: georgewallace
manager: jwhit
editor: tysonn
ms.assetid: 02c6606e-8924-4328-a196-45630c2255e9
ms.service: automation
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/25/2017
ms.author: magoedte
ms.openlocfilehash: 75f4ac1bc940a2b1d8e4ac6aeac8b80c642489da
ms.sourcegitcommit: fa28ca091317eba4e55cef17766e72475bdd4c96
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/14/2017
---
# <a name="troubleshooting-tips-for-hybrid-runbook-worker"></a>Consejos para la solución de problemas de Hybrid Runbook Worker

En este artículo se proporciona ayuda para solucionar los errores comunes que puede experimentar con Hybrid Runbook Worker de Automation y sugiere posibles soluciones para resolverlos.

## <a name="a-runbook-job-terminates-with-a-status-of-suspended"></a>Un trabajo de Runbook finaliza con el estado Suspendido

El Runbook se suspende poco después de intentar ejecutarlo tres veces. Hay condiciones que pueden interrumpir el Runbook antes de su correcta finalización y el mensaje de error relacionado no incluye información adicional que indique el motivo. En este artículo se proporcionan pasos que ayudan a solucionar problemas relacionados con errores de ejecución de Runbook de Hybrid Runbook Worker.

Si su problema con Azure no se trata en este artículo, visite los foros de Azure en [MSDN y Stack Overflow](https://azure.microsoft.com/support/forums/). Puede publicar su problema en ellos o en [@AzureSupport en Twitter](https://twitter.com/AzureSupport). También puede presentar una solicitud de soporte técnico de Azure; para ello seleccione **Obtener soporte técnico** en el sitio de [soporte técnico de Azure](https://azure.microsoft.com/support/options/) .

### <a name="symptom"></a>Síntoma
Se produce un error en la ejecución de un Runbook y el error devuelto es "La acción de trabajo 'Activar' no se puede ejecutar porque el proceso se detuvo de manera inesperada. La acción de trabajo se intentó 3 veces."

Hay varias causas posibles para este error: 

1. Hybrid Worker está detrás de un firewall o proxy.
2. El equipo en el que se está ejecutando Hybrid Worker no cumple los [requisitos de hardware](automation-offering-get-started.md#hybrid-runbook-worker) mínimos.  
3. No se pueden autenticar los Runbooks con recursos locales.

#### <a name="cause-1-hybrid-runbook-worker-is-behind-proxy-or-firewall"></a>Causa 1: Hybrid Runbook Worker está detrás de un firewall o proxy
El equipo en el que se está ejecutando Hybrid Runbook Worker está detrás de un servidor proxy o firewall y es posible que el acceso de red saliente no esté permitido o configurado correctamente.

#### <a name="solution"></a>Solución
Compruebe que el equipo tenga acceso saliente a *.azure-automation.net en el puerto 443. 

#### <a name="cause-2-computer-has-less-than-minimum-hardware-requirements"></a>Causa 2: El equipo no cumple los requisitos mínimos de hardware
Los equipos que ejecutan Hybrid Runbook Worker deben cumplir los requisitos mínimos de hardware antes de indicar que hospede esta característica. De lo contrario, en función del uso de recursos de otros procesos en segundo plano y la contención provocada por Runbooks durante la ejecución, el equipo estará sobreutilizado y provocará retrasos o tiempos de espera en los trabajos de Runbook. 

#### <a name="solution"></a>Solución
Confirme primero que el equipo designado para ejecutar la característica Hybrid Runbook Worker cumple los requisitos mínimos de hardware.  Si es así, supervise el uso de la CPU y de memoria para determinar las posibles correlaciones entre el rendimiento de los procesos de Hybrid Runbook Worker y Windows.  Si hay presión de memoria o de la CPU, esto puede indicar que se deben actualizar o agregar procesadores adicionales, o aumentar la memoria para resolver el cuello de botella de recursos y el error. También puede seleccionar un recurso de proceso diferente que pueda admitir los requisitos mínimos y escalarse cuando las demandas de trabajo indiquen que es necesario un aumento.         

#### <a name="cause-3-runbooks-cannot-authenticate-with-local-resources"></a>Causa 3: No se pueden autenticar los Runbooks con recursos locales.

#### <a name="solution"></a>Solución
Compruebe el registro de eventos **Microsoft SMA** para un evento correspondiente con la descripción *Proceso Win32 cerrado con el código [4294967295]*.  La causa de este error es que no ha configurado la autenticación en sus Runbooks o especificado las credenciales de identificación para el grupo de Hybrid Worker.  Revise los [permisos de runbook](automation-hrw-run-runbooks.md#runbook-permissions) para confirmar que ha configurado correctamente la autenticación para los runbooks.  

## <a name="next-steps"></a>Pasos siguientes

Para solucionar otros problemas en Automation, consulte [Solución de problemas de Azure Automation](automation-troubleshooting-automation-errors.md). 