---
title: "Administración de alertas de productos de supervisión de Microsoft | Microsoft Docs"
description: "Una alerta indica un problema que requiere la atención por parte del administrador.  Este artículo describe las diferencias en cómo las alertas se crean y administran en System Center Operations Manager (SCOM) y Log Analytics y proporciona prácticas recomendadas para aprovechar los dos productos para una estrategia de administración de alertas híbrida."
services: operations-management-suite
documentationcenter: 
author: bwren
manager: jwhit
editor: tysonn
ms.assetid: 6572c3f8-78ca-4fa9-8fe1-d0b488590788
ms.service: operations-management-suite
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/11/2017
ms.author: bwren
ms.openlocfilehash: 7df2fd7ef838465a60e3b0ce2f889127b7487684
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="managing-alerts-with-microsoft-monitoring"></a>Administración de alertas con la supervisión de Microsoft
Una alerta indica un problema que requiere la atención por parte del administrador.  Hay claras diferencias entre System Center Operations Manager (SCOM) y Log Analytics en Operations Management Suite (OMS) en cuanto a cómo se crean alertas, cómo se administran y analizan, y cómo se notifica que se ha detectado un problema crítico.

## <a name="alerts-in-operations-manager"></a>Alertas en Operations Manager
Se generan alertas en SCOM mediante monitores o reglas individuales para indicar un problema específico.  Un monitor puede generar una alerta cuando entra en un estado de error, mientras que una regla puede generar una alerta para indicar algún problema crítico que no está relacionado directamente con el estado de un objeto administrado.  Los módulos de administración incluyen una serie de flujos de trabajo que crean alertas para la aplicación o el servicio que administran.  El ajuste forma parte del proceso de configuración de un nuevo módulo de administración para garantizar de que no recibirá un número excesivo de alertas para problemas que no considere críticos.

![Vista de alertas de SCOM](media/operations-management-suite-monitoring-alerts/scom-alert-view.png)

SCOM proporciona una administración completa de alertas con alertas con un estado que los administradores pueden cambiar mientras trabajan para resolver el problema.  Cuando se haya resuelto el problema, el administrador establecerá la alerta en cerrada en el momento que ya no aparezca en las vistas mostrando alertas activas.  Las alertas que se generan a partir de los monitores se pueden resolver automáticamente cuando el monitor vuelve a un estado correcto.

![Estado de alerta](media/operations-management-suite-monitoring-alerts/scom-alert-status.png)

## <a name="alerts-in-log-analytics"></a>Alertas de Log Analytics
Se crea una alerta en Log Analytics desde una consulta de registro que se ejecuta automáticamente en intervalos regulares.  Puede crear una regla de alerta desde cualquier consulta de registro.  Si la consulta devuelve resultados que coinciden con los criterios que especifica, se crea una alerta.  Podría tratarse de una consulta específica que crea una alerta si se detecta un evento determinado, o podría utilizar una consulta más general que busque cualquier evento de error relacionado con una aplicación determinada.

Las alertas de Log Analytics se escriben en el repositorio de OMS como un evento y se pueden recuperar con una consulta de registro.  No tienen un estado como los eventos de SCOM, por lo que puede indicar si se ha resuelto el problema.

![Alerta de OMS](media/operations-management-suite-monitoring-alerts/oms-alert.png)

Cuando SCOM se utiliza como origen de datos para Log Analytics, las alertas de SCOM se escriben en el repositorio de OMS cuando se crean y modifican.  

![Alerta de SCOM](media/operations-management-suite-monitoring-alerts/scom-alert.png)

La [solución de administración de alertas](http://technet.microsoft.com/library/mt484092.aspx) proporciona un resumen de las alertas activas y varias consultas comunes para recuperar diferentes conjuntos de alertas.  Esto le brinda un análisis más eficaz de las alertas que un informe en SCOM.  Puede profundizar en resúmenes y en datos detallados, y crear consultas ad hoc para recuperar diferentes conjuntos de alertas.

![solución de administración de alertas](media/operations-management-suite-monitoring-alerts/alert-management.png)

## <a name="notifications"></a>Notificaciones
Las notificaciones en SCOM envían un correo electrónico o texto en respuesta a las alertas que coinciden con determinados criterios.  Puede crear suscripciones de notificación diferentes que realicen notificaciones a diferentes personas según estos criterios, como el objeto que se está supervisando, la gravedad de la alerta, el tipo de problema que se ha detectado o la hora del día.

Algunas suscripciones pueden utilizarse para implementar una estrategia de notificación completa para un gran número de módulos de administración.

![Alertas de SCOM](media/operations-management-suite-monitoring-alerts/alerts-overview-scom.png)

Log Analytics puede enviarle una notificación por correo electrónico de que se ha creado una alerta mediante el establecimiento de una acción de notificación de correo electrónico en cada [regla de alerta](http://technet.microsoft.com/library/mt614775.aspx).  No tiene la capacidad de SCOM de suscripción a varias alertas con una sola regla.  También debe crear sus propias reglas de alerta, puesto que OMS no proporciona ninguna preconfigurada.

![Alertas de Log Analytics](media/operations-management-suite-monitoring-alerts/alerts-overview-oms.png)

No puede administrar completamente alertas de SCOM en Log Analytics, aunque puede modificarlas en la consola de operaciones.  Log Analytics resulta útil como parte de un proceso de administración de alertas, ya que proporciona herramientas de análisis con las que SCOM no cuenta.

## <a name="alert-remediation"></a>Corrección de alerta
[Corrección](http://technet.microsoft.com/library/mt614775.aspx) hace referencia a un intento de corregir automáticamente el problema identificado mediante una alerta.

SCOM permite ejecutar diagnósticos y recuperaciones en respuesta a un monitor con un estado incorrecto.  Esto sucede de manera simultánea en el monitor que crea la alerta.  Los diagnósticos y las recuperaciones se implementan normalmente como un script que se ejecuta en el agente.  Un diagnóstico intenta recopilar más información sobre el problema detectado mientras que una recuperación intenta corregir el problema.

Log Analytics le permite iniciar un [runbook de Azure Automation](https://azure.microsoft.com/documentation/services/automation/) o una llamada de un webhook en respuesta a una alerta de Log Analytics.  Los runbooks pueden contener una lógica compleja implementada en PowerShell.  El script se ejecuta en Azure y puede tener acceso a los recursos de Azure o los recursos externos disponibles de la nube.  Automatización de Azure tiene la capacidad de ejecutar runbooks en un servidor en su centro de datos local, pero esta característica no está disponible actualmente al iniciar el runbook en respuesta a las alertas de Log Analytics.

Tanto las recuperaciones de SCOM y runbooks de OMS pueden contener scripts de PowerShell, pero las recuperaciones son más difíciles de crear y administrar, ya que deben estar dentro de un módulo de administración.  Los runbooks se almacenan en Automatización de Azure, que proporciona características para crear, probar y administrar runbooks.

Si usa SCOM como un origen de datos para Log Analytics, puede crear una alerta de Log Analytics mediante una consulta de registro para recuperar las alertas de SCOM almacenadas en el repositorio de OMS.  Esto le permitirá ejecutar un runbook de Automatización de Azure en respuesta a una alerta de SCOM.  Por supuesto, dado que el runbook se ejecutará en Azure, no sería una estrategia viable para las recuperaciones de problemas locales.

## <a name="next-steps"></a>Pasos siguientes
* Obtenga información sobre los detalles de las [alertas de System Center Operations Manager (SCOM)](https://technet.microsoft.com/library/hh212913.aspx).

