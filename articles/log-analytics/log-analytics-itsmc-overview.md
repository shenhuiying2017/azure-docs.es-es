---
title: IT Service Management Connector en Azure Log Analytics | Microsoft Docs
description: "En este artículo se proporciona información general sobre el Conector de Administración de servicios de TI (ITSMC) e información sobre cómo usar esta solución para supervisar y administrar de forma centralizada los elementos de trabajo de ITSM en Azure Log Analytics y resolver rápidamente cualquier problema."
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
ms.date: 01/23/2018
ms.author: v-jysur
ms.openlocfilehash: d586ee1b96b34d6ca83e1ffd76aee38e79bdd727
ms.sourcegitcommit: 28178ca0364e498318e2630f51ba6158e4a09a89
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/24/2018
---
# <a name="connect-azure-to-itsm-tools-using-it-service-management-connector"></a>Conectar Azure a las herramientas de ITSM mediante el Conector de Administración de servicios de TI

![Símbolo de IT Service Management Connector](./media/log-analytics-itsmc/itsmc-symbol.png)

El Conector de Administración de servicios de TI (ITSMC) le permite conectar Azure y un producto o servicio de Administración de servicios de TI (ITSM) compatibles.

Servicios de Azure como Log Analytics y Azure Monitor proporcionan herramientas para detectar, analizar y solucionar problemas relacionados con los recursos de Azure y de otros proveedores. Sin embargo, los elementos de trabajo relacionados con un problema suelen residir en un producto o servicio de ITSM. El conector de ITSM proporciona una conexión bidireccional entre las herramientas de Azure e ITSM para ayudarle a resolver problemas con mayor rapidez.

ITSMC admite conexiones con las siguientes herramientas ITSM:

-   ServiceNow
-   System Center Service Manager
-   Provance
-   Cherwell

Con ITSMC, puede:

-  Crear elementos de trabajo en la herramienta de ITSM, en función de las alertas de Azure (alertas de métricas, de Activity Log y de Log Analytics).
-  Sincronizar de forma opcional los datos de incidentes y solicitudes de cambio de la herramienta ITSM con un área de trabajo de Azure Log Analytics.


Puede empezar a usar el conector de ITSM siguiendo estos pasos:

1.  [Agregue la solución del conector de ITSM](#adding-the-it-service-management-connector-solution).
2.  [Cree una conexión de ITSM](#creating-an-itsm-connection).
3.  [Use la conexión](#using-the-solution).


##  <a name="adding-the-it-service-management-connector-solution"></a>Agregar la solución IT Service Management Connector

Para poder crear una conexión, debe agregar la solución del conector de ITSM.

1.  En Azure Portal, haga clic en el icono **+ Nuevo**.

    ![Nuevo recurso de Azure](./media/log-analytics-itsmc/azure-add-new-resource.png)

2.  Busque el **Conector de Administración de servicios de TI** en Marketplace y haga clic en **Crear**.

    ![Agregar la solución ITSMC](./media/log-analytics-itsmc/add-itsmc-solution.png)

3.  En la sección **Área de trabajo de OMS**, seleccione el área de trabajo de Azure Log Analytics donde quiera instalar la solución.
4.  En la sección **Configuración del área de trabajo de OMS**, seleccione el grupo de recursos donde quiera crear el recurso de la solución.

    ![Área de trabajo de ITSMC](./media/log-analytics-itsmc/itsmc-solution-workspace.png)

5.  Haga clic en **Create**(Crear).

Cuando se implemente el recurso de la solución, aparecerá una notificación en la parte superior derecha de la ventana.


## <a name="creating-an-itsm--connection"></a>Crear una conexión de ITSM

Después de instalar la solución, puede crear una conexión.

Para crear una conexión, debe preparar la herramienta de ITSM para permitir la conexión a la solución del conector de ITSM.  

Dependiendo del producto ITSM que se vaya a conectar, siga estos pasos:

- [System Center Service Manager (SCSM)](log-analytics-itsmc-connections.md#connect-system-center-service-manager-to-it-service-management-connector-in-azure)
- [ServiceNow](log-analytics-itsmc-connections.md#connect-servicenow-to-it-service-management-connector-in-azure)
- [Provance](log-analytics-itsmc-connections.md#connect-provance-to-it-service-management-connector-in-azure)  
- [Cherwell](log-analytics-itsmc-connections.md#connect-cherwell-to-it-service-management-connector-in-azure)

Una vez que haya preparado sus herramientas ITSM, siga estos pasos para crear una conexión:

1.  Vaya a **Todos los recursos** y busque **ServiceDesk(YourWorkspaceName)**.
2.  En la opción **ORÍGENES DE DATOS DEL ÁREA DE TRABAJO** del panel izquierdo, haga clic en **Conexiones de ITSM** .
    ![Conexiones de ITSM](./media/log-analytics-itsmc/itsm-connections.png)

    En esta página se muestra la lista de conexiones.
3.  Haga clic en **Agregar conexión**.

    ![Agregar una conexión de ITSM](./media/log-analytics-itsmc/add-new-itsm-connection.png)

4.  Especifique la configuración de conexión tal como se describe en el artículo [Configuring the ITSMC connection with your ITSM products/services](log-analytics-itsmc-connections.md) (Configurar la conexión ITSMC con los productos o servicios de ITSM).

    > [!NOTE]

    > De forma predeterminada, ITSMC actualiza los datos de configuración de la conexión una vez cada 24 horas. Para actualizar los datos de la conexión al instante para cualquier modificación o actualizaciones de plantilla que se realicen, haga clic en el botón de actualización que se muestra junto a la conexión.

    ![Actualizar la conexión](./media/log-analytics-itsmc/itsmc-connections-refresh.png)


## <a name="using-the-solution"></a>Uso de la solución
   Al usar la solución del Conector ITSM, puede crear elementos de trabajo de alertas de Azure, alertas de Log Analytics y entradas de registros de Log Analytics.

## <a name="create-itsm-work-items-from-azure-alerts"></a>Creación de elementos de trabajo de ITSM a partir de alertas de Azure

Una vez que haya creado la conexión a ITSM, puede crear elementos de trabajo en la herramienta de ITSM según las alertas de Azure, mediante la **acción de ITSM** en **Grupos de acciones**.

Los grupos de acciones proporcionan una manera modular y reutilizable de desencadenar acciones para las alertas de Azure. Puede usar los grupos de acciones con alertas de métricas, de Activity Log y de Azure Log Analytics en Azure Portal.

Utilice el siguiente procedimiento:

1. En Azure Portal, haga clic en **Monitor**
2. En el panel izquierdo, haga clic en **Grupos de acciones**. Aparece la ventana **Agregar grupo de acciones**.

    ![Grupos de acciones](media/log-analytics-itsmc/action-groups.png)

3. Complete los campos **Name** (Nombre) y **ShortName** (Nombre corto) para el grupo de acciones. Seleccione el **grupo de recursos** y la **suscripción** donde quiere crear el grupo de acciones.

    ![Detalle de los grupos de acciones](media/log-analytics-itsmc/action-groups-details.png)

4. En la lista de acciones, seleccione **ITSM** en la lista desplegable **Tipo de acción**. Proporcione un **nombre** para la acción y haga clic en **Editar detalles**.
5. En **Subscription** (Suscripción), indique dónde se encuentra el área de trabajo de Log Analytics. Seleccione el nombre de la **conexión** (el nombre del conector ITSM) seguido del nombre del área de trabajo. Por ejemplo, "MiITSMMConnector(MiAreaDeTrabajo)".

    ![Detalles de acción de ITSM](./media/log-analytics-itsmc/itsm-action-details.png)

6. Seleccione el tipo **Elemento de trabajo** en el menú desplegable.
   Elija usar una plantilla existente o rellene los campos requeridos para el producto ITSM.
7. Haga clic en **OK**.

Al crear o editar una regla de alerta de Azure, use un grupo de acciones, que tiene una acción de ITSM. Cuando se desencadena la alerta, se crea o actualiza un elemento de trabajo en la herramienta ITSM.

>[!NOTE]

> Para obtener información sobre los precios de las acciones de ITSM, consulte la [página de precios](https://azure.microsoft.com/pricing/details/monitor/) de los grupos de acciones.


## <a name="create-itsm-work-items-from-log-analytics-alerts"></a>Crear elementos de trabajo de ITSM para alertas de Log Analytics

Para configurar las reglas de alertas en el portal de Azure Log Analytics para crear elementos de trabajo en la herramienta de ITSM, siga el procedimiento siguiente.

1. En la ventana **Búsqueda de registros**, ejecute una consulta de búsqueda de registros para ver los datos. Los resultados de la consulta son el origen de los elementos de trabajo.
2. En **Búsqueda de registros**, haga clic en **Alerta** para abrir la página **Agregar regla de alerta**.

    ![Pantalla de Log Analytics](./media/log-analytics-itsmc/itsmc-work-items-for-azure-alerts.png)

3. En la ventana **Agregar regla de alerta**, proporcione los detalles necesarios para **Nombre**, **Gravedad**, **Consulta de búsqueda** y **Criterios de alerta** (Ventana de tiempo/unidad métrica).
4. Seleccione **Sí** en **Acciones de ITSM**.
5. Seleccione la conexión de ITSM en la lista **Conexión seleccionada**.
6. Proporcione los detalles requeridos.
7. Para crear un elemento de trabajo independiente para cada entrada de registro de esta alerta, active la casilla **Crear elementos de trabajo individuales para cada entrada de registro**.

    o

    deje desactivada esta casilla para crear solo un elemento de trabajo para cualquier número de entradas de registro en esta alerta.

7. Haga clic en **Save**(Guardar).

Puede ver la alerta de Log Analytics que creó en **Configuración > Alertas**. Los elementos de trabajo de la conexión de ITSM correspondientes se crean cuando se cumple la condición de la alerta especificada.


## <a name="create-itsm-work-items-from-log-analytics-log-records"></a>Crear elementos de trabajo de ITSM a partir de los registros de Log Analytics

También puede crear elementos de trabajo en los orígenes de ITSM conectados directamente desde una entrada de registro. Esto puede utilizarse para probar si la conexión funciona correctamente.


1. En **Búsqueda de registros**, busque los datos requeridos, seleccione el detalle y haga clic en **Crear elemento de trabajo**.

    Aparece la ventana **Crear elemento de trabajo de ITSM**:

    ![Pantalla de Log Analytics](media/log-analytics-itsmc/itsmc-work-items-from-azure-logs.png)

2.   Agregue los detalles siguientes:

  - **Título de elemento de trabajo**: el título del elemento de trabajo.
  - **Descripción de elemento de trabajo**: la descripción del nuevo elemento de trabajo.
  - **Equipo afectado**: el nombre del equipo en que se encontraron estos datos de registro.
  - **Conexión seleccionada**: la conexión ITSM en la que desea crear este elemento de trabajo.
  - **Elemento de trabajo**: tipo de elemento de trabajo.

3. Para usar una plantilla de elemento de trabajo existente para un incidente, haga clic en **Sí** en la opción **Generar elemento de trabajo basado en plantilla** y, luego, haga clic en **Crear**.

    O bien,

    Haga clic en **No** si desea proporcionar valores personalizados.

4. Proporcione los valores adecuados en los cuadros de texto **Tipo de contacto**, **Impacto**, **Urgencia**, **Categoría** y **Subcategoría** y, luego, haga clic en **Crear**.


##<a name="visualize-and-analyze-the-incident-and-change-request-data"></a>Visualizar y analizar los datos de incidentes y solicitudes de cambios

Si se tienen en cuenta las opciones de configuración de una conexión, el Conector ITSM puede sincronizar hasta 120 días de datos referentes a incidentes y a solicitudes cambios. El esquema de registros de estos datos se proporciona en la [próxima sección](#additional-information).

Gracias al panel del Conector ITSM en la solución, se pueden visualizar los datos de incidentes y solicitudes de cambios.

![Pantalla de Log Analytics](./media/log-analytics-itsmc/itsmc-overview-sample-log-analytics.png)

Asimismo, el panel también proporciona información acerca del estado del conector que se puede usar como punto de partida para analizar cualquier problema con las conexiones.

También puede visualizar los incidentes que se sincronizan con los equipos afectados dentro de la solución Service Map.

Mapa de servicio detecta automáticamente los componentes de la aplicación en sistemas Windows y Linux y asigna la comunicación entre servicios. Permite ver los servidores a medida que piensa en ellos, como los sistemas interconectados que ofrecen servicios críticos. Mapa de servicio muestra las conexiones entre servidores, procesos y puertos en cualquier arquitectura conectada TCP sin una configuración necesaria que sea distinta a la instalación de un agente. [Más información](../operations-management-suite/operations-management-suite-service-map.md).

Si usa la solución Service Map, puede ver los elementos de la consola de servicio creados en la solución ITSM tal como se muestra en el ejemplo siguiente:

![Pantalla de Log Analytics](./media/log-analytics-itsmc/itsmc-overview-integrated-solutions.png)

Obtenga más información en: [Service Map](../operations-management-suite/operations-management-suite-service-map.md).


## <a name="additional-information"></a>Información adicional

### <a name="data-synced-from-itsm-product"></a>Datos que se sincronizan del producto ITSM
Los incidentes y las solicitudes de cambio se sincronizan desde el producto ITSM con el área de trabajo de Log Analytics según la configuración de la conexión.

En la siguiente información se muestran ejemplos de datos recopilados por ITSMC:

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
- DESCRIPCIÓN
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
- type
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
- DESCRIPCIÓN
- Equipo

## <a name="output-data-for-a-servicenow-incident"></a>Datos de salida para un incidente de ServiceNow

| Campo de Log Analytics | Campo de ServiceNow |
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

| Log Analytics | Campo de ServiceNow |
|:--- |:--- |
| ServiceDeskId_s| Number |
| CreatedBy_s | Solicitado por |
| ClosedBy_s | Cerrado por |
| AssignedTo_s | Asignado a  |
| Title_s|  Descripción breve |
| Type_s|  type |
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
| Description_s | DESCRIPCIÓN |
| Equipo  | Elemento de configuración |


## <a name="troubleshoot-itsm-connections"></a>Solución de problemas de conexión de ITSM
1.  Si se produce un error en la conexión de la UI del origen conectado y recibe el mensaje **Error al guardar la conexión**, siga estos pasos:
- En el caso de conexiones de ServiceNow, Cherwell y Provance,  
       - Asegúrese de que ha introducido correctamente el nombre de usuario, la contraseña, el identificador de cliente y el secreto de cliente de cada una de las conexiones.  
       Compruebe si dispone de privilegios suficientes en el producto ITSM correspondiente para realizar la conexión.  
- En el caso de conexiones de Service Manager,  
       - Asegúrese de que la aplicación web se implementa correctamente y se crea la conexión híbrida. Para comprobar que la conexión se ha establecido correctamente con el equipo de Service Manager local, visite la dirección URL de la aplicación web como se detalla en la documentación para realizar la [conexión híbrida](log-analytics-itsmc-connections.md#configure-the-hybrid-connection).  

2.  Si los datos de ServiceNow no se sincronizan con Log Analytics, asegúrese de que la instancia de ServiceNow no esté suspendida. En algunas ocasiones, las instancias de desarrollo de ServiceNow se suspenden si están inactivas durante mucho tiempo. En caso contrario, notifique el problema.
3.  Si se generan alertas de OMS, pero no se crean elementos de trabajo en el producto de ITSM, no se crean elementos de configuración o no se vinculan a elementos de trabajo, o, simplemente, quiere más información general, consulte:
 -  ITSMC: La solución muestra un resumen de conexiones, elementos de trabajo, equipos, etc. Haga clic en el icono que muestra **Estado del conector**, que le lleva a **Búsqueda de registros** con la consulta correspondiente. Para obtener más información, consulte las entradas del registro con LogType_S como ERROR para obtener más información.
 - Página **Búsqueda de registros**: vea directamente los errores y la información relacionada mediante la consulta *Type=ServiceDeskLog_CL*.

## <a name="troubleshoot-service-manager-web-app-deployment"></a>Solucionar problemas de implementación de aplicaciones web de Service Manager
1.  En caso de que se produzcan problemas con la implementación de la aplicación web, asegúrese de tener los permisos suficientes en la suscripción mencionada para crear o implementar recursos.
2.  Si aparece el mensaje de error **Referencia a objeto no establecida como instancia de un objeto"** al ejecutar el [script](log-analytics-itsmc-service-manager-script.md), asegúrese de que especificó valores válidos en la sección **Configuración de usuario**.
3.  Si no puede crear el espacio de nombres de retransmisión de Service Bus, asegúrese de que el proveedor de recursos necesario está registrado en la suscripción. Si no está registrado, cree el espacio de nombres de Bus Relay manualmente desde Azure Portal. También puede crearlo mientras [crea la conexión híbrida](log-analytics-itsmc-connections.md#configure-the-hybrid-connection) desde Azure Portal.


## <a name="contact-us"></a>Ponerse en contacto con nosotros

Si tiene consultas o comentarios sobre IT Service Management, póngase en contacto con nosotros en [omsitsmfeedback@microsoft.com](mailto:omsitsmfeedback@microsoft.com).

## <a name="next-steps"></a>pasos siguientes
[Incorporación de productos o servicios de ITSM a IT Service Management Connector](log-analytics-itsmc-connections.md).
