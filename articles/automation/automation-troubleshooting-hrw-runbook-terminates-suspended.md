---
title: 'Hybrid Runbook Worker: un trabajo de runbook finaliza con el estado Suspendido | Microsoft Docs'
description: "Causas de los síntomas y soluciones para error de finalización de trabajo de Hybrid Runbook Worker."
services: automation
documentationcenter: 
author: mgoedtel
manager: jwhit
editor: tysonn
ms.assetid: 02c6606e-8924-4328-a196-45630c2255e9
ms.service: automation
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/17/2016
ms.author: magoedte
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 7c6365b729d73f1c5b9bc57952b1723255d9e9f0


---
# <a name="hybrid-runbook-worker-a-runbook-job-terminates-with-a-status-of-suspended"></a>Hybrid Runbook Worker: un trabajo de Runbook finaliza con el estado Suspendido
## <a name="summary"></a>Resumen
El Runbook se suspende poco después de intentar ejecutarlo tres veces. Hay condiciones que pueden interrumpir el Runbook antes de su correcta finalización y el mensaje de error relacionado no incluye información adicional que indique el motivo. En este artículo se proporcionan pasos que ayudan a solucionar problemas relacionados con errores de ejecución de Runbook de Hybrid Runbook Worker.

Si su problema con Azure no se trata en este artículo, visite los foros de Azure en [MSDN y Stack Overflow](https://azure.microsoft.com/support/forums/). Puede publicar su problema en ellos o en [@AzureSupport en Twitter](https://twitter.com/AzureSupport). También puede presentar una solicitud de soporte técnico de Azure; para ello seleccione **Obtener soporte técnico** en el sitio de [soporte técnico de Azure](https://azure.microsoft.com/support/options/) .

## <a name="symptom"></a>Síntoma
Se produce un error en la ejecución de un Runbook y el error devuelto es "La acción de trabajo 'Activar' no se puede ejecutar porque el proceso se detuvo de manera inesperada. La acción de trabajo se intentó 3 veces."

## <a name="cause"></a>Causa
Hay varias causas posibles para este error: 

1. Hybrid Worker está detrás de un firewall o proxy.
2. El equipo en el que se está ejecutando Hybrid Worker no cumple los [requisitos](automation-hybrid-runbook-worker.md#hybrid-runbook-worker-requirements) 
3. No se pueden autenticar los Runbooks con recursos locales.

## <a name="cause-1-hybrid-runbook-worker-is-behind-proxy-or-firewall"></a>Causa 1: Hybrid Runbook Worker está detrás de un firewall o proxy
El equipo en el que se está ejecutando Hybrid Runbook Worker está detrás de un servidor proxy o firewall y es posible que el acceso de red saliente no esté permitido o configurado correctamente.

### <a name="solution"></a>Solución
Compruebe que el equipo tenga acceso saliente a *.cloudapp.net en los puertos 443, 9354 y 30000-30199. 

## <a name="cause-2-computer-has-less-than-minimum-hardware-requirements"></a>Causa 2: El equipo no cumple los requisitos mínimos de hardware
Los equipos que ejecutan Hybrid Runbook Worker deben cumplir los requisitos mínimos de hardware antes de indicar que hospede esta característica. De lo contrario, en función del uso de recursos de otros procesos en segundo plano y la contención provocada por Runbooks durante la ejecución, el equipo estará sobreutilizado y provocará retrasos o tiempos de espera en los trabajos de Runbook. 

### <a name="solution"></a>Solución
Confirme primero que el equipo designado para ejecutar la característica Hybrid Runbook Worker cumple los requisitos mínimos de hardware.  Si es así, supervise el uso de la CPU y de memoria para determinar las posibles correlaciones entre el rendimiento de los procesos de Hybrid Runbook Worker y Windows.  Si hay presión de memoria o de la CPU, esto puede indicar que se deben actualizar o agregar procesadores adicionales, o aumentar la memoria para resolver el cuello de botella de recursos y el error. También puede seleccionar un recurso de proceso diferente que pueda admitir los requisitos mínimos y escalarse cuando las demandas de trabajo indiquen que es necesario un aumento.         

## <a name="cause-3-runbooks-cannot-authenticate-with-local-resources"></a>Causa 3: No se pueden autenticar los Runbooks con recursos locales.
### <a name="solution"></a>Solución
Compruebe el registro de eventos **Microsoft SMA** para un evento correspondiente con la descripción *Proceso Win32 cerrado con el código [4294967295]*.  La causa de este error es que no ha configurado la autenticación en sus Runbooks o especificado las credenciales de identificación para el grupo de Hybrid Worker.  Revise los [permisos de runbook](automation-hybrid-runbook-worker.md#runbook-permissions) para confirmar que ha configurado correctamente la autenticación para los runbooks.  




<!--HONumber=Nov16_HO3-->


