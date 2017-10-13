---
title: "Replicación de una aplicación de SharePoint de niveles múltiples mediante Azure Site Recovery | Microsoft Docs"
description: "En este artículo se describe cómo replicar una aplicación de SharePoint mediante Azure Site Recovery."
services: site-recovery
documentationcenter: 
author: sujayt
manager: rochakm
editor: 
ms.assetid: 
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/31/2017
ms.author: sutalasi
ms.openlocfilehash: 55323df68715c80d5e8535199cd739921a3baad9
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="replicate-a-multi-tier-sharepoint-application-for-disaster-recovery-using-azure-site-recovery"></a>Replicación de una aplicación de SharePoint de niveles múltiples para una recuperación ante desastres mediante Azure Site Recovery | Microsoft Docs

En este artículo se describe detalladamente cómo proteger una aplicación de SharePoint mediante [Azure Site Recovery](site-recovery-overview.md).


## <a name="overview"></a>Información general

Microsoft SharePoint es una aplicación eficaz que puede ayudar a un grupo o departamento a organizarse, colaborar y compartir información. SharePoint proporciona portales de intranet, administración de archivos y documentos, colaboración, redes sociales, extranets, sitios web, motor de búsqueda Enterprise Search e inteligencia empresarial. También dispone de integración de sistemas, integración de procesos y funcionalidades de automatización de flujos de trabajo. Normalmente, las organizaciones la consideran como una aplicación de nivel 1 sensible a tiempos de inactividad y pérdidas de datos.

En la actualidad, la aplicación Microsoft SharePoint no tiene integrada ninguna funcionalidad de recuperación ante desastres. Independientemente del tipo y la escala de un desastre, la recuperación implica el uso de un centro de datos en espera del que recuperar el conjunto de servidores. Se requieren centros de datos en espera en escenarios donde los sistemas locales de redundancia y copias de seguridad no se puedan recuperar de una interrupción en el centro de datos principal.

Una buena solución de recuperación ante desastres debe permitir el modelado de planes de recuperación alrededor de arquitecturas de aplicación complejas, como SharePoint. También debe tener la capacidad de agregar pasos personalizados para controlar las asignaciones de aplicaciones entre distintos niveles y, por tanto, proporcionar una conmutación por error de un solo clic con un RTO inferior en caso de desastre.

En este artículo se describe detalladamente cómo proteger una aplicación de SharePoint mediante [Azure Site Recovery](site-recovery-overview.md). También se describen los procedimientos recomendados para replicar una aplicación de SharePoint de tres niveles en Azure, cómo llevar a cabo la recuperación ante desastres y cómo conmutar por error la aplicación en Azure.

También puede ver el vídeo siguiente sobre la recuperación de una aplicación de niveles múltiples en Azure.

> [!VIDEO https://channel9.msdn.com/Series/Azure-Site-Recovery/Disaster-Recovery-of-load-balanced-multi-tier-applications-using-Azure-Site-Recovery/player]


## <a name="prerequisites"></a>Requisitos previos

Antes de empezar, no olvide informarse sobre las cuestione siguientes:

1. [Replicación de una máquina virtual en Azure](site-recovery-vmware-to-azure.md)
2. [Diseño de una red de recuperación](site-recovery-network-design.md)
3. [Realización de una conmutación por error de prueba en Azure](site-recovery-test-failover-to-azure.md)
4. [Ejecución de una conmutación por error en Azure](site-recovery-failover.md)
5. [Replicación de un controlador de dominio](site-recovery-active-directory.md)
6. [Replicación de SQL Server](site-recovery-sql.md)

## <a name="sharepoint-architecture"></a>Arquitectura de SharePoint

SharePoint puede implementarse en uno o varios servidores usando topologías por niveles y roles de servidor para implementar un diseño de granja de servidores que cumpla determinados objetivos. Una granja de servidores de SharePoint típica de gran tamaño y alta demanda que admita un número elevado de usuarios simultáneos y elementos de contenido utiliza la agrupación de servicios como parte de su estrategia de escalabilidad. Este enfoque implica ejecutar servicios en servidores dedicados, agrupando estos servicios y posteriormente escalando horizontalmente los servidores como un grupo. La topología siguiente ilustra la agrupación de servicios y servidores para una granja de servidores de SharePoint de tres niveles. Consulte la documentación de SharePoint y las arquitecturas de línea de productos para obtener instrucciones detalladas sobre diferentes topologías de SharePoint. Encontrará más detalles sobre la implementación de SharePoint 2013 en [este documento](https://technet.microsoft.com/en-us/library/cc303422.aspx).



![Modelo de implementación 1](./media/site-recovery-sharepoint/sharepointarch.png)


## <a name="site-recovery-support"></a>Compatibilidad de Site Recovery

Para elaborar este artículo, se utilizaron máquinas virtuales de VMware con Windows Server 2012 R2 Enterprise. Se utilizaron SharePoint 2013 Enterprise y SQL server 2014 Enterprise. Como la replicación de la recuperación del sitio no depende de la aplicación, se pretende que estas recomendaciones sirvan también para los escenarios siguientes.

### <a name="source-and-target"></a>Origen y destino

**Escenario** | **En un sitio secundario** | **En Azure**
--- | --- | ---
**Hyper-V** | Sí | Sí
**VMware** | Sí | Sí
**Servidor físico** | Sí | Sí

### <a name="sharepoint-versions"></a>Versiones de SharePoint
Se admiten las siguientes versiones de servidor de SharePoint.

* SharePoint Server 2013 Standard
* SharePoint Server 2013 Enterprise
* SharePoint Server 2016 Standard
* SharePoint Server 2016 Enterprise

### <a name="things-to-keep-in-mind"></a>Aspectos que debe tener en cuenta

Si usa un clúster basado en disco compartido en cualquier nivel de la aplicación, no podrá utilizar la replicación de Site Recovery para replicar estas máquinas virtuales. Puede usar replicación nativa proporcionada por la aplicación y, a continuación, usar un [plan de recuperación](site-recovery-create-recovery-plans.md) para conmutar por error todas las capas.

## <a name="replicating-virtual-machines"></a>Replicación de máquinas virtuales

Siga [estas directrices](site-recovery-vmware-to-azure.md) para comenzar a replicar la máquina virtual en Azure.

* Una vez completada la replicación, asegúrese de acceder a todas las máquinas virtuales de cada nivel y seleccionar el mismo conjunto de disponibilidad en Elemento replicado > Configuración > Propiedades > Proceso y red. Por ejemplo, si el nivel web tiene tres máquinas virtuales, asegúrese de que todas están configuradas para formar parte del mismo conjunto de disponibilidad en Azure.

    ![Configuración de conjuntos de disponibilidad](./media/site-recovery-sharepoint/select-av-set.png)

* Para obtener instrucciones sobre la protección de Active Directory y DNS, consulte el documento [Protección de Active Directory y DNS con Azure Site Recovery](site-recovery-active-directory.md).

* Para obtener instrucciones sobre la protección de niveles de bases de datos ejecutadas en SQL server, consulte el documento [Protección de Active Directory y DNS con Azure Site Recovery](site-recovery-active-directory.md).

## <a name="networking-configuration"></a>Configuración de red

### <a name="network-properties"></a>Propiedades de red

* En máquinas virtuales de nivel de web y aplicación, configure las opciones de red en Azure Portal para que las máquinas virtuales se conecten a la red de recuperación ante desastres adecuada después de la conmutación por error.

    ![Selección de la red](./media/site-recovery-sharepoint/select-network.png)


* Si utiliza una dirección IP estática, especifique la dirección IP que desea utilizar con la máquina virtual en campo **IP de destino**.

    ![Configuración de direcciones IP estáticas](./media/site-recovery-sharepoint/set-static-ip.png)

### <a name="dns-and-traffic-routing"></a>Enrutamiento de tráfico y DNS

Para sitios orientados a Internet, [cree un perfil de Traffic Manager de tipo Prioridad](../traffic-manager/traffic-manager-create-profile.md) en la suscripción de Azure. A continuación, configure el perfil de DNS y Traffic Manager de la siguiente manera.


| **Where** | **Origen** | **Destino**|
| --- | --- | --- |
| DNS público | DNS público para sitios de SharePoint <br/><br/> Por ejemplo: sharepoint.contoso.com | Administrador de tráfico <br/><br/> contososharepoint.trafficmanager.net |
| DNS local | sharepointonprem.contoso.com | Dirección IP pública en la granja de servidores locales |


En el perfil de Traffic Manager, [cree los puntos de conexión principal y de recuperación](../traffic-manager/traffic-manager-configure-priority-routing-method.md). Utilice el punto de conexión externo para el punto de conexión local y la dirección IP pública para el punto de conexión de Azure. Asegúrese de que la prioridad sea superior a la del punto de conexión local.

Hospede una página de prueba en un puerto específico (por ejemplo, 800) en el nivel web de SharePoint para que Traffic Manager detecte automáticamente la disponibilidad después de la conmutación por error. Se trata de una solución alternativa en caso de que no se puede habilitar la autenticación anónima en alguno de los sitios de SharePoint.

[Configure el perfil de Traffic Manager](../traffic-manager/traffic-manager-configure-priority-routing-method.md) con las opciones siguientes.

* Método de enrutamiento: Prioridad
* Período de vida (TTL) de DNS: 30 segundos
* Configuración del monitor del punto de conexión: Si se puede habilitar la autenticación anónima, puede proporcionar un punto de conexión de sitio web específico. También puede usar una página de prueba en un puerto específico (por ejemplo, 800).

## <a name="creating-a-recovery-plan"></a>Creación de un plan de recuperación

Los planes de recuperación permiten secuenciar la conmutación por error de los diferentes niveles de una aplicación de niveles múltiples y, por tanto, mantener la coherencia de la aplicación. Siga los pasos que se indican a continuación cuando cree un plan de recuperación para una aplicación web de múltiples niveles. [Aprenda más sobre la creación de un plan de recuperación](site-recovery-runbook-automation.md#customize-the-recovery-plan).

### <a name="adding-virtual-machines-to-failover-groups"></a>Incorporación de máquinas virtuales a grupos de conmutación por error

1. Cree un plan de recuperación agregando la aplicación y las máquinas virtuales de nivel web.
2. Haga clic en Personalizar para agrupar las máquinas virtuales. De forma predeterminada, todas las máquinas virtuales forman parte del grupo 1.

    ![Personalizar RP](./media/site-recovery-sharepoint/rp-groups.png)

3. Cree otro grupo (grupo 2) y mueva las máquinas virtuales de nivel web al nuevo grupo. Las máquinas virtuales de nivel de aplicación deben formar parte del grupo 1, mientras que las máquinas virtuales de nivel web deben formar parte del grupo 2. Esto es para asegurarse de que las máquinas virtuales de nivel de aplicación arranquen en primer lugar, seguidas por las máquinas virtuales de nivel web.


### <a name="adding-scripts-to-the-recovery-plan"></a>Incorporación de scripts al plan de recuperación

Puede implementar los scripts de Azure Site Recovery utilizados en su cuenta de automatización haciendo clic en el botón Implementar en Azure más abajo. Si utiliza un script publicado, asegúrese de seguir las instrucciones en el script.

[![Implementación en Azure](https://azurecomcdn.azureedge.net/mediahandler/acomblog/media/Default/blog/c4803408-340e-49e3-9a1f-0ed3f689813d.png)](https://aka.ms/asr-automationrunbooks-deploy)

1. Agregue un script anterior a la acción del grupo 1 al grupo de disponibilidad de SQL de conmutación por error. Use el script ASR-SQL-FailoverAG publicado en los scripts de ejemplo. Asegúrese de seguir las instrucciones en el script y realice los cambios necesarios de forma adecuada.

    ![Paso 1 para agregar el script AG](./media/site-recovery-sharepoint/add-ag-script-step1.png)

    ![Paso 2 para agregar el script AG](./media/site-recovery-sharepoint/add-ag-script-step2.png)

2. Agregue un script posterior a la acción para adjuntar un equilibrador de carga en las máquinas virtuales conmutadas por error del nivel web (grupo 2). Use el script ASR-AddSingleLoadBalancer publicado en los scripts de ejemplo. Asegúrese de seguir las instrucciones en el script y realice los cambios necesarios de forma adecuada.

    ![Paso 1 para agregar el script LB](./media/site-recovery-sharepoint/add-lb-script-step1.png)

    ![Paso 2 para agregar el script LB](./media/site-recovery-sharepoint/add-lb-script-step2.png)

3. Agregue un paso manual para actualizar los registros DNS de modo que apunten a la nueva granja de servidores en Azure.

    * En sitios orientados a Internet no se requieren las actualizaciones DNS son posteriores a la conmutación por error. Siga los pasos descritos en la sección Guía de red para configurar Traffic Manager. Si el perfil de Traffic Manager se ha configurado como se describe en la sección anterior, agregue un script para abrir el puerto ficticio (800 en el ejemplo) en la máquina virtual de Azure.

    * En sitios internos orientados internamente, agregue un paso manual para actualizar el registro DNS de modo que apunte a la nueva dirección IP del equilibrador de carga de la máquina virtual de nivel web.

4. Agregue un paso manual para restaurar la aplicación de búsqueda desde una copia de seguridad o iniciar un nuevo servicio de búsqueda.

5. Para restaurar la aplicación de servicio de búsqueda desde una copia de seguridad, siga los pasos indicados a continuación.

    * Este método supone que se ha realizado una copia de seguridad de la aplicación de servicio de búsqueda antes del desastre y que la copia de seguridad está disponible en el sitio de recuperación ante desastres.
    * Esto puede lograrse fácilmente programando la copia de seguridad (por ejemplo, una vez al día) y usando un procedimiento de copia para colocar la copia de seguridad en el sitio de recuperación ante desastres. Los procedimientos de copia pueden incluir programas con scripts, como AzCopy (Azure Copy), o la configuración de DFSR (replicación de servicios de archivos distribuida).
    * Ahora que se está ejecutando la granja de servidores de SharePoint, vaya a la administración central, seleccione Copias de seguridad y restauración, y elija Restaurar. La restauración interroga la ubicación de copia de seguridad especificada (puede que tenga que actualizar el valor). Seleccione la copia de seguridad de la aplicación de servicio de búsqueda que desea restaurar.
    * Se restaura la búsqueda. Tenga en cuenta que la restauración espera encontrar la misma topología (el mismo número de servidores) y las mismas letras de unidad de disco duro asignadas a esos servidores. Para obtener más información, consulte el documento [Restaurar las aplicaciones de servicio de búsqueda en SharePoint 2013](https://technet.microsoft.com/library/ee748654.aspx).


6. Para iniciar con una nueva aplicación de servicio de búsqueda, siga los pasos indicados a continuación.

    * Este método supone que hay disponible una copia de seguridad de la base de datos de "Administración de búsqueda" en el sitio de recuperación ante desastres.
    * Puesto que no se replican las otras bases de datos de la aplicación de servicio de búsqueda, deben crearse de nuevo. Para ello, vaya a la administración central y elimine la aplicación de servicio de búsqueda. En todos los servidores que hospeden el índice de búsqueda, elimine los archivos de índice.
    * Vuelva a crear la aplicación de servicio de búsqueda para recrear las bases de datos. Se recomienda tener un script preparado que vuelve a crear esta aplicación de servicio, ya que no se pueden realizar todas las acciones mediante la GUI. Por ejemplo, solo se puede establecer la ubicación de la unidad de índice y configurar la topología de búsqueda mediante cmdlets de PowerShell de SharePoint. Use el cmdlet de Windows PowerShell Restore-SPEnterpriseSearchServiceApplication y especifique la base de datos de administración de búsqueda replicada y con registros enviados automáticamente (Search_Service__DB). Este cmdlet proporciona la configuración de búsqueda, esquema, propiedades administradas, reglas y orígenes, y crea un conjunto predeterminado del resto de los componentes.
    * Una vez recreada la aplicación de servicio de búsqueda, debe iniciar un rastreo completo para cada origen de contenido para restaurar el servicio de búsqueda. Se perderán determinados datos de análisis de la granja de servidores locales, como las recomendaciones de búsqueda.

7. Una vez completados todos los pasos, guarde el plan de recuperación. El plan de recuperación final tendrá un aspecto similar al siguiente.

    ![RP guardado](./media/site-recovery-sharepoint/saved-rp.png)

## <a name="doing-a-test-failover"></a>Realización de una conmutación por error de prueba
Siga [estas directrices](site-recovery-test-failover-to-azure.md) para llevar a cabo una conmutación por error de prueba.

1.  Vaya a Azure Portal y seleccione el almacén de Recovery Services.
2.  Haga clic en el plan de recuperación creado para la granja de servidores de SharePoint.
3.  Haga clic en 'Probar conmutación por error'.
4.  Seleccione un punto de recuperación y una red virtual de Azure para iniciar el proceso de conmutación por error de prueba.
5.  Cuando el entorno secundario esté activo, podrá realizar las validaciones.
6.  Después de completar las validaciones, haga clic en Limpiar conmutación por error de prueba en el plan de recuperación para limpiar el entorno de la conmutación por error de prueba.

Para obtener instrucciones sobre cómo realizar la conmutación por error para AD y DNS, consulte el documento [Consideraciones sobre la conmutación por error de prueba](site-recovery-active-directory.md#test-failover-considerations).

Para obtener instrucciones sobre cómo realizar la conmutación por error de grupos de disponibilidad AlwaysOn de SQL, consulte el documento [Conmutación por error de prueba para Always On de SQL Server](site-recovery-sql.md#steps-to-do-a-test-failover).

## <a name="doing-a-failover"></a>Realización de una conmutación por error
Siga [estas directrices](site-recovery-failover.md) cuando realice una conmutación por error.

1.  Vaya a Azure Portal y seleccione el almacén de Recovery Services.
2.  Haga clic en el plan de recuperación creado para la granja de servidores de SharePoint.
3.  Haga clic en 'Conmutación por error'.
4.  Seleccione el punto de recuperación en el que se va a iniciar el proceso de conmutación por error.

## <a name="next-steps"></a>Pasos siguientes
Puede obtener más información sobre la [replicación de otras aplicaciones](site-recovery-workload.md) a través de Site Recovery.
