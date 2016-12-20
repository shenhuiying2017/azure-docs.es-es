---
title: "Solución de seguimiento de cambios de Log Analytics | Microsoft Docs"
description: "Puede usar la solución de seguimiento de cambios de configuración de Log Analytics para facilitar el proceso de identificación de los cambios de los servicios de Windows y software que se producen en su entorno. La identificación de estos cambios de configuración puede ayudarle a identificar problemas de funcionamiento."
services: operations-management-suite
documentationcenter: 
author: bandersmsft
manager: jwhit
editor: 
ms.assetid: f8040d5d-3c89-4f0c-8520-751c00251cb7
ms.service: operations-management-suite
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/31/2016
ms.author: banders
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: 56faeccbb25d2f0e3dbe6b104b5e9bc95a06ca36


---
# <a name="change-tracking-solution-in-log-analytics"></a>Solución de seguimiento de cambios de Log Analytics
Este artículo le ayudará a usar la solución Seguimiento de cambios de configuración en Log Analytics para identificar fácilmente los cambios de su entorno. La solución realiza un seguimiento de los cambios en el software, los Servicios de Windows, los demonios de Linux y los paquetes Linux. Identificar los cambios de configuración puede ayudarle a localizar problemas operativos. También puede configurar la solución para realizar un seguimiento de los cambios aplicados en archivos de Windows específicos.

La solución se instala para actualizar el tipo de agente que ha instalado. Los cambios en los demonios de Linux, los Servicios de Windows y el software instalados en los servidores supervisados se leen y, a continuación, los datos se envían al servicio de Log Analytics en la nube para su procesamiento. Se aplica la lógica a los datos recibidos y el servicio de nube registra los datos. Cuando se detectan cambios, se muestran los servidores con los cambios en el panel de seguimiento de cambios. Con la información en el panel de seguimiento de cambios, puede ver fácilmente los cambios realizados en la infraestructura de servidores.

## <a name="installing-and-configuring-the-solution"></a>Instalación y configuración de la solución
Utilice la siguiente información para instalar y configurar la solución.

* Debe tener un agente [Windows](log-analytics-windows-agents.md), [Operations Manager](log-analytics-om-agents.md) o [Linux](log-analytics-linux-agents.md) en cada equipo en el que desee supervisar los cambios.
* Agregue la solución de seguimiento de cambios al área de trabajo de OMS mediante el proceso descrito en [Incorporación de soluciones de Log Analytics desde la galería de soluciones](log-analytics-add-solutions.md).  No es necesario realizar ninguna configuración más.

### <a name="configure-windows-files-to-track"></a>Configuración de los archivos de Windows de los que se realizará un seguimiento
Use los pasos siguientes para configurar los archivos de los que se realizará un seguimiento en los equipos Windows.

1. En el portal de OMS, haga clic en **Configuración** (el símbolo de engranaje).
2. En la página **Configuración**, haga clic en **Datos** y, a continuación, en **Seguimiento de archivos de Windows**.
3. En Seguimiento de cambios de archivos de Windows, escriba toda la ruta de acceso, incluido el nombre del archivo del que desea realizar un seguimiento y, a continuación, haga clic en el símbolo **Agregar**. Por ejemplo: C:\Archivos de programa (x86)\Internet Explorer\iexplore.exe o C:\Windows\System32\drivers\etc\hosts.
4. Haga clic en **Guardar**.  
   ![Seguimiento de cambios de archivos de Windows](./media/log-analytics-change-tracking/windows-file-change-tracking.png)

### <a name="limitations"></a>Limitaciones
Actualmente, la solución de seguimiento de cambios no admite nada de lo siguiente:

* carpetas (directorios)
* recursión
* caracteres comodín
* variables de ruta de acceso
* sistemas de archivos de red

Otras limitaciones:

* Los valores y la columna **Tamaño máximo de archivos** no se usan en la implementación actual.
* Si recopila más de 2500 archivos en el ciclo de recopilación de 30 minutos, el rendimiento podría degradarse.
* Cuando el tráfico de red es elevado, los registros de cambios pueden tardar hasta seis horas en aparecer.
* Si modifica la configuración mientras un equipo está apagado, el equipo podría publicar cambios de archivos que pertenecían a la configuración anterior.

## <a name="change-tracking-data-collection-details"></a>Detalles de la recopilación de datos de seguimiento de cambios
El seguimiento de cambios recopila inventario de software y metadatos de servicio de Windows con los agentes habilitados.

La siguiente tabla muestra los métodos de recopilación de datos y otros detalles sobre cómo se recopilan los datos para el seguimiento de cambios.

| plataforma | Agente directo | Agente de SCOM | Agente Linux | Almacenamiento de Azure | ¿Se necesita SCOM? | Datos del agente de SCOM enviados a través del grupo de administración | Frecuencia de recopilación |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Windows y Linux |![Sí](./media/log-analytics-change-tracking/oms-bullet-green.png) |![Sí](./media/log-analytics-change-tracking/oms-bullet-green.png) |![Sí](./media/log-analytics-change-tracking/oms-bullet-green.png) |![No](./media/log-analytics-change-tracking/oms-bullet-red.png) |![No](./media/log-analytics-change-tracking/oms-bullet-red.png) |![Sí](./media/log-analytics-change-tracking/oms-bullet-green.png) |cada hora |

## <a name="use-change-tracking"></a>Uso de seguimiento de cambios
Una vez instalada la solución, puede ver el resumen de los cambios para los servidores supervisados mediante el icono **Seguimiento de cambios** en la página **Información general** de OMS.

![imagen del icono de seguimiento de cambios](./media/log-analytics-change-tracking/oms-changetracking-tile.png)

Puede ver los cambios en la infraestructura y, a continuación, profundizar en los detalles de las siguientes categorías:

* Cambios realizados por el tipo de configuración para los servicios de Windows y el software
* Cambios de software en aplicaciones y actualizaciones para los servidores individuales
* Número total de cambios de software para cada aplicación
* Paquetes Linux
* Cambios de servicio de Windows para servidores individuales
* Cambios de demonios de Linux

![imagen del panel de seguimiento de cambios](./media/log-analytics-change-tracking/oms-changetracking01.png)

![imagen del panel de seguimiento de cambios](./media/log-analytics-change-tracking/oms-changetracking02.png)

### <a name="to-view-changes-for-any-change-type"></a>Para ver los cambios de algún tipo de cambio
1. En la página **Información general**, haga clic en el icono **Seguimiento de cambios**.
2. En el panel **Seguimiento de cambios**, revise la información de resumen en una de las hojas de tipo de cambio y, a continuación, haga clic en una para obtener información detallada acerca de la misma en la página de **búsqueda de registros**.
3. En cualquiera de las páginas de búsqueda de registros, puede ver los resultados por tiempo, resultados detallados y el historial de búsqueda de registros. También puede filtrar por las facetas para restringir los resultados.

## <a name="next-steps"></a>Pasos siguientes
* Use [Búsquedas de registros en Log Analytics](log-analytics-log-searches.md) para ver datos detallados sobre el seguimiento de cambios.




<!--HONumber=Nov16_HO3-->


