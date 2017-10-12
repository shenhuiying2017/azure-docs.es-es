---
title: Respuesta a eventos con las alertas de Azure Log Analytics | Microsoft Docs
description: "Este tutorial le ayuda a comprender las alertas de Log Analytics para identificar información importante en el repositorio de OMS y avisarle proactivamente de problemas o invocar acciones para intentar corregirlos."
services: log-analytics
documentationcenter: 
author: MGoedtel
manager: carmonm
editor: 
ms.assetid: abb07f6c-b356-4f15-85f5-60e4415d0ba2
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/20/2017
ms.author: magoedte
ms.openlocfilehash: ccf0bd57b7f54b1d8d57b460df9bb381b29ee381
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="respond-to-events-with-log-analytics-alerts"></a>Respuesta a eventos con las alertas de Log Analytics
Las alertas de Log Analytics identifican información importante en el repositorio de Log Analytics.  Se crean mediante reglas de alerta que ejecutan automáticamente búsquedas de registro en intervalos regulares, y si los resultados de la búsqueda de registro coinciden con criterios determinados, se crea un registro de alerta y se puede configurar para realizar una respuesta automatizada.  Este tutorial es la continuación del tutorial [Creación y uso compartido de paneles de datos de Log Analytics](log-analytics-tutorial-dashboards.md).   

En este tutorial, aprenderá a:

> [!div class="checklist"]
> * Crear una regla de alerta
> * Configurar una regla de alertas para enviar una notificación por correo electrónico.

Para completar el ejemplo en este tutorial, debe disponer de una máquina virtual existente [conectada al área de trabajo de Log Analytics](log-analytics-quick-collect-azurevm.md).  

## <a name="log-in-to-azure-portal"></a>Iniciar sesión en Azure Portal
Inicie sesión en Azure Portal desde [https://portal.azure.com](https://portal.azure.com). 

## <a name="create-alerts"></a>Creación de alertas

Las alertas se crean mediante reglas de alerta que ejecutan automáticamente búsquedas de registros a intervalos regulares.  Puede crear alertas basadas en métricas de rendimiento específicas o cuando se creen determinados eventos, haya un evento ausente o se cree una serie de eventos dentro de una ventana de tiempo determinada.  Por ejemplo, se pueden usar alertas para avisarle cuando el uso medio de la CPU supere un determinado umbral o generar un evento cuando un servicio de Windows específico o Linux Daemon no funcionen.   Si los resultados de la búsqueda de registros coinciden con determinados criterios, se crea un registro de alertas. Luego, la regla puede ejecutar automáticamente una o varias acciones para avisarle proactivamente de la alerta o invocar otro proceso. 

En el ejemplo siguiente crearemos una regla de alertas de unidades métricas, que generarán una alerta para cada objeto de equipo en la consulta con un valor que supere un umbral del 90 %.

1. En Azure Portal, haga clic en **Más servicios**, en la esquina inferior izquierda. En la lista de recursos, escriba **Log Analytics**. Cuando comience a escribir, la lista se filtrará en función de la entrada. Seleccione de **Log Analytics**.
2. Inicie el portal de OMS; para ello, seleccione el portal de OMS y en la página **Introducción**, seleccione **Búsqueda de registros**.  
3. Seleccione **Favoritos** en la parte superior del portal y en el panel **Búsquedas guardadas** de la derecha, seleccione la consulta *Máquinas virtuales de Azure - Uso del procesador*.  
4. Haga clic en **Alerta** en la parte superior de la página para abrir la pantalla **Agregar regla de alerta**.  
5. Configure la regla de alertas con la siguiente información:  
   a. Proporcione un **nombre** para la alerta, como *Utilización del procesador de máquina virtual excedido >90*  
   b. Para **Ventana de tiempo**, especifique un intervalo de tiempo para la consulta, como *30*.  La consulta devuelve solo los registros que se crearon dentro de este intervalo de tiempo actual.  
   c. **Frecuencia de alerta** especifica con qué frecuencia se debe ejecutar la consulta.  En este ejemplo, especifique *5* minutos, que estará dentro de la ventana de tiempo que hemos especificado.  
   d. Seleccione **Unidades métricas** y especifique *90* para **Valor agregado** y especifique *3* para **Desencadenar alerta según** .  
   e. En **Acciones**, deshabilite la notificación por correo electrónico.
6. Haga clic en **Guardar** para completar la regla de alerta. Se iniciará la ejecución de inmediato.<br><br> ![Ejemplo de regla de alertas](media/log-analytics-tutorial-response/log-analytics-alert-01.png)

Los registros de alerta creados por reglas de alerta en Log Analytics tienen un Tipo de **Alerta** y un SourceSystem de **OMS**.<br><br> ![Ejemplo de eventos de alerta generados](media/log-analytics-tutorial-response/log-analytics-alert-events-01.png)  

## <a name="alert-actions"></a>Acciones de alerta
Puede realizar acciones avanzadas con alertas, como crear una notificación por correo electrónico, iniciar un [runbook de automatización](../automation/automation-runbook-types.md) o usar un webhook para crear un registro de incidentes en el sistema de administración de incidentes de ITSM, o con la [solución de IT Service Management Connector](log-analytics-itsmc-overview.md) como respuesta cuando se cumplan los criterios de alerta.   

Las acciones de correo electrónico envían un correo electrónico con los detalles de la alerta a uno o varios destinatarios. Puede especificar el asunto del mensaje de correo, pero su contenido es un formato estándar construido por Log Analytics.  Vamos a actualizar la regla de alertas creada anteriormente y a configurarla para la notificación por correo electrónico en lugar de supervisar activamente el registro de alertas con una búsqueda de registros.     

1. En el portal de OMS, en el menú superior, seleccione **Configuración** y, después, **Alertas**.
2. En la lista de reglas de alertas, haga clic en el icono de lápiz junto a la alerta creada anteriormente.
3. En la sección **Acciones**, habilite las notificaciones de correo electrónico.
4. Proporcione un **asunto** para el correo electrónico, como *La utilización del procesador ha excedido el umbral > 90*.
5. Agregue direcciones de uno o más destinatarios de correo electrónico en el campo **Destinatarios**.  Si especifica más de una dirección, separe las direcciones con un punto y coma (;).
6. Haga clic en **Guardar** para completar la regla de alerta. Se iniciará la ejecución de inmediato.<br><br> ![Regla de alertas con notificación por correo electrónico](media/log-analytics-tutorial-response/log-analytics-alert-02.png)

## <a name="next-steps"></a>Pasos siguientes
En este tutorial ha aprendido cómo las reglas de alerta pueden identificar un problema y resolverlo de forma proactiva cuando se ejecuten búsquedas de registro en intervalos programados y coincidan con criterios determinados.  

Siga este vínculo para ver ejemplos de scripts de Log Analytics creados previamente.  

> [!div class="nextstepaction"]
> [Ejemplos de scripts de Log Analytics](powershell-samples.md)