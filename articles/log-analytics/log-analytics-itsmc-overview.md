---
title: IT Service Management Connector en OMS | Microsoft Docs
description: "Use IT Service Manager Connector para supervisar y administrar de manera centralizada los elementos de trabajo ITSM en OMS y solucionar rápidamente cualquier problema."
services: log-analytics
documentationcenter: 
author: JYOTHIRMAISURI
manager: riyazp
editor: 
ms.assetid: 0b1414d9-b0a7-4e4e-a652-d3a6ff1118c4
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/27/2017
ms.author: v-jysur
ms.translationtype: Human Translation
ms.sourcegitcommit: 54b5b8d0040dc30651a98b3f0d02f5374bf2f873
ms.openlocfilehash: f8e668f89eb1e508c61fec6565e93667d3d0478e
ms.contentlocale: es-es
ms.lasthandoff: 04/28/2017


---
# <a name="centrally-manage-itsm-work-items-using-it-service-management-connector-preview"></a>Administración centralizada de los elementos de trabajo ITSM con IT Service Management Connector (versión preliminar)

Puede usar IT Service Management Connector (ITSMC) en Log Analytics de OMS para supervisar y administrar de manera centralizada elementos de trabajo en todos los productos o servicios de ITSM.

IT Service Management Connector integra los productos y servicios existentes de IT Service Management (ITSM) con Log Analytics de OMS.  La solución tiene una integración bidireccional con productos o servicios de ITSM, donde proporciona a los usuarios de OMS una opción para crear incidentes, alertas o eventos en la solución ITSM. El conector también importa datos como incidentes y solicitudes de cambio desde la solución ITSM a Log Analytics de OMS.

Con IT Service Management Connector, puede:

  - Supervisar y administrar de manera centralizada elementos de trabajo para productos y servicios de ITSM que se usan en toda la organización.
  - Crear elementos de trabajo de ITSM (como alertas, eventos, incidentes) en ITSM desde alertas de OMS y a través de la búsqueda de registros.
  - Leer incidentes y solicitudes de cambio en la solución ITSM y correlacionarlos con los datos de registro correspondientes en el área de trabajo de Log Analytics.
  - Encontrar eventos inesperados e inusuales y resolverlos, incluso antes de que los usuarios finales llamen al departamento de soporte técnico para informarlos.
  - Importar datos de elementos de trabajo a Log Analytics y crear informes de indicador de rendimiento clave (KPI).  Con estos informes, puede identificar y evaluar varios elementos importantes, como la evaluación de malware, y actuar en consecuencia.
  - Ver paneles protegidos para obtener información más detallada sobre incidentes, solicitudes de cambio y sistemas afectados.
  - Solucionar problemas más rápidamente a través de la correlación con otras soluciones de administración en el área de trabajo de Log Analytics.


## <a name="prerequisites"></a>Requisitos previos

Para importar los elementos de trabajo de ITSM a Log Analytics de OMS, la solución requiere una conexión entre IT Service Management Connector en OMS y el producto o servicio de ITSM desde el cual importa los elementos de trabajo.


## <a name="configuration"></a>Configuración

Agregue la solución IT Service Management Connector al área de trabajo de OMS a través del proceso descrito en [Incorporación de soluciones de Log Analytics desde la galería de soluciones](log-analytics-add-solutions.md).

Icono de IT Service Management Connector como aparece en la galería de soluciones:

![icono de conector](./media/log-analytics-itsmc/itsmc-solutions-tile.png)

Después de agregarlo de manera correcta, podrá ver IT Service Management Connector en **OMS** > **Configuración** > **Orígenes conectados.**

![ITSMC conectado](./media/log-analytics-itsmc/itsmc-overview-solution-in-connected-sources.png)

## <a name="management-packs"></a>Módulos de administración
Esta solución no necesita ningún módulo de administración.

## <a name="connected-sources"></a>Orígenes conectados

IT Service Management Connector admite los siguientes productos o servicios de ITSM:

- [System Center Service Manager (SCSM)](log-analytics-itsmc-connections.md#connect-system-center-service-manager-to-it-service-management-connector-in-oms)

- [ServiceNow](log-analytics-itsmc-connections.md#connect-servicenow-to-it-service-management-connector-in-oms)

- [Provance](log-analytics-itsmc-connections.md#connect-provance-to-it-service-management-connector-in-oms)  

- [Cherwell](log-analytics-itsmc-connections.md#connect-cherwell-to-it-service-management-connector-in-oms)

## <a name="using-the-solution"></a>Uso de la solución

Una vez que conecta IT Service Management Connector de OMS al servicio de ITSM, los servicios de Log Analytics comienzan a recopilar los datos de los productos o servicios de ITSM conectados.

> [!NOTE]
> - Los datos que importa la solución IT Service Management Connector aparecen en Log Analytics como eventos denominados **ServiceDesk_CL**.
- Los eventos contienen un campo llamado **ServiceDeskWorkItemType_s**. que puede tomar su valor como incidente o como solicitud de cambio, según los datos de elemento de trabajo contenidos en el evento **ServiceDesk_CL**.

La información siguiente muestra ejemplos de los datos que recopila IT Service Management Connector:

> [!NOTE]
> En función del tipo de elemento de trabajo que se importa a Log Analytics, **ServiceDesk_CL** contiene los campos siguientes:

**Elemento de trabajo:** **incidentes**  
ServiceDeskWorkItemType_s="Incidente"

**Fields**

- ServiceDeskConnectionName
- Service Desk ID
- Estado
- Urgencia
- Impacto
- Prioridad
- Escalado
- Creado por
- Resuelto por
- Cerrado por
- Origen
- Asignado a
- Categoría
- Título
- Descripción
- Fecha de creación
- Fecha de cierre
- Fecha de resolución
- Fecha de última modificación
- Equipo


**Elemento de trabajo:** **solicitudes de cambio**

ServiceDeskWorkItemType_s="ChangeRequest"

**Fields**
- ServiceDeskConnectionName
- Service Desk ID
- Creado por
- Cerrado por
- Origen
- Asignado a
- Título
- Tipo
- Categoría
- Estado
- Escalado
- Estado del conflicto
- Urgencia
- Prioridad
- Riesgo
- Impacto
- Asignado a
- Fecha de creación
- Fecha de cierre
- Fecha de última modificación
- Fecha de solicitud
- Fecha de inicio planeada
- Fecha de finalización planeada
- Fecha de inicio del trabajo
- Fecha de finalización del trabajo
- Descripción
- Equipo

Pantalla de Log Analytics de ejemplo para datos de ITSM:

![Pantalla de Log Analytics](./media/log-analytics-itsmc/itsmc-overview-sample-log-analytics.png)

## <a name="it-service-management-connector--integration-with-other-oms-solutions"></a>IT Service Management Connector: integración con otras soluciones de OMS

IT Service Management Connector admite actualmente la integración con la solución Mapa de servicio.

Mapa de servicio detecta automáticamente los componentes de la aplicación en sistemas Windows y Linux y asigna la comunicación entre servicios. Permite ver los servidores a medida que piensa en ellos, como los sistemas interconectados que ofrecen servicios críticos. Mapa de servicio muestra las conexiones entre servidores, procesos y puertos en cualquier arquitectura conectada TCP sin una configuración necesaria que sea distinta a la instalación de un agente. Más información: [Mapa de servicio](../operations-management-suite/operations-management-suite-service-map.md).

Con esta integración, puede ver los elementos del departamento de servicios creados en la solución ITSM como se muestra en el ejemplo siguiente:

![Solución integrada ](./media/log-analytics-itsmc/itsmc-overview-integrated-solutions.png)
## <a name="create-itsm-work-items-for-oms-alerts"></a>Creación de elementos de trabajo de ITSM para alertas de OMS

Desde las alertas de OMS, puede crear elementos de trabajo asociados en los orígenes conectados de ITSM.  Para hacerlo, use el procedimiento siguiente:

1. En la ventana **Búsqueda de registros**, ejecute una consulta de búsqueda de registros para ver los datos. Los resultados de la consulta son el origen de los elementos de trabajo.
2. En **Búsqueda de registros**, haga clic en **Alerta** para abrir la página **Agregar regla de alerta**.

    ![Pantalla de Log Analytics](./media/log-analytics-itsmc/itsmc-work-items-for-oms-alerts.png)

3. En la ventana **Agregar regla de alerta**, proporcione los detalles necesarios para **Nombre**, **Gravedad**, **Consulta de búsqueda** y **Criterios de alerta** (Ventana de tiempo/unidad métrica).
4. Seleccione **Sí** en **Acciones de ITSM**.
5. Seleccione la conexión de ITSM en la lista **Conexión seleccionada**.
6. Proporcione los detalles requeridos.
7. Para crear un elemento de trabajo independiente para cada entrada de registro de esta alerta, active la casilla **Crear elementos de trabajo individuales para cada entrada de registro**.

    o

    deje desactivada esta casilla para crear solo un elemento de trabajo para cualquier número de entradas de registro en esta alerta.

7. Haga clic en **Guardar**.

La alerta de OMS se creará en **Alertas**. Los elementos de trabajo de la conexión de ITSM correspondientes se crean cuando se cumple la condición de la alerta especificada.

## <a name="create-itsm-work-items-from-oms-logs"></a>Creación de elementos de trabajo de ITSM desde registros de OMS

Puede crear elementos de trabajo en los orígenes conectados de ITSM a través de Búsqueda de registros de OMS. Para hacerlo, use el procedimiento siguiente:

1. En **Búsqueda de registros**, busque los datos requeridos, seleccione el detalle y haga clic en **Crear elemento de trabajo**.

    Aparecerá la ventana **Creación de elemento de trabajo de ITSM**:

    ![Pantalla de Log Analytics](media/log-analytics-itsmc/itsmc-work-items-from-oms-logs.png)

2.   Agregue los detalles siguientes:

  - **Título de elemento de trabajo**: el título del elemento de trabajo.
  - **Descripción de elemento de trabajo**: la descripción del nuevo elemento de trabajo.
  - **Equipo afectado**: el nombre del equipo en que se encontraron estos datos de registro.
  - **Conexión seleccionada**: la conexión ITSM en la que desea crear este elemento de trabajo.
  - **Elemento de trabajo**: tipo de elemento de trabajo.

3. Para usar una plantilla de elemento de trabajo existente para un incidente, haga clic en **Sí** en la opción **Generate work item based on the template** (Generar elemento de trabajo basado en la plantilla) y, luego, haga clic en **Crear**.

    O bien,

    Haga clic en **No** si desea proporcionar valores personalizados.

4. Proporcione los valores adecuados en los cuadros de texto **Tipo de contacto**, **Impacto**, **Urgencia**, **Categoría** y **Subcategoría** y, luego, haga clic en **Crear**.

El elemento de trabajo se creará en ITSM, que también puede ver en OMS.

## <a name="contact-us"></a>Ponerse en contacto con nosotros

Si tiene consultas o comentarios sobre IT Service Management, póngase en contacto con nosotros en [omsitsmfeedback@microsoft.com](mailto:omsitsmfeedback@microsoft.com).

## <a name="next-steps"></a>Pasos siguientes
[Incorporación de productos o servicios de ITSM a IT Service Management Connector](log-analytics-itsmc-connections.md).

