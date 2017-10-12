---
title: "Creación de alertas en Log Analytics (OMS) | Microsoft Docs"
description: "Las alertas de Log Analytics identifican información importante en el repositorio de OMS y pueden avisarle proactivamente de problemas o invocar acciones para intentar corregirlos.  En este artículo se describe cómo crear una regla de alerta y los detalles de las distintas acciones que pueden realizar."
services: log-analytics
documentationcenter: 
author: bwren
manager: jwhit
editor: tysonn
ms.assetid: 6cfd2a46-b6a2-4f79-a67b-08ce488f9a91
ms.service: log-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/23/2017
ms.author: bwren
ms.openlocfilehash: c34fb7295e8f386f0e7cf2c1db6b26a3e49eae98
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="working-with-alert-rules-in-log-analytics"></a>Uso de reglas de alertas en Log Analytics
Las alertas se crean mediante reglas de alerta que ejecutan automáticamente búsquedas de registros a intervalos regulares.  Crean un registro de alertas si los resultados coinciden con criterios concretos.  Luego, la regla puede ejecutar automáticamente una o varias acciones para avisarle proactivamente de la alerta o invocar otro proceso.   

En este artículo se describen los procesos para crear y editar reglas de alerta mediante el portal OMS.  Para más información sobre las distintas configuraciones y cómo implementar la lógica necesaria, consulte [Descripción de las alertas de Log Analytics](log-analytics-alerts.md).

>[!NOTE]
> Actualmente no se puede crear ni modificar ninguna regla de alerta mediante Azure Portal. 

## <a name="create-an-alert-rule"></a>Crear una regla de alerta

Para crear una regla de alerta mediante el portal OMS, primero debe crear una búsqueda de registros para los registros que deben invocar la alerta.  El botón **Alerta** estará entonces disponible para que pueda crear y configurar la regla de alerta.

>[!NOTE]
> Actualmente se puede crear un máximo de 250 reglas de alertas en un área de trabajo de OMS. 

1. En la página de información general de OMS, haga clic en **Búsqueda de registros**.
2. Cree una nueva consulta de búsqueda de registros o seleccione una búsqueda de registros guardada. 
3. Haga clic en **Alerta** en la parte superior de la página para abrir la pantalla **Agregar regla de alerta**.
4. Configure la regla de alerta mediante la información de la sección [Detalles sobre las reglas de alerta](#details-of-alert-rules) que aparece más abajo.
6. Haga clic en **Guardar** para completar la regla de alerta.  Se iniciará la ejecución de inmediato.


## <a name="edit-an-alert-rule"></a>Edición de una regla de alerta
Puede obtener una lista de todas las reglas de alerta en el menú **Alertas** de la **configuración** de Log Analytics.  

![Administrar alertas](./media/log-analytics-alerts/configure.png)

1. En la consola de OMS, seleccione el icono **Configuración** .
2. Seleccione **Alertas**.

Puede realizar varias acciones desde esta vista.

* Deshabilite una regla seleccionando el icono **Desactivada** que aparece a su lado.
* Edite una regla de alerta haciendo clic en el icono de lápiz junto a ella.
* Quite una regla de alerta haciendo clic en el icono **X** que está a su lado. 

## <a name="details-of-alert-rules"></a>Detalles sobre las reglas de alerta
Cuando cree o edite una regla de alerta en el portal de OMS, trabajará con las páginas **Agregar regla de alerta** o **Editar regla de alerta** página.  Las tablas siguientes describen los campos de esta pantalla.

![Regla de alerta](media/log-analytics-alerts/add-alert-rule.png)

### <a name="alert-information"></a>Información de alertas
Estas son las configuraciones básicas de la regla de alerta y las alertas que crea.

| Propiedad | Descripción |
|:--- |:---|
| Nombre | Nombre único para identificar la regla de alerta. Este nombre se incluirá en cualquier alerta creada por la regla.  |
| Descripción | Descripción opcional de la regla de alerta. |
| Severity |Gravedad de las alertas creadas por esta regla. |

### <a name="search-query-and-time-window"></a>Ventana de consulta y ventana de tiempo
La ventana de consulta y la ventana de tiempo que devuelven los registros que se evalúan para determinar si se debe crear alguna alerta.

| Propiedad | Descripción |
|:--- |:---|
| Consulta de búsqueda | Esta es la consulta que se va a ejecutar.  Los registros devueltos por esta consulta se utilizarán para determinar si se crea una alerta.<br><br>Seleccione **Use current search query** (Usar consulta de búsqueda actual) para utilizar la consulta actual o seleccionar una búsqueda guardada de la lista.  La sintaxis de la consulta se proporciona en el cuadro de texto, desde donde puede modificarla si fuera necesario. |
| Período de tiempo |Especifica el intervalo de tiempo para la consulta.  La consulta devuelve solo los registros que se crearon dentro de este intervalo de tiempo actual.  Puede ser cualquier valor entre 5 minutos y 24 horas.  Debe ser mayor que la frecuencia de alertas o igual a este valor.  <br><br> Por ejemplo, si el período de tiempo se establece en 60 minutos, y la consulta se ejecuta a las 13:15, se devolverán solo los registros que se crearon entre las 12:15 y las 13:15. |

Al facilitar el período de tiempo para la regla de alerta, se mostrará el número de registros existentes que cumplen los criterios de búsqueda para ese período de tiempo.  Esto puede ayudarle a determinar la frecuencia con que proporcionará el número de resultados esperados.

### <a name="schedule"></a>Schedule
Define la frecuencia con la que se ejecuta la consulta de búsqueda.

| Propiedad | Descripción |
|:--- |:---|
| Frecuencia de alertas | Especifica con qué frecuencia se debe ejecutar la consulta. Puede ser cualquier valor entre 5 minutos y 24 horas. Debe ser igual que el período de tiempo o inferior a este valor.  Si el valor es mayor que la ventana de tiempo, se arriesga a que se pierdan registros.<br><br>Por ejemplo, considere la posibilidad de una ventana de tiempo de 30 minutos y una frecuencia de 60 minutos.  Si la consulta se ejecuta a la 1:00, devolverá registros entre las 12:30 y la 1:00 p. m.  La próxima vez que se ejecute la consulta será a las 2:00 y devolverá los registros comprendidos entre la 1:30 y las 2:00.  Nunca se evaluarán los registros creados entre la 1:00 y la 1:30. |


### <a name="generate-alert-based-on"></a>Generación de alerta según
Define los criterios que se evaluarán con respecto a los resultados de la consulta de búsqueda para determinar si se debería crear una alerta.  Estos detalles variarán en función del tipo de regla de alertas que seleccione.  Para obtener información sobre los distintos tipos de reglas de alertas, consulte [Respuesta a los problemas de Log Analytics mediante el uso de alertas](log-analytics-alerts.md).

| Propiedad | Descripción |
|:--- |:---|
| Suprimir alertas | Al activar la supresión de la regla de alerta, las acciones de la regla se deshabilitan durante un período de tiempo definido después de crear una nueva alerta. La regla se seguirá ejecutando y creará registros de alerta si se cumplen los criterios. Esto se hace así para que tenga tiempo para corregir el problema sin ejecutar acciones duplicadas. |

#### <a name="number-of-results-alert-rules"></a>Reglas de alertas para números de resultados

| Propiedad | Descripción |
|:--- |:---|
| Número de resultados |Se crea una alerta si el número de registros devueltos por la consulta es **mayor que** o **menor que** el valor que proporcione.  |

#### <a name="metric-measurement-alert-rules"></a>Reglas de alertas para medición de métricas

| Propiedad | Descripción |
|:--- |:---|
| Valor agregado | Valor umbral que cada valor agregado de los resultados debe superar para que se considere una infracción. |
| Activación de alerta según | El número de infracciones para que se cree una alerta.  Puede especificar **Infracciones totales** para cualquier combinación de infracciones en el conjunto de resultados o **Infracciones consecutivas** para que las infracciones deban tener lugar en muestras consecutivas. |

### <a name="actions"></a>Acciones
Las reglas de alerta siempre crean un [registro de alerta](#alert-records) cuando se alcanza el umbral.  También puede definir la ejecución de una o varias respuestas, como enviar un mensaje de correo electrónico o iniciar un runbook.



#### <a name="email-actions"></a>Acciones de correo electrónico
Las acciones de correo electrónico envían un correo electrónico con los detalles de la alerta a uno o varios destinatarios.

| Propiedad | Descripción |
|:--- |:---|
| Notificación por correo electrónico |Especifique **Sí** si desea que se le envíe un correo electrónico cuando se active la alerta. |
| Asunto |El asunto del correo electrónico.  No se puede modificar el cuerpo del mensaje de correo. |
| Recipients |Direcciones de todos los destinatarios de correo electrónico.  Si especifica más de una dirección, separe las direcciones con un punto y coma (;). |

#### <a name="webhook-actions"></a>Acciones de webhook
Las acciones de Webhook permiten invocar un proceso externo a través de una sola solicitud HTTP POST.

| Propiedad | Descripción |
|:--- |:---|
| webhook |Especifique **Sí** si desea llamar a un webhook cuando se desencadene la alerta. |
| Dirección URL de Webhook |La dirección URL del webhook. |
| Incluir la carga personalizada de JSON |Seleccione esta opción si desea reemplazar la carga predeterminada por una carga personalizada. |
| Especificar la carga JSON personalizada |La carga personalizada para el webhook.  Consulte la sección anterior para obtener más información. |

#### <a name="runbook-actions"></a>Acciones de runbook
Las acciones de runbook inician un runbook en Automatización de Azure. 

>[!NOTE]
> Debe tener instalada la solución Automation en el área de trabajo para que se habilite esta acción. 


| Propiedad | Descripción |
|:--- |:---|
| Runbook | Especifique **Sí** si desea iniciar un runbook de Automatización de Azure cuando se desencadene la alerta.  |
| Cuenta de Automation | Especifica la cuenta de Automation de la que se seleccionan los runbooks.  Se trata de la cuenta de acción que está vinculada al área de trabajo. |
| Seleccionar un runbook | Seleccione el runbook que desea iniciar cuando se crea una alerta. |
| Ejecutar en | Seleccione **Azure** para ejecutar el runbook en la nube.  Seleccione **Hybrid Worker** para ejecutar el runbook en un agente con [Hybrid Runbook Worker](../automation/automation-hybrid-runbook-worker.md ) instalado.  |




## <a name="next-steps"></a>Pasos siguientes
* Instale la [solución de Administración de alertas](log-analytics-solution-alert-management.md) para analizar las alertas creadas en Log Analytics junto con las alertas recopiladas desde System Center Operations Manager (SCOM).
* Obtenga más información sobre las [búsquedas de registros](log-analytics-log-searches.md) que pueden generar alertas.
* Complete un tutorial para [configurar un webhook](log-analytics-alerts-webhooks.md) con una regla de alerta.  
* Aprenda a escribir [runbooks en Automatización de Azure](https://azure.microsoft.com/documentation/services/automation) para solucionar los problemas identificados por las alertas.

