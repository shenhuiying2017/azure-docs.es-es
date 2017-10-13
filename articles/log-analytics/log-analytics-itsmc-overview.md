---
title: IT Service Management Connector en Azure Log Analytics | Microsoft Docs
description: "Use IT Service Manager Connector para supervisar y administrar de manera centralizada los elementos de trabajo de ITSM en Azure Log Analytics y solucionar rápidamente cualquier problema."
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
ms.date: 06/19/2017
ms.author: v-jysur
ms.openlocfilehash: 411d6103852cbf534d3c420d5ea7b2146df5164e
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="centrally-manage-itsm-work-items-using-it-service-management-connector-preview"></a>Administración centralizada de los elementos de trabajo ITSM con IT Service Management Connector (versión preliminar)

![Símbolo de IT Service Management Connector](./media/log-analytics-itsmc/itsmc-symbol.png)

IT Service Management Connector proporciona una integración bidireccional entre un producto o servicio de IT Service Management (ITSM) y Log Analytics.  A través de esta conexión, puede crear incidentes, alertas o eventos de producto ITSM a partir de alertas de Log Analytics o entradas de registro. El conector también importa datos, como incidentes y solicitudes de cambio, desde el producto ITSM a Log Analytics de OMS.

Con IT Service Management Connector, puede:

  - Integre las alertas operativas con sus prácticas de administración de incidentes en la herramienta de ITSM de su elección.
    - Crear elementos de trabajo (como alertas, eventos, incidentes) en ITSM desde alertas de OMS y a través de la búsqueda de registros.
    - Crear elementos de trabajo basados en las alertas de registro de actividad de Azure mediante una acción de ITSM en grupos de acciones. 
  
  - Unifique los datos de supervisión, registros y administración de servicios que se usan en la organización.
    - Correlacione los datos de incidentes y solicitudes de cambio de las herramientas de ITSM con los datos de registro correspondientes del área de trabajo de Log Analytics.   
    - Consulte paneles de nivel superior para obtener información general sobre incidentes, solicitudes de cambio y sistemas afectados.
    - Cree consultas de Log Analytics para obtener información sobre los datos de administración de servicios.
      
## <a name="adding-the-it-service-management-connector-solution"></a>Agregar la solución IT Service Management Connector

Agregue la solución IT Service Management Connector al área de trabajo de Log Analytics a través del proceso descrito en [Incorporación de soluciones de Log Analytics desde la galería de soluciones](log-analytics-add-solutions.md).

Icono de IT Service Management Connector como aparece en la galería de soluciones:

![icono de conector](./media/log-analytics-itsmc/itsmc-solutions-tile.png)

Después de agregarlo de manera correcta, podrá ver IT Service Management Connector en **OMS** > **Configuración** > **Orígenes conectados.**

![ITSMC conectado](./media/log-analytics-itsmc/itsmc-overview-solution-in-connected-sources.png)

> [!NOTE]

> De forma predeterminada, IT Service Management Connector actualiza los datos de la conexión una vez cada 24 horas. Para actualizar los datos de la conexión al instante para cualquier modificación o actualizaciones de plantilla que se realicen, haga clic en el botón de actualización que se muestra junto a la conexión.

 ![Actualización de ITSMC](./media/log-analytics-itsmc/itsmc-connection-refresh.png)


## <a name="configuring-the-connection-with-your-itsm-software"></a>Configurar la conexión con el software ITSM

La solución IT Service Management Connector admite conexiones a **System Center Service Manager**, **ServiceNow**, **Provance** y **Cherwell**. Puede configurar la conexión con

- [System Center Service Manager (SCSM)](log-analytics-itsmc-connections.md#connect-system-center-service-manager-to-it-service-management-connector-in-oms)

- [ServiceNow](log-analytics-itsmc-connections.md#connect-servicenow-to-it-service-management-connector-in-oms)

- [Provance](log-analytics-itsmc-connections.md#connect-provance-to-it-service-management-connector-in-oms)  

- [Cherwell](log-analytics-itsmc-connections.md#connect-cherwell-to-it-service-management-connector-in-oms)

## <a name="using-the-solution"></a>Uso de la solución

Una vez configurado IT Service Management Connector con los detalles del software ITSM, el conector iniciará la recopilación de datos procedentes del producto o servicio ITSM conectado. Aunque depende del número de incidentes y solicitudes de cambio del producto o servicio ITSM, la sincronización inicial se debería completar en unos minutos. 

> [!NOTE]
> - Los datos que importa la solución IT Service Management Connector del producto ITSM aparecen en Log Analytics como entradas de registro del tipo **ServiceDesk_CL**.
> - La entrada de registro contiene un campo denominado **ServiceDeskWorkItemType_s**, que es un incidente o una solicitud de cambio, los dos tipos de datos importados del producto ITSM

## <a name="data-synced-from-itsm-product"></a>Datos que se sincronizan del producto ITSM
Los incidentes y las solicitudes de cambio se sincronizan desde el producto ITSM con el área de trabajo de Log Analytics. La información siguiente muestra ejemplos de los datos que recopila IT Service Management Connector:

> [!NOTE]
> En función del tipo de elemento de trabajo que se importa a Log Analytics, **ServiceDesk_CL** contiene los campos siguientes:

**Elemento de trabajo:****incidentes**  
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


**Elemento de trabajo:****solicitudes de cambio**

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

## <a name="output-data-for-a-servicenow-incident"></a>Datos de salida para un incidente de ServiceNow

| Campo OMS | Campo ITSM |
|:--- |:--- |
| ServiceDeskId_s| Number |
| IncidentState_s | Estado |
| Urgency_s |Urgencia |
| Impact_s |Impacto|
| Priority_s | Prioridad |
| CreatedBy_s | Abierto por |
| ResolvedBy_s | Resuelto por|
| ClosedBy_s  | Cerrado por |
| Source_s| Tipo de contacto |
| AssignedTo_s | Asignado a  |
| Category_s | Categoría |
| Title_s|  Descripción breve |
| Description_s|  Notas |
| CreatedDate_t|  Abierto |
| ClosedDate_t| closed|
| ResolvedDate_t|Resuelto|
| Equipo  | Elemento de configuración |

## <a name="output-data-for-a-servicenow-change-request"></a>Datos de salida para una solicitud de cambio de ServiceNow

| Campo OMS | Campo ITSM |
|:--- |:--- |
| ServiceDeskId_s| Number |
| CreatedBy_s | Solicitado por |
| ClosedBy_s | Cerrado por |
| AssignedTo_s | Asignado a  |
| Title_s|  Descripción breve |
| Type_s|  Tipo |
| Category_s|  Categoría |
| CRState_s|  Estado|
| Urgency_s|  Urgencia |
| Priority_s| Prioridad|
| Risk_s| Riesgo|
| Impact_s| Impacto|
| RequestedDate_t  | Solicitado por fecha |
| ClosedDate_t | Fecha de cierre |
| PlannedStartDate_t  |     Fecha de inicio planeada |
| PlannedEndDate_t  |   Fecha de finalización planeada |
| WorkStartDate_t  | Fecha de inicio real |
| WorkEndDate_t | Fecha de finalización real|
| Description_s | Descripción |
| Equipo  | Elemento de configuración |

**Pantalla de Log Analytics de ejemplo para datos de ITSM:**

![Pantalla de Log Analytics](./media/log-analytics-itsmc/itsmc-overview-sample-log-analytics.png)

## <a name="it-service-management-connector--integration-with-other-oms-solutions"></a>IT Service Management Connector: integración con otras soluciones de OMS

IT Service Management Connector admite actualmente la integración con la solución Service Map.

Mapa de servicio detecta automáticamente los componentes de la aplicación en sistemas Windows y Linux y asigna la comunicación entre servicios. Permite ver los servidores a medida que piensa en ellos, como los sistemas interconectados que ofrecen servicios críticos. Mapa de servicio muestra las conexiones entre servidores, procesos y puertos en cualquier arquitectura conectada TCP sin una configuración necesaria que sea distinta a la instalación de un agente. Más información: [Mapa de servicio](../operations-management-suite/operations-management-suite-service-map.md).

Si también usa la solución Service Map, puede ver los elementos de la consola de servicios creados en la solución ITSM como se muestra en el ejemplo siguiente:

![Integración de ServiceMap](./media/log-analytics-itsmc/itsmc-overview-integrated-solutions.png)
## <a name="create-itsm-work-items-for-oms-alerts"></a>Creación de elementos de trabajo de ITSM para alertas de OMS

Con la solución ITSM Connector, puede configurar alertas de OMS para desencadenar la creación de elementos de trabajo en la herramienta ITSM conectada como se indica a continuación:

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

La alerta de OMS que ha creado se puede ver en **Configuración**>**Alertas**. Los elementos de trabajo de la conexión de ITSM correspondientes se crean cuando se cumple la condición de la alerta especificada.

## <a name="create-itsm-work-items-from-oms-logs"></a>Creación de elementos de trabajo de ITSM desde registros de OMS

También puede crear elementos de trabajo en los orígenes de ITSM conectados directamente desde una entrada de registro como se indica a continuación:

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

## <a name="create-itsm-work-items-from-azure-alerts"></a>Creación de elementos de trabajo de ITSM a partir de alertas de Azure
Ahora, ITSM Connector está integrado con los grupos de acciones. Los [grupos de acciones](../monitoring-and-diagnostics/monitoring-action-groups.md) proporcionan una manera modular y reutilizable de desencadenar acciones para las alertas de Azure. La acción de ITSM en grupos de acciones crea elementos de trabajo en el producto ITSM mediante una solución existente de ITSM Connector.

1. En Azure Portal, haga clic en **Monitor**
2. En el panel izquierdo, haga clic en **Action groups** (Grupos de acciones).

    ![Grupos de acciones](media/log-analytics-itsmc/ActionGroups.png)

3. Complete los campos **Name** (Nombre) y **ShortName** (Nombre corto) para el grupo de acciones. Seleccione el **grupo de recursos** y la **suscripción** en que quiere crear el grupo de acciones.

    ![Detalle de grupos de acciones](media/log-analytics-itsmc/ActionGroupsDetail.png)

4. En la lista de acciones, seleccione **ITSM** en la lista desplegable **Action Type** (Tipo de acción). Complete el campo **Name** (Nombre) para la acción y haga clic en **Edit details** (Editar detalles).


5. En **Subscription** (Suscripción), indique dónde se encuentra el área de trabajo de Log Analytics. Seleccione un valor de **Connection** (Conexión); por ejemplo, el nombre de ITSM Connector seguido del nombre del área de trabajo. Por ejemplo, "MiITSMMConnector(MiAreaDeTrabajo)".

    ![Detalles de acción de ITSM](./media/log-analytics-itsmc/ITSMActionDetails.png)

6. Seleccione el tipo **Work Item** (Elemento de trabajo) en la lista desplegable.
7. Elija usar una plantilla existente o rellene los campos requeridos para el producto ITSM.
8. Haga clic en **Aceptar**

Al crear o editar una regla de alerta de Azure, use un grupo de acciones, que tiene una acción de ITSM. Cuando se desencadena la alerta, se crea un elemento de trabajo en la herramienta ITSM. 

>[!NOTE]
>Actualmente, solo las alertas de registro de actividad admiten la acción de ITSM. Para otras alertas de Azure, esta acción no es operativa.
>


## <a name="troubleshoot-itsm-connections-in-oms"></a>Solución de problemas de conexión de ITSM en OMS
1.  Si se produce un error en la conexión de la UI del origen conectado y recibe el mensaje **Error al guardar la conexión**, siga estos pasos:
 - En el caso de conexiones de ServiceNow, Cherwell y Provance,
    - asegúrese de que ha introducido correctamente el nombre de usuario, la contraseña, el Id. de cliente y el secreto de cliente de cada una de las conexiones.
    - compruebe si dispone de privilegios suficientes en el producto ITSM correspondiente para realizar la conexión.
 - En el caso de conexiones de Service Manager,
     - asegúrese de que la aplicación web se implementa correctamente y se crea la conexión híbrida. Para comprobar que la conexión se ha establecido correctamente con el equipo de Service Manager local, visite la dirección URL de la aplicación web como se detalla en la documentación para realizar la [conexión híbrida](log-analytics-itsmc-connections.md#configure-the-hybrid-connection).
     
2.  Si los datos de ServiceNow no se sincronizan con Log Analytics, asegúrese de que la instancia de ServiceNow no esté suspendida. En algunas ocasiones, las instancias de desarrollo de ServiceNow se suspenden si están inactivas durante mucho tiempo. En caso contrario, notifique el problema.
3.  Si se generan alertas de OMS, pero no se crean elementos de trabajo en el producto de ITSM, no se crean elementos de configuración o no se vinculan a elementos de trabajo, o, simplemente, quiere más información general, consulte:
 -  **Solución IT Service Management Connector**: muestra un resumen de las conexiones, elementos de trabajo, equipos, etc. Haga clic en el icono que muestra el **Estado del conector**, que le llevará a la **Búsqueda de registros** con la consulta correspondiente. Para obtener más información, consulte las entradas del registro con LogType_S como ERROR para obtener más información.
 - También puede ver directamente los errores y la información relacionada en la página **Búsqueda de registros** mediante la consulta *Type=ServiceDeskLog_CL*.

## <a name="troubleshoot-service-manager-web-app-deployment"></a>Solucionar problemas de implementación de aplicaciones web de Service Manager
1.  Si tiene problemas con la implementación de la aplicación web, asegúrese de tener los permisos suficientes en la suscripción mencionada para crear o implementar recursos.
2.  Si aparece el mensaje de error **Referencia a objeto no establecida como instancia de un objeto"** al ejecutar el [script](log-analytics-itsmc-service-manager-script.md), asegúrese de que especificó valores válidos en la sección **Configuración de usuario**.
3.  Si no puede crear el espacio de nombres de retransmisión de Service Bus, asegúrese de que el proveedor de recursos necesario está registrado en la suscripción. Si no está registrado, cree el espacio de nombres de Bus Relay manualmente desde Azure Portal. También puede crearlo mientras [crea la conexión híbrida](log-analytics-itsmc-connections.md#configure-the-hybrid-connection) desde Azure Portal.


## <a name="contact-us"></a>Ponerse en contacto con nosotros

Si tiene consultas o comentarios sobre IT Service Management, póngase en contacto con nosotros en [omsitsmfeedback@microsoft.com](mailto:omsitsmfeedback@microsoft.com).

## <a name="next-steps"></a>Pasos siguientes
[Incorporación de productos o servicios de ITSM a IT Service Management Connector](log-analytics-itsmc-connections.md).
