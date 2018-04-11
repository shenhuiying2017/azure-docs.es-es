---
title: Conexión de Configuration Manager con Log Analytics | Microsoft Docs
description: En este artículo se muestran los pasos para conectar Configuration Manager con Log Analytics y empezar a analizar datos.
services: log-analytics
documentationcenter: ''
author: MGoedtel
manager: carmonm
editor: ''
ms.assetid: f2298bd7-18d7-4371-b24a-7f9f15f06d66
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/22/2018
ms.author: magoedte
ms.openlocfilehash: 5ff0687fe99f0853e29e5f0d814a8555c367027c
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 03/30/2018
---
# <a name="connect-configuration-manager-to-log-analytics"></a>Conexión de Configuration Manager con Log Analytics
Puede conectar el entorno de System Center Configuration Manager a Azure Log Analytics para sincronizar los datos de recopilación del dispositivo y hacer referencia a estas recopilaciones en Log Analytics y Azure Automation.  

## <a name="prerequisites"></a>requisitos previos

Log Analytics es compatible con la rama actual de System Center Configuration Manager, versión 1606 y posteriores.  

## <a name="configuration-overview"></a>Información general sobre la configuración
Los pasos siguientes son un resumen de los pasos necesarios para configurar la integración de Configuration Manager con Log Analytics.  

1. En Azure Portal, registre Configuration Manager como una aplicación web o una aplicación de API web y asegúrese de que tiene el identificador de cliente y la clave secreta de cliente en el registro de Azure Active Directory. Consulte [Uso del portal para crear una aplicación de Active Directory y una entidad de servicio con acceso a los recursos](../azure-resource-manager/resource-group-create-service-principal-portal.md) para obtener información detallada sobre cómo realizar este paso.
2. En Azure Portal, [proporcione a Configuration Manager (la aplicación web registrada) permiso para acceder a Log Analytics](#grant-configuration-manager-with-permissions-to-log-analytics).
3. En Configuration Manager, [agregue una conexión mediante el Asistente para agregar una conexión de OMS](#add-an-oms-connection-to-configuration-manager).
4. En Configuration Manager, [actualice las propiedades de conexión](#update-oms-connection-properties) si la clave secreta de cliente o la contraseña expiran o se pierden.
5. [Descargue e instale Microsoft Monitoring Agent](#download-and-install-the-agent) en el equipo que ejecuta el rol de sistema de sitio de punto de conexión del servicio de Configuration Manager. El agente envía datos de Configuration Manager al área de trabajo de Log Analytics.
6. En Log Analytics, [importe recopilaciones de Configuration Manager](#import-collections) como grupos de equipos.
7. En Log Analytics, consulte datos de Configuration Manager como [grupos de equipos](log-analytics-computer-groups.md).

Puede leer más información sobre cómo conectar Configuration Manager a OMS en [Sync data from Configuration Manager to the Microsoft Operations Management Suite](https://technet.microsoft.com/library/mt757374.aspx) (Sincronización de datos de Configuration Manager con Microsoft Operations Management Suite).

## <a name="grant-configuration-manager-with-permissions-to-log-analytics"></a>Concesión de permisos a Configuration Manager para acceder a Log Analytics
En el procedimiento siguiente, concede el rol *Colaborador* del área de trabajo de Log Analytics a la aplicación de AD y a la entidad de servicio que creó anteriormente para Configuration Manager.  Si no dispone de un área de trabajo, consulte [Creación de un área de trabajo en Azure Log Analytics](log-analytics-quick-create-workspace.md) antes de continuar.  Esto permite a Configuration Manager autenticarse y conectarse a su área de trabajo de Log Analytics.  

> [!NOTE]
> Debe especificar permisos en Log Analytics para Configuration Manager. De lo contrario, recibirá un mensaje de error cuando utilice el Asistente para configuración de Configuration Manager.
>

1. En Azure Portal, haga clic en **Todos los servicios**, en la esquina superior izquierda. En la lista de recursos, escriba **Log Analytics**. Cuando comience a escribir, la lista se filtrará en función de la entrada. Seleccione **Log Analytics**.<br><br> ![Azure Portal](media/log-analytics-quick-collect-azurevm/azure-portal-01.png)<br><br>  
2. En la lista de áreas de trabajo de Log Analytics, seleccione el área de trabajo que desea modificar.
3. En el panel izquierdo, seleccione **Access Control (IAM)**.
4. En la página de Access Control, haga clic en **Agregar** y aparecerá el panel **Agregar permisos**.
5. En el panel **Agregar permisos**, en la lista desplegable **Rol**, seleccione el rol **Colaborador**.  
6. En la lista desplegable **Asignar acceso a**, seleccione la aplicación de Configuration Manager que creó en AD anteriormente y, a continuación, haga clic en **Aceptar**.  

## <a name="download-and-install-the-agent"></a>Descarga e instalación del agente
Revise el artículo [Conexión de equipos Windows al servicio Log Analytics de Azure](log-analytics-agent-windows.md) para entender los métodos disponibles a la hora de instalar Microsoft Monitoring Agent en el equipo que hospeda el rol del sistema de sitio de punto de conexión del servicio Configuration Manager.  

## <a name="add-an-oms-connection-to-configuration-manager"></a>Adición de una conexión de OMS a Configuration Manager
Para agregar una conexión de OMS, su entorno de Configuration Manager debe tener un [punto de conexión de servicio](https://technet.microsoft.com/library/mt627781.aspx) configurado para el modo en línea.

1. En el área de trabajo **Administración** de Configuration Manager, seleccione **OMS Connector** (Conector de OMS). Se abrirá el **Asistente para agregar conexiones de OMS**. Seleccione **Next** (Siguiente).
2. En la pantalla **General**, confirme que ha realizado las siguientes acciones y que tiene los detalles de cada elemento; después, seleccione **Siguiente**.

   1. En Azure Portal, ha registrado Configuration Manager como una aplicación web o una aplicación de API web y se ha asegurado de que tiene el [identificador de cliente en el registro](../active-directory/active-directory-integrating-applications.md).
   2. En Azure Portal, ha creado una clave secreta de aplicación para la aplicación registrada en Azure Active Directory.  
   3. En Azure Portal, ha proporcionado a la aplicación web registrada permiso para acceder a OMS.  
      ![Conexión a la página general del Asistente para OMS](./media/log-analytics-sccm/sccm-console-general01.png)
3. En la pantalla **Azure Active Directory**, establezca la configuración de conexión a Log Analytics proporcionando su **inquilino**, el **identificador de cliente** y la **clave secreta de cliente**; después, seleccione **Siguiente**.  
   ![Conexión a la página de Azure Active Directory del Asistente para OMS](./media/log-analytics-sccm/sccm-wizard-tenant-filled03.png)
4. Si ha realizado todos los procedimientos correctamente, la información de la pantalla **OMS Connection Configuration** (Configuración de conexión de OMS) aparecerá automáticamente en esta página. Debe aparecer la información de la configuración de conexión para su **suscripción de Azure**, **grupo de recursos de Azure** y **área de trabajo de Operations Management Suite**.  
   ![Conexión a la página de conexión de OMS del Asistente para OMS](./media/log-analytics-sccm/sccm-wizard-configure04.png)
5. El asistente se conecta al servicio de Log Analytics usando la información que ha introducido. Seleccione las recopilaciones de dispositivos que desea sincronizar con el servicio y, después, haga clic en **Agregar**.  
   ![Selección de recopilaciones](./media/log-analytics-sccm/sccm-wizard-add-collections05.png)
6. Compruebe la configuración de conexión de la pantalla **Resumen** y, después, seleccione **Siguiente**. La pantalla **Progreso** muestra el estado de conexión, que debería ser **Completado**.

> [!NOTE]
> Debe conectar el sitio de nivel superior de la jerarquía a Log Analytics. Si conecta un sitio principal independiente a Log Analytics y, después, agrega un sitio de administración central a su entorno, tendrá que eliminar y volver a crear la conexión dentro de la nueva jerarquía.
>
>

Después de haber vinculado Configuration Manager a Log Analytics, puede agregar o quitar recopilaciones y ver las propiedades de la conexión.

## <a name="update-log-analytics-connection-properties"></a>Actualización de las propiedades de conexión de Log Analytics
Si la clave secreta de cliente o la contraseña nunca expira o se pierde, tendrá que actualizar manualmente las propiedades de conexión de Log Analytics.

1. En Configuration Manager, acceda a **Cloud Services**, después, seleccione **OMS Connector** (Conector de OMS) para abrir la página **OMS Connection Properties** (Propiedades de conexión de OMS).
2. En esta página, haga clic en la pestaña **Azure Active Directory** para ver su **inquilino**, **identificador de cliente** y **expiración de clave secreta de cliente**. **Compruebe** si ha expirado la **clave secreta de cliente**.

## <a name="import-collections"></a>Importación de recopilaciones
Después de agregar una conexión de OMS en Configuration Manager y de instalar el agente en el equipo que ejecuta la conexión al servicio de Configuration Manager, seleccione el rol de sistema de sitio. Después, tiene que importar las recopilaciones de Configuration Manager en Log Analytics como grupos de equipos.

Después de completar la configuración inicial para importar recopilaciones de dispositivos de la jerarquía, la información de pertenencia de la recopilación se recupera cada 3 horas para mantener actualizada la pertenencia. Puede deshabilitar esta opción en cualquier momento.

1. En Azure Portal, haga clic en **Todos los servicios**, en la esquina superior izquierda. En la lista de recursos, escriba **Log Analytics**. Cuando comience a escribir, la lista se filtrará en función de la entrada. Seleccione **Log Analytics**.
2. En la lista de áreas de trabajo de Log Analytics, seleccione el área de trabajo en la que se ha registrado Configuration Manager.  
3. Seleccione **Configuración avanzada**.<br><br> ![Configuración avanzada de Log Analytics](media/log-analytics-quick-collect-azurevm/log-analytics-advanced-settings-01.png)<br><br>  
4. Seleccione **Grupos de equipos** y, a continuación, **SCCM**.  
5. Seleccione **Importar pertenencias de la recopilación de Configuration Manager** y, después, haga clic en **Guardar**.  
   ![Grupos de equipos: pestaña SCCM](./media/log-analytics-sccm/sccm-computer-groups01.png)

## <a name="view-data-from-configuration-manager"></a>Visualización de datos de Configuration Manager
Después de agregar una conexión de OMS en Configuration Manager y de instalar el agente en el equipo que ejecuta el rol del sistema de punto de conexión del servicio Configuration Manager, se envían datos desde el agente a Log Analytics. En Log Analytics, las recopilaciones de Configuration Manager aparecen como [grupos de equipos](log-analytics-computer-groups.md). Puede ver los grupos de la página **Configuration Manager** en **Configuración\Grupos de equipos**.

Después de importar las recopilaciones, puede ver cuántos equipos con pertenencias a las recopilaciones se han detectado. También puede ver el número de recopilaciones que se han importado.

![Grupos de equipos: pestaña SCCM](./media/log-analytics-sccm/sccm-computer-groups02.png)

Al hacer clic en cualquiera de ellos, se abre Búsqueda, que muestra todos los grupos importados o todos los equipos que pertenecen a cada grupo. Mediante [Búsqueda de registros](log-analytics-log-searches.md), puede iniciar un análisis exhaustivo de datos de Configuration Manager.

## <a name="next-steps"></a>Pasos siguientes
* Use [Búsqueda de registros](log-analytics-log-searches.md) para ver información detallada sobre los datos de Configuration Manager.
