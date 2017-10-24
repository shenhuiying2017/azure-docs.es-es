---
title: "Supervisión y procesamiento de eventos de seguridad en Azure Security Center | Microsoft Docs"
description: "Obtenga información sobre cómo usar el panel de eventos de Security Center para ver los eventos de seguridad relativos a máquinas virtuales de Azure y a equipos que no son de Azure."
services: security-center
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: 
ms.assetid: 70c076ef-3ad4-4000-a0c1-0ac0c9796ff1
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/05/2017
ms.author: terrylan
ms.openlocfilehash: 367067874b167268bd690a9e0b55412e92e08122
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="monitoring-and-processing-security-events-in-azure-security-center"></a>Supervisión y procesamiento de eventos de seguridad en Azure Security Center
El panel Eventos proporciona información general sobre el número de eventos de seguridad recopilados a lo largo del tiempo y una lista de eventos importantes que requieren su atención.  

> [!NOTE]
> Para usar esta característica, el área de trabajo debe ejecutar la versión 2 de Log Analytics y tener el nivel Estándar de Security Center. Para más información sobre el nivel Estándar, vea la [página de precios](security-center-pricing.md) de Security Center.
>
>

## <a name="what-is-a-security-event"></a>¿Qué es un evento de seguridad?
Security Center usa Microsoft Monitoring Agent para recopilar distintos eventos y configuraciones relacionados con la seguridad en los equipos y almacena esos eventos en los espacios de trabajo. Algunos ejemplos de estos datos son: registros del sistema operativo (registros de eventos de Windows), procesos en ejecución y eventos de soluciones de seguridad integrados con Security Center. Microsoft Monitoring Agent también copia los archivos de volcado de memoria en las áreas de trabajo.

## <a name="events-processed-dashboard"></a>Panel de eventos procesados
Puede tener acceso al panel **Eventos** desde el menú principal de Security Center o desde la hoja **Introducción** de Security Center.  

![Panel de eventos procesados][1]

El icono **Eventos** en **Security Center** muestra el número de eventos que fluyen hacia Security Center desde las máquinas virtuales de Azure y los equipos que no son de Azure.

En el **panel Eventos** se proporciona información general sobre el número de eventos procesados con el tiempo y una lista de eventos.

 ![Panel][2]

 En la mitad superior del panel se muestran todos los eventos procesados en la última semana, mientras que en la mitad inferior se enumeran los eventos importantes y todos los eventos por tipo:

 - En la sección de **eventos importantes** aparecen las consultas de evento que Security Center proporciona y las consultas de evento que el usuario crea y agrega. En este panel también encontrará una vista rápida de cada evento importante.
 - En la sección de **todos los eventos por tipo** se muestran los tipos de evento que se reciben y un recuento de cada tipo. Ejemplos de tipos de evento son SecurityEvent, CommonSecurityLog, WindowsFirewall y W3CIISLog.

> [!NOTE]
> Los eventos importantes incluyen [valoración de base de referencia web](https://docs.microsoft.com/azure/operations-management-suite/oms-security-web-baseline-assessment). El objetivo de la evaluación de base de referencia web es encontrar configuraciones del servidor web potencialmente vulnerables.

## <a name="view-processed-event-details"></a>Ver detalles de los eventos procesados
1. En el menú principal de **Security Center**, seleccione **Eventos**.
2. Puede que se abra el selector de áreas de trabajo del panel **Eventos**. Si solo tiene un área de trabajo, este selector de área de trabajo no aparecerá. Si tiene más de un área de trabajo, debe seleccionar una para ver los detalles de eventos procesados correspondientes. Si tiene más de un área de trabajo, seleccione un área de trabajo de la lista.

  ![Lista de áreas de trabajo][3]

3. El panel **Eventos** se abre y muestra detalles de los eventos del área de trabajo seleccionada. Puede ver los eventos importantes y todos los eventos por tipo.  En este ejemplo, se seleccionan los **eventos importantes**.

  ![Evento importante][4]

4. Puede consultar más datos en el área de trabajo; para ello, seleccione un tipo de evento. En este ejemplo, se selecciona **SecurityEvent**.

  ![Seleccionar un tipo de evento][5]

5. La **búsqueda de registros** se abre con más detalles sobre el tipo de evento.

  ![Búsqueda de registros][6]

## <a name="add-a-notable-event"></a>Agregar un evento importante
Security Center proporciona eventos importantes listos para usar. Puede agregar eventos importantes basados en su propia consulta con el [lenguaje de consulta de Log Analytics](../log-analytics/log-analytics-search-reference.md). Volvamos al panel **Eventos** para agregar un evento importante.

1. Seleccione **Add Notable Event** (Agregar evento importante).

  ![Agregar un evento importante][7]

2. Se abre el cuadro de diálogo **Add custom notable event** (Agregar evento importante personalizado).  En **Nombre para mostrar**, escriba el nombre del evento importante. En **Consulta de búsqueda**, escriba la consulta relativa el evento.

  ![Escribir la consulta][8]

4. Seleccione **Aceptar**.

## <a name="update-your-workspace-for-events-processing"></a>Actualizar el área de trabajo para el procesamiento de eventos
Para poder usar el procesamiento de eventos en Security Center, el área de trabajo debe ejecutar la versión 2 de Log Analytics y tener el nivel Estándar de Security Center. El selector de áreas de trabajo del panel **Eventos** identifica las áreas de trabajo que no reúnen estos requisitos.

![El área de trabajo no cumple los requisitos][9]

Si la fila del área de trabajo:

- Contiene **REQUIRES UPDATE**, debe actualizar el área de trabajo a la versión 2 de Log Analytics.
- Contiene **UPGRADE PLAN**, debe actualizar el área de trabajo al nivel Estándar de Security Center.
- Está en blanco, el área de trabajo reúne los requisitos, de modo que si selecciona un área de trabajo, se le dirigirá al panel.

> [!NOTE]
> En el panel **Eventos**, la columna **EVENTOS** señala la cantidad de eventos que hay en cada área de trabajo.  Esta columna aparece en blanco en algunas áreas de trabajo porque esas áreas de trabajo tienen aplicado el nivel Gratis de Security Center. En el nivel Gratis, Security Center recopilará eventos, pero estos no se guardarán en Log Analytics ni estarán disponibles en el panel.
>
>

## <a name="update-workspace-to-log-analytics-version-2"></a>Actualizar un área de trabajo a la versión 2 de Log Analytics
1. Seleccione un área de trabajo marcada con **REQUIRES UPDATE**.
2. Se abre **Buscar actualización**. Seleccione **Actualizar ahora**.

  ![Actualizar ahora][10]

## <a name="upgrade-to-security-centers-standard-tier"></a>Actualizar al nivel Estándar de Security Center
1. Seleccione un área de trabajo marcada con **UPGRADE PLAN**.
2. Se abre el **panel Eventos**. Seleccione **Probar el panel Eventos**.

  ![Probar panel][11]

3. En **Incorporación a la seguridad avanzada**, seleccione el área de trabajo que quiera actualizar.
4. En **Precios**, seleccione **Estándar**.
5. Seleccione **Guardar**.

  ![Actualizar al nivel Estándar][12]

## <a name="next-steps"></a>Pasos siguientes
En este artículo, ha aprendido a usar el panel Eventos de Security Center. Para más información sobre cómo funciona el panel y escribir sus propias consultas de eventos, vea:

- [¿Qué es Log Analytics?](../log-analytics/log-analytics-overview.md) - Introducción a Log Analytics
- [Descripción de las búsquedas de registros en Log Analytics:](../log-analytics/log-analytics-log-search-new.md) en este artículo se describe cómo se usan las búsquedas de registros en Log Analytics y se proporcionan los conceptos que debe comprender antes de crear una.
- [Referencia sobre búsqueda de registros de Log Analytics:](../log-analytics/log-analytics-search-reference.md) obtenga información sobre cómo escribir sus propias consultas de eventos con el lenguaje de consulta de Log Analytics.

Para más información sobre Security Center, vea:

- [Introducción a Security Center](security-center-intro.md): aquí se describen las principales funciones de Security Center.

<!--Image references-->
[1]: ./media/security-center-events-dashboard/events-processed.png
[2]: ./media/security-center-events-dashboard/dashboard.png
[3]: ./media/security-center-events-dashboard/view-processed-event.png
[4]: ./media/security-center-events-dashboard/notable-event.png
[5]: ./media/security-center-events-dashboard/events-by-type.png
[6]: ./media/security-center-events-dashboard/log-search-detail.png
[7]: ./media/security-center-events-dashboard/add-notable-event.png
[8]: ./media/security-center-events-dashboard/create-query.png
[9]: ./media/security-center-events-dashboard/requires-update.png
[10]: ./media/security-center-events-dashboard/search-upgrade.png
[11]: ./media/security-center-events-dashboard/try-dashboard.png
[12]: ./media/security-center-events-dashboard/onboard-workspace.png
