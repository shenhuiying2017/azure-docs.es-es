---
title: "Integración de Service Map con System Center Operations Manager | Microsoft Docs"
description: "Mapa de servicio es una solución de Operations Management Suite (OMS) que detecta automáticamente los componentes de la aplicación en sistemas Windows y Linux y asigna la comunicación entre servicios.  En este artículo se proporcionan detalles para crear diagramas de aplicaciones distribuidas en SCOM automáticamente con Service Map."
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
translationtype: Human Translation
ms.sourcegitcommit: c300ba45cd530e5a606786aa7b2b254c2ed32fcd
ms.openlocfilehash: 1937462eef4647b273dfa029c8f18c80d3443ae8
ms.lasthandoff: 04/14/2017


---

# <a name="service-map-integration-with-system-center-operations-manager-integration"></a>Integración de Service Map con System Center Operations Manager
  > [!NOTE]
  > Esta característica está en versión preliminar privada, por lo que no se debe usar en los sistemas de producción.
  > 
  
Service Map de Operations Management Suite (OMS) detecta automáticamente los componentes de la aplicación en sistemas Windows y Linux y asigna la comunicación entre los servicios. Permite ver los servidores a medida que piensa en ellos, como los sistemas interconectados que ofrecen servicios críticos. Mapa de servicio muestra las conexiones entre servidores, procesos y puertos en cualquier arquitectura conectada TCP sin una configuración necesaria que sea distinta a la instalación de un agente.  Para más detalles, consulte la [documentación de Service Map](operations-management-suite-service-map.md).

Con esta integración entre Service Map y System Center Operations Manager (SCOM), puede crear automáticamente diagramas de aplicaciones distribuidas en SCOM basados en mapas de dependencia dinámica de Mapa de servicio.

## <a name="prerequisites"></a>Requisitos previos
1.    Un grupo de SCOM de administración de un conjunto de servidores.
2.    Un área de trabajo de OMS con la solución Service Map habilitada.
3.    Un conjunto de servidores (al menos uno) que se administren desde SCOM y envíen datos a Service Map.  Se admiten los servidores de Windows y Linux.
4.    Una entidad de servicio con acceso a la suscripción de Azure asociada al área de trabajo de OMS.  [Más información sobre cómo crear una entidad de servicio](#creating-a-service-principal).

## <a name="installing-service-map-management-pack"></a>Instalar el módulo de administración de Service Map
La integración entre SCOM y Service Map se habilita al importar el paquete de módulos de administración de Microsoft.SystemCenter.ServiceMap (Microsoft.SystemCenter.ServiceMap.mpb).  El paquete contiene los siguientes módulos de administración:
* Vistas de aplicación de Microsoft Service Map
* Microsoft System Center Service Map Internal
* Invalidaciones de Microsoft System Center Service Map
* Microsoft System Center Service Map

## <a name="configuring-the-service-map-integration"></a>Configuración de la integración de Service Map
1. Después de instalar el módulo de administración de Service Map, habrá un nuevo nodo, Service Map, en Operations Management Suite (en el panel de administración).
2. Haga clic en "Add workspace" (Agregar área de trabajo) en el panel de información general de Service Map para abrir el asistente para configuración.

    ![Asistente para configuración de SCOM](media/oms-service-map/scom-configuration.png)

3. El primer paso del asistente es la configuración de la conexión, donde escribe la información de la entidad de servicio de Azure. Escriba el id. o el nombre del inquilino, el id. de la aplicación (también conocido como nombre de usuario o ClientID) y la contraseña de la entidad de servicio.  [Más información sobre cómo crear una entidad de servicio](#creating-a-service-principal).

    ![Configuración del SPN de SCOM](media/oms-service-map/scom-config-spn.png)

4. El paso siguiente consiste en seleccionar la suscripción de Azure, el grupo de recursos de Azure (el que contiene el área de trabajo de OMS) y el área de trabajo de OMS.

    ![Configuración del área de trabajo de SCOM](media/oms-service-map/scom-config-workspace.png)

5. El siguiente paso es configurar el grupo de servidores de Service Map con los servidores que desee sincronizar entre SCOM y Service Map.  Haga clic en el botón Add/Remove Servers... (Agregar/Quitar servidores...) . Tenga en cuenta que para que la integración genere un diagrama de aplicaciones distribuidas para un servidor, este debe: 1) administrarse desde SCOM, 2) administrarse desde Service Map y 3) enumerarse en el grupo de servidores de Service Map.

    ![Configuración del grupo de SCOM](media/oms-service-map/scom-config-group.png)

6. (Opcional) Seleccione el grupo de recursos del servidor de administración para comunicarse con OMS y haga clic en "Add workspace" (Agregar área de trabajo).

    ![Configuración del grupo de recursos de SCOM](media/oms-service-map/scom-config-pool.png)

7. Tenga en cuenta que tardará un minuto en configurarse y registrarse el área de trabajo de OMS. Una vez configurado, SCOM iniciará la primera sincronización de Service Map desde OMS.

    ![Configuración del grupo de recursos de SCOM](media/oms-service-map/scom-config-success.png)

**Nota:** El intervalo de sincronización predeterminado se establece en 60 minutos. Los usuarios pueden configurar invalidaciones para cambiar el intervalo de sincronización. También pueden agregar servidores al grupo de servidores de Service Map manualmente desde el panel de creación (panel de creación--> Groups [Grupos], busque "Service Map Servers Group" [Grupo de servidores de Service Map]). Las asignaciones de servidor para esos servidores se sincronizarán con la siguiente sincronización (según el intervalo de sincronización configurado).

## <a name="monitoring-service-map"></a>Supervisión de Service Map
Una vez conectado el área de trabajo de OMS, aparecerá una nueva carpeta de Service Map en el panel de supervisión de la consola de SCOM.
![Supervisión de SCOM](media/oms-service-map/scom-monitoring.png)

La carpeta de Service Map tiene tres nodos:
### <a name="active-alerts"></a>Alertas activas:
Muestra todas las alertas activas referidas a la comunicación entre SCOM y la solución Service Map en OMS.

**Nota:** No son las alertas de OMS que aparecen en SCOM.
### <a name="servers"></a>Servers (Servidores):
Contiene la lista de los servidores supervisados que se hayan configurado para la sincronización con Service Map.

![Supervisión de los servidores de SCOM](media/oms-service-map/scom-monitoring-servers.png)

### <a name="server-dependency-views"></a>Vistas de la dependencia de servidor:
Contiene la lista de todos los servidores sincronizados desde Service Map. Los usuarios pueden hacer clic en cualquier servidor para ver el diagrama de aplicaciones distribuidas.

![Diagrama de aplicaciones distribuidas de SCOM](media/oms-service-map/scom-dad.png)

## <a name="editdelete-workspace"></a>Edición/Eliminación del área de trabajo:
Los usuarios pueden editar o eliminar el área de trabajo configurada desde el panel de información general de Service Map (panel de administración --> Operations Management Suite --> Service Map).  Tenga en cuenta que por ahora solo se puede configurar un área de trabajo de OMS.

![Edición del área de trabajo SCOM](media/oms-service-map/scom-edit-workspace.png)

## <a name="configuring-rules-and-overrides"></a>Configuración de reglas e invalidaciones:
Se crea una regla **_Microsoft.SystemCenter.ServiceMapImport.Rule**_ para capturar información de Mapa de servicio periódicamente.  Los usuarios pueden configurar invalidaciones de esta regla para cambiar los intervalos de sincronización.
Panel de creación --> Rules (Reglas) --> Microsoft.SystemCenter.ServiceMapImport.Rule

![Invalidaciones de SCOM](media/oms-service-map/scom-overrides.png)
* **Enabled** (Habilitado): sirve para habilitar o deshabilitar las actualizaciones automáticas. 
* **IntervalSeconds** (Intervalo en segundos): tiempo entre una actualización y la siguiente.  El valor predeterminado es 1 hora. Los usuarios pueden cambiar el valor aquí si desean sincronizar asignaciones de servidor con más frecuencia.
* **TimeoutSeconds** (Tiempo de espera en segundos): tiempo de vencimiento de la solicitud. 
* **TimeWindowMinutes** (Margen en minutos): amplitud de la consulta de datos.  El valor predeterminado es un margen de 60 minutos. El valor máximo es 1 hora (el máximo que permite Service Map).

## <a name="known-issueslimitations"></a>Problemas conocidos y limitaciones:
Del diseño actual:
1. Aunque los usuarios pueden agregar servidores manualmente al grupo de servidores de Service Map desde el panel de creación, las asignaciones para esos servidores se sincronizarán desde Service Map únicamente durante el siguiente ciclo de sincronización (a los 60 minutos de forma predeterminada. Los usuarios pueden invalidar el tiempo de sincronización). 
2. Los usuarios pueden conectarse a una única área de trabajo de OMS.

## <a name="creating-a-service-principal"></a>Creación de una entidad de servicio
Los siguientes vínculos le llevarán a la documentación oficial de Azure sobre tres maneras de crear una entidad de servicio.
* [Creación de una entidad de servicio con PowerShell](https://docs.microsoft.com/en-us/azure/azure-resource-manager/resource-group-authenticate-service-principal)
* [Creación de una entidad de servicio con la CLI de Azure](https://docs.microsoft.com/en-us/azure/azure-resource-manager/resource-group-authenticate-service-principal-cli)
* [Creación de una entidad de servicio con Azure Portal](https://docs.microsoft.com/en-us/azure/azure-resource-manager/resource-group-create-service-principal-portal)

### <a name="feedback"></a>Comentarios
¿Quiere hacernos llegar algún comentario acerca de Mapa de servicio o esta documentación?  Visite nuestra [página Voz del usuario](https://feedback.azure.com/forums/267889-log-analytics/category/184492-service-map), donde puede sugerir características o votar sugerencias existentes.

