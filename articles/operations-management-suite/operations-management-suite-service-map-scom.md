---
title: "Integración de Service Map con System Center Operations Manager | Microsoft Docs"
description: "Mapa de servicio es una solución de Operations Management Suite que detecta automáticamente los componentes de la aplicación en sistemas Windows y Linux y asigna la comunicación entre servicios. En este artículo se aborda el uso de Mapa de servicio para crear automáticamente diagramas de aplicaciones distribuidas en Operations Manager."
services: operations-management-suite
documentationcenter: 
author: daveirwin1
manager: jwhit
editor: tysonn
ms.assetid: e8614a5a-9cf8-4c81-8931-896d358ad2cb
ms.service: operations-management-suite
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/21/2017
ms.author: bwren;dairwin
ms.openlocfilehash: af1f683f08ff6b70b23ff265f39b9a76f92f4be2
ms.sourcegitcommit: 8aa014454fc7947f1ed54d380c63423500123b4a
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/23/2017
---
# <a name="service-map-integration-with-system-center-operations-manager"></a>Integración de Mapa de servicio con System Center Operations Manager
  > [!NOTE]
  > Esta característica está en versión preliminar pública.
  > 
  
Mapa de servicio de Operations Management Suite detecta automáticamente los componentes de la aplicación en sistemas Windows y Linux y asigna la comunicación entre los servicios. Mapa de servicio permite ver los servidores a medida que piensa en ellos, como los sistemas interconectados que ofrecen servicios críticos. Mapa de servicio muestra las conexiones entre servidores, procesos y puertos en cualquier arquitectura conectada TCP sin una configuración necesaria que sea distinta a la instalación de un agente. Para más detalles, consulte la [documentación de Mapa de servicio](operations-management-suite-service-map.md).

Con esta integración entre Mapa de servicio y System Center Operations Manager, puede crear automáticamente diagramas de aplicaciones distribuidas en Operations Manager basados en mapas de dependencia dinámica de Mapa de servicio.

## <a name="prerequisites"></a>Requisitos previos
* Un grupo de administración de Operations Manager (2012 R2 o posterior) que administra un conjunto de servidores.
* Un área de trabajo de Operations Management Suite con la solución Mapa de servicio habilitada.
* Un conjunto de servidores (al menos uno) que se administren desde Operations Manager y envíen datos a Mapa de servicio. Se admiten los servidores de Windows y Linux.
* Una entidad de servicio con acceso a la suscripción de Azure asociada al área de trabajo de Operations Management Suite. Para más información, vaya a [Create a service principal](#creating-a-service-principal) (Creación de una entidad de servicio).

## <a name="install-the-service-map-management-pack"></a>Instalación del módulo de administración de Mapa de servicio
La integración entre Operations Manager y Mapa de servicio se habilita al importar el paquete de módulos de administración de Microsoft.SystemCenter.ServiceMap (Microsoft.SystemCenter.ServiceMap.mpb). Puede descargar el paquete de módulo de administración en el [Centro de descarga de Microsoft](https://www.microsoft.com/download/details.aspx?id=55763). El paquete contiene los siguientes módulos de administración:
* Microsoft Service Map Application Views
* Microsoft System Center Service Map Internal
* Microsoft System Center Service Map Overrides
* Microsoft System Center Service Map

## <a name="configure-the-service-map-integration"></a>Configuración de la integración de Mapa de servicio
Después de instalar el módulo de administración de **Mapa de servicio**, habrá un nuevo nodo, Mapa de servicio, en **Operations Management Suite**, en el panel de **Administración**. 

Para configurar la integración de Mapa de servicio, haga lo siguiente:

1. Para abrir el asistente para configuración, en el panel de **información general de Mapa de servicio**, haga clic en **Agregar área de trabajo**.  

    ![Panel de información general de Mapa de servicio](media/oms-service-map/scom-configuration.png)

2. En la ventana **Configuración de la conexión**, escriba el identificador o el nombre del inquilino, el identificador de la aplicación (también conocido como nombre de usuario o identificador de cliente) y la contraseña de la entidad de servicio; a continuación, haga clic en **Siguiente**. Para más información, vaya a [Create a service principal](#creating-a-service-principal) (Creación de una entidad de servicio).

    ![Ventana de configuración de la conexión](media/oms-service-map/scom-config-spn.png)

3. En la ventana de **selección de suscripción**, seleccione la suscripción de Azure, el grupo de recursos de Azure (el que contiene el área de trabajo de Operations Management Suite) y el área de trabajo de Operations Management Suite; a continuación, haga clic en **Siguiente**.

    ![Área de trabajo de configuración de Operations Manager](media/oms-service-map/scom-config-workspace.png)

4. En la ventana **Selección de Grupo de máquinas**, elija los grupos de máquinas de Service Map que desea sincronizar con Operations Manager. Haga clic en **Agregar o quitar Grupos de máquinas**, seleccione los grupos de la lista de **Grupos de máquinas disponibles** y haga clic en **Agregar**.  Cuando haya terminado la selección de grupos, haga clic en **Aceptar** para finalizar.
    
    ![Configuración de grupos de máquinas de Operations Manager](media/oms-service-map/scom-config-machine-groups.png)
    
5. En la ventana **Selección de servidor**, se puede configurar el grupo de servidores de Mapa de servicio con los servidores que desea sincronizar entre Operations Manager y Mapa de servicio. Haga clic en **Agregar o quitar servidores**.   
    
    Para que la integración genere un diagrama de aplicación distribuida para un servidor, el servidor debe cumplir lo siguiente:

    * Administrado por Operations Manager
    * Administrado por Service Map
    * Aparece en el grupo de servidores de Service Map

    ![Grupo de configuración de Operations Manager](media/oms-service-map/scom-config-group.png)

6. Opcional: seleccione el grupo de recursos del servidor de administración para comunicarse con Operations Management Suite y haga clic en **Agregar área de trabajo**.

    ![Grupo de recursos de configuración de Operations Manager](media/oms-service-map/scom-config-pool.png)

    Es posible que el proceso de configuración y registro del área de trabajo de Operations Management Suite tarde un minuto. Una vez configurado, Operations Manager inicia la primera sincronización de Mapa de servicio desde Operations Management Suite.

    ![Grupo de recursos de configuración de Operations Manager](media/oms-service-map/scom-config-success.png)


## <a name="monitor-service-map"></a>Supervisión del Mapa de servicio
Después de conectar el área de trabajo de Operations Management Suite, una nueva carpeta (Mapa de servicio) aparece en el panel **Supervisión** de la consola de Operations Manager.

![Panel de supervisión de Operations Manager](media/oms-service-map/scom-monitoring.png)

La carpeta de Service Map tiene cuatro nodos:
* **Alertas activas**: muestra todas las alertas activas sobre la comunicación entre Operations Manager y Service Map.  Observe que estas alertas no son alertas de Operations Management Suite sincronizadas con Operations Manager. 

* **Servidores**: muestra los servidores supervisados que se han configurado para la sincronización con Mapa de servicio.

    ![Panel de servidores de supervisión de Operations Manager](media/oms-service-map/scom-monitoring-servers.png)

* **Vistas de dependencia de grupo de máquinas**: muestra todos los grupos de máquinas que se sincronizan desde Service Map. Puede hacer clic en cualquier grupo para ver su diagrama de aplicaciones distribuidas.

    ![Diagrama de aplicaciones distribuidas de Operations Manager](media/oms-service-map/scom-group-dad.png)

* **Vistas de la dependencia de servidor**: muestra todos los servidores que se sincronizan desde Mapa de servicio. Puede hacer clic en cualquier servidor para ver su diagrama de aplicaciones distribuidas.

    ![Diagrama de aplicaciones distribuidas de Operations Manager](media/oms-service-map/scom-dad.png)

## <a name="edit-or-delete-the-workspace"></a>Edición o eliminación del área de trabajo
Puede editar o eliminar el área de trabajo configurada desde el panel de **información general de Mapa de servicio** (panel **Administración** > **Operations Management Suite** > **Mapa de servicio**). Puede configurar solo un área de trabajo de Operations Management Suite por ahora.

![Panel de edición de área de trabajo de Operations Manager](media/oms-service-map/scom-edit-workspace.png)

## <a name="configure-rules-and-overrides"></a>Configuración de reglas e invalidaciones
Se crea una regla, _Microsoft.SystemCenter.ServiceMapImport.Rule_, para capturar información periódicamente de Mapa de servicio. Para cambiar los intervalos de sincronización, puede configurar las invalidaciones de la regla (panel **Creación** > **Reglas** > **Microsoft.SystemCenter.ServiceMapImport.Rule**).

![Ventana de propiedades de invalidaciones de Operations Manager](media/oms-service-map/scom-overrides.png)

* **Enabled**: sirve para habilitar o deshabilitar las actualizaciones automáticas. 
* **IntervalMinutes**: restablece el tiempo entre actualizaciones. El intervalo predeterminado es de una hora. Si desea sincronizar asignaciones de servidor con más frecuencia, puede cambiar el valor.
* **TimeoutSeconds**: restablece el período de tiempo antes de que se agote el tiempo de espera de la solicitud. 
* **TimeWindowMinutes**: restablece el período de tiempo para consultar datos. El valor predeterminado es de 60 minutos. El valor máximo permitido por Mapa de servicio es de 60 minutos.

## <a name="known-issues-and-limitations"></a>Problemas conocidos y limitaciones

El diseño actual presenta los problemas y limitaciones siguientes:
* Puede conectarse a una sola área de trabajo de Operations Management Suite.
* Aunque puede agregar servidores manualmente al grupo de servidores de Service Map desde el panel **Creación**, las asignaciones para esos servidores no se sincronizan inmediatamente.  Se sincronizarán desde Service Map durante el siguiente ciclo de sincronización.
* Si realiza cambios en los diagramas de aplicación distribuida creados por el módulo de administración, es probable que se sobrescriban esos cambios en la próxima sincronización con Service Map.

## <a name="create-a-service-principal"></a>Creación de una entidad de servicio
Para obtener documentación oficial de Azure acerca de cómo crear una entidad de servicio, consulte:
* [Uso de Azure PowerShell para crear una entidad de servicio](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-authenticate-service-principal)
* [Uso de la CLI de Azure para crear a una entidad de servicio](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-authenticate-service-principal-cli)
* [Create a service principal by using the Azure portal](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-create-service-principal-portal) (Uso de Azure Portal para crear una entidad de servicio)

### <a name="feedback"></a>Comentarios
¿Quiere hacernos llegar algún comentario acerca de Mapa de servicio o esta documentación? Visite nuestra [página Voz del usuario](https://feedback.azure.com/forums/267889-log-analytics/category/184492-service-map), donde puede sugerir características o votar sugerencias existentes.
