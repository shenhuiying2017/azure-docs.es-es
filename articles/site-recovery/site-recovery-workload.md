---
title: "¿Qué cargas de trabajo se pueden proteger con Azure Site Recovery? | Microsoft Docs"
description: "Describe las cargas de trabajo que pueden protegerse mediante la recuperación ante desastres con el servicio Azure Site Recovery."
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: cfreeman
editor: 
ms.assetid: 4953948f-26c0-4699-8fe7-59d3bfc1d3da
ms.service: site-recovery
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 12/15/2017
ms.author: raynew
ms.openlocfilehash: 03d311f84a4b9bc5f3a4c3c488ee7c84b1ef49ad
ms.sourcegitcommit: 48fce90a4ec357d2fb89183141610789003993d2
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/12/2018
---
# <a name="what-workloads-can-you-protect-with-azure-site-recovery"></a>¿Qué cargas de trabajo se pueden proteger con Azure Site Recovery?

Este artículo describe las aplicaciones y cargas de trabajo que se pueden replicar con el servicio [Azure Site Recovery](site-recovery-overview.md).



## <a name="overview"></a>Información general

Las organizaciones necesitan una estrategia de recuperación ante desastres y continuidad empresarial (BCDR) para mantener las cargas de trabajo y los datos seguros y disponibles durante los tiempos de inactividad planeados y no planeados, y recuperar las condiciones de funcionamiento normales lo antes posible.

Site Recovery es un servicio de Azure que contribuye a su estrategia de recuperación ante desastres y continuidad del negocio. Mediante Site Recovery, puede implementar la replicación con reconocimiento de aplicaciones en la nube o en un sitio secundario. Si las aplicaciones se basan en Windows o Linux, y se ejecutan en servidores físicos, VMware o Hyper-V, puede usar Site Recovery para organizar la replicación, realizar pruebas de recuperación ante desastres y ejecutar conmutaciones por error y conmutación por recuperación.

Site Recovery se integra con aplicaciones de Microsoft, incluidas SharePoint, Exchange, Dynamics, SQL Server y Active Directory. Microsoft también trabaja en estrecha colaboración con destacados proveedores como Oracle, SAP y Red Hat. Puede personalizar las soluciones de replicación en función de cada aplicación.

## <a name="why-use-site-recovery-for-application-replication"></a>¿Por qué es aconsejable usar Site Recovery para la replicación de aplicaciones?

Site Recovery contribuye a la recuperación y protección de nivel de aplicación como se indica a continuación:

* Independiente de las aplicaciones; proporciona replicación para las cargas de trabajo que se ejecutan en un equipo compatible.
* Replicación casi sincrónica con los RPO en solo 30 segundos, con el fin de cubrir las necesidades de las aplicaciones empresariales más críticas.
* Instantáneas coherentes con las aplicaciones para aplicaciones de uno o varios niveles.
* Integración con SQL Server AlwaysOn y asociación con otras tecnologías de replicación de nivel de aplicación, incluida la replicación de AD, SQL AlwaysOn, Grupos de disponibilidad de base de datos (DAG) de Exchange y Oracle Data Guard.
* Planes de recuperación flexibles que permiten recuperar toda una pila de aplicaciones con un solo clic e incluyen scripts externos y acciones manuales en el plan.
* Administración de red avanzada en Site Recovery y Azure para simplificar los requisitos de red de las aplicaciones, incluidas la reserva de direcciones IP, la configuración del equilibrio de carga y la integración con Azure Traffic Manager, lo que reduce los cambios de red de RTO.
* Una biblioteca de automatización enriquecida que proporciona scripts específicos de la aplicación y preparados para la producción que pueden descargarse e integrarse con los planes de recuperación.

## <a name="workload-summary"></a>Resumen de cargas de trabajo
Site Recovery puede replicar cualquier aplicación que se ejecute en una máquina compatible. Además, nos hemos asociado con los equipos de producto para llevar a cabo pruebas de adicionales específicas de la aplicación.

| **Carga de trabajo** |**Replicación de máquinas virtuales de Azure en Azure** |**Replicación de máquinas virtuales de Hyper-V a un sitio secundario** | **Replicación de máquinas virtuales de Hyper-V en Azure** | **Replicación de máquinas virtuales VMware en un sitio secundario** | **Replicación de máquinas virtuales VMware en Azure** |
| --- | --- | --- | --- | --- |---|
| Active Directory, DNS |Y |Y |Y |Y |Y|
| Aplicaciones web (IIS, SQL) |Y |Y |Y |Y |Y|
| System Center Operations Manager |Y |Y |Y |Y |Y|
| SharePoint |Y |Y |Y |Y |Y|
| SAP<br/><br/>Replicación de un sitio de SAP en Azure para no clúster |S (probado por Microsoft) |S (probado por Microsoft) |S (probado por Microsoft) |S (probado por Microsoft) |S (probado por Microsoft)|
| Exchange (no DAG) |Y |Y |Y |Y |Y|
| Escritorio remoto/VDI |Y |Y |Y |Y |Y|
| Linux (sistema operativo y aplicaciones) |S (probado por Microsoft) |S (probado por Microsoft) |S (probado por Microsoft) |S (probado por Microsoft) |S (probado por Microsoft)|
| Dynamics AX |Y |Y |Y |Y |Y|
| Oracle |S (probado por Microsoft) |S (probado por Microsoft) |S (probado por Microsoft) |S (probado por Microsoft) |S (probado por Microsoft)|
| Servidor de archivos de Windows |Y |Y |Y |Y |Y|
| Citrix XenApp y XenDesktop |Y|N/D |Y |N/D |Y |

## <a name="replicate-active-directory-and-dns"></a>Replicación de Active Directory y DNS
Las infraestructuras de DNS y Active Directory son esenciales para la mayoría de las aplicaciones empresariales. Durante la recuperación ante desastres será preciso que proteja y recupere estos componentes de la infraestructura antes de recuperar las aplicaciones y las cargas de trabajo.

Puede usar Site Recovery para crear un plan de recuperación ante desastres automatizada completa para Active Directory y DNS. Por ejemplo, si desea conmutar por error SharePoint y SAP de un servidor principal a un sitio secundario, puede configurar un plan de recuperación que conmute Active Directory primero y, después, un plan de recuperación adicional específico de la aplicación para conmutar por error las demás aplicaciones que usan Active Directory.

[Más información](site-recovery-active-directory.md) sobre la protección de Active Directory y DNS.

## <a name="protect-sql-server"></a>Protección de SQL Server
SQL Server proporciona una base de servicios de datos para los servicios de datos de muchas aplicaciones empresariales de un centro de datos local.  Site Recovery se puede utilizar junto con las tecnologías de HA/DR de SQL Server para proteger las aplicaciones empresariales de niveles que utilizan SQL Server. Site Recovery proporciona:

* Una solución de recuperación ante desastres simple y rentable para SQL Server. Replique varias versiones y ediciones de servidores independientes y clústeres de SQL Server en Azure o en un sitio secundario.  
* Integración con grupos de disponibilidad AlwaysOn de SQL para administrar la conmutación por error y la conmutación por recuperación con planes de recuperación de Azure Site Recovery.
* Planes de recuperación de un extremo a otro para todos los niveles de una aplicación, incluidas las bases de datos de SQL Server.
* Escalado de SQL Server para cargas máximas con Site Recovery "protegiéndolos" en tamaños de máquina virtual de IaaS más grandes en Azure.
* Pruebas fáciles de recuperación ante desastres de SQL Server. Puede ejecutar conmutaciones por error de prueba para analizar datos y realizar comprobaciones de cumplimento sin que ello afecte al entorno de producción.

[Más información](site-recovery-sql.md) sobre cómo proteger SQL Server.

## <a name="protect-sharepoint"></a>Protección de SharePoint
Azure Site Recovery ayuda a proteger las implementaciones de SharePoint como se indica a continuación:

* Elimina la necesidad y los costos de infraestructura asociados a la recuperación ante desastres en una granja en espera. Use Site Recovery para replicar toda la granja (niveles de base de datos, aplicación y web) en Azure o en un sitio secundario.
* Simplifica la administración e implementación de las aplicaciones. Las actualizaciones implementadas en el sitio principal se replican automáticamente y, por consiguiente, están disponibles después de la conmutación por error y recuperación de una granja en un sitio secundario. También reduce la complejidad de la administración y los costes asociados con el hecho de mantener actualizada una granja en espera.
* Simplifica el desarrollo y las pruebas de aplicaciones de SharePoint mediante la creación de un entorno de réplica de copia de producción a petición para realizar pruebas y depuraciones.
* Simplifica la transición a la nube mediante el uso de Site Recovery para migrar las implementaciones de SharePoint a Azure.

[Más información](site-recovery-sharepoint.md) sobre cómo proteger SharePoint.

## <a name="protect-dynamics-ax"></a>Protección de Dynamics AX
Azure Site Recovery le ayuda a proteger soluciones ERP de Dynamics AX mediante:

* La organización de la replicación de todo el entorno de Dynamics AX (niveles web y de AOS, niveles de base de datos y SharePoint) en Azure o en un sitio secundario.
* La simplificación de la migración de las implementaciones de Dynamics AX a la nube (Azure).
* La simplificación del desarrollo y de las pruebas de aplicaciones de Dynamics AX mediante la creación de una copia de producción a petición para realizar pruebas y depuraciones.

[Más información](site-recovery-dynamicsax.md) sobre cómo proteger Dynamic AX.

## <a name="protect-rds"></a>Protección de RDS
Servicios de Escritorio remoto (RDS) habilita Infraestructura de escritorio virtual (VDI), escritorios basados en sesión y aplicaciones, lo que permite a los usuarios trabajar desde cualquier lugar. Con Azure Site Recovery, puede:

* Replicar escritorios virtuales agrupados administrados o no administrados en un sitio secundario y aplicaciones remotas y sesiones en un sitio secundario o Azure.

* Esto es lo que se puede replicar:

| **RDS** |**Replicación de máquinas virtuales de Azure en Azure** | **Replicación de máquinas virtuales de Hyper-V a un sitio secundario** | **Replicación de máquinas virtuales de Hyper-V en Azure** | **Replicación de máquinas virtuales VMware en un sitio secundario** | **Replicación de máquinas virtuales VMware en Azure** | **Replicación de servidores físicos en un sitio secundario** | **Replicación de servidores físicos a Azure** |
|---| --- | --- | --- | --- | --- | --- | --- |
| **Escritorio virtual agrupado (no administrado)** |Sin |Sí |Sin  |Sí |Sin  |Sí |Sin  |
| **Escritorio virtual agrupado (administrado y sin UPD)** |Sin |Sí |Sin  |Sí |Sin  |Sí |Sin  |
| **Aplicaciones remotas y sesiones de escritorio (sin UPD)** |Sí|Sí |Sí |Sí |Sí |Sí |Sí |

[Configuración de la recuperación ante desastres para RDS mediante Azure Site Recovery](https://docs.microsoft.com/windows-server/remote/remote-desktop-services/rds-disaster-recovery-with-azure).

[Más información](https://gallery.technet.microsoft.com/Remote-Desktop-DR-Solution-bdf6ddcb) sobre cómo proteger RDS.

## <a name="protect-exchange"></a>Protección de Exchange
Site Recovery ayuda a proteger Exchange como se indica a continuación:

* En implementaciones de Exchange pequeñas, como servidor individual o independiente, Site Recovery puede realizar la replicación y la conmutación por error en Azure o en un sitio secundario.
* En implementaciones mayores Site Recovery se integra con los DAG de Exchange.
* Los DAG de Exchange son la solución recomendada para la recuperación ante desastres de Exchange en una empresa.  Los planes de recuperación de Site Recovery pueden incluir varios DAG para organizar la conmutación por error de DAG entre sitios.

[Más información](https://gallery.technet.microsoft.com/Exchange-DR-Solution-using-11a7dcb6) sobre cómo proteger Exchange.

## <a name="protect-sap"></a>Protección de SAP
Use Site Recovery para proteger una implementación de SAP como se indica a continuación:

* Habilite la protección de aplicaciones de producción de SAP NetWeaver y no NetWeaver en ejecución de forma local, mediante la replicación de componentes en Azure.
* Habilitar la protección de aplicaciones de producción de SAP NetWeaver y no NetWeaver que se ejecutan en Azure, mediante la replicación de componentes en otro centro de datos de Azure.
* Simplifique la migración a la nube con Site Recovery para migrar la implementación de SAP en Azure.
* Simplifique las actualizaciones del proyecto SAP, las pruebas y la creación de prototipos, mediante la creación de una clon en producción a petición para probar aplicaciones de SAP.

[Más información](site-recovery-sap.md) sobre cómo proteger SAP.

## <a name="protect-iis"></a>Protección de IIS
Use Site Recovery para proteger una implementación de IIS como se indica a continuación:

Azure Site Recovery proporciona la función de recuperación ante desastres mediante la replicación de los componentes críticos de su entorno en un sitio remoto inactivo o en una nube pública, como Microsoft Azure. Dado que las máquinas virtuales con el servidor web y la base de datos se replica en el sitio de recuperación, no es preciso realizar copias de seguridad de los archivos de configuración o de los certificados por separado. Los enlaces y las asignaciones de las aplicaciones que dependen de las variables de entorno que se cambian después de la conmutación por error se pueden actualizar a través de los scripts integrados en los planes de la recuperación ante desastres. Las máquinas virtuales solo se muestran en el sitio de recuperación en caso de conmutación por error. Y no solo esto, Azure Site Recovery también le ayuda a orquestar la conmutación por error de un extremo a otro a través de las siguientes funcionalidades:

-   Secuenciación del apagado e inicio de las máquinas virtuales en los distintos niveles.
-   Incorporación de scripts que permiten la actualización de las dependencias y los enlaces de las aplicaciones después de que se hayan iniciado. Los scripts también se pueden utilizar para actualizar el servidor DNS para que apunte al sitio de recuperación.
-   Asignación de direcciones IP a máquinas virtuales antes de la conmutación por error mediante la asignación de las redes principal y de recuperación y, por consiguiente, el uso de scripts que no es preciso actualizarlos después de la conmutación por error.
-   Funcionalidad de conmutación por error con un solo clic para varias aplicaciones web en los servidores web, con lo que se elimina el alcance de una confusión en caso de desastre.
-   Funcionalidad de probar los planes de recuperación en un entorno aislado en los simulacros de recuperación ante desastres.

[Más información](https://aka.ms/asr-iis) acerca de cómo una granja de servidores web de IIS.

## <a name="protect-citrix-xenapp-and-xendesktop"></a>Protección de Citrix XenApp y XenDesktop
Use Site Recovery para proteger las implementaciones de Citrix XenApp y XenDesktop como se indica a continuación:

* Habilite la protección de la implementación de Citrix XenApp y XenDesktop, para lo que debe replicar diferentes capas de implementación diferentes niveles [entre los que se incluyen servidor DNS de AD, servidor de SQL Database, Citrix Delivery Controller, servidor de StoreFront, XenApp Master (VDA) y Citrix XenApp License Server] en Azure.
* Simplifique la migración a nube mediante el uso de Site Recovery para migrar la implementación de Citrix XenApp y XenDesktop a Azure.
* Simplifique las pruebas de Citrix XenApp o XenDesktop mediante la creación de una copia de producción a petición para la realización de pruebas y depuraciones.
* Esta solución solo se puede aplicar a escritorios virtuales del sistema operativo de Windows Server, no a escritorios virtuales cliente, ya que estos aún no se admiten para la concesión de licencias en Azure.
Aquí encontrará [más información](https://azure.microsoft.com/pricing/licensing-faq/) acerca de la concesión de licencias a escritorios de cliente/servidor de Azure.

[Más información](site-recovery-citrix-xenapp-and-xendesktop.md) acerca de la protección de las implementaciones de Citrix XenApp y XenDesktop. También puede consultar las [notas del producto de Citrix](https://aka.ms/citrix-xenapp-xendesktop-with-asr), donde se detallan.

## <a name="next-steps"></a>pasos siguientes

[Introducción](azure-to-azure-quickstart.md) a la replicación de máquinas virtuales de Azure.
