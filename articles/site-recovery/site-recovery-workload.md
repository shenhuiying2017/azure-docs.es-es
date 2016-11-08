---
title: ¿Qué cargas de trabajo se pueden proteger con Azure Site Recovery?
description: Azure Site Recovery protege sus cargas de trabajo y aplicaciones mediante la coordinación de la replicación, la conmutación por error y la recuperación de máquinas virtuales locales y servidores físicos en Azure o en un sitio local secundario.
services: site-recovery
documentationcenter: ''
author: rayne-wiselman
manager: jwhit
editor: ''

ms.service: site-recovery
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 07/06/2016
ms.author: raynew

---
# ¿Qué cargas de trabajo se pueden proteger con Azure Site Recovery?
Este artículo describe qué aplicaciones y cargas de trabajo se pueden proteger con Azure Site Recovery.

Publique cualquier comentario o pregunta que tenga en la parte inferior de este artículo, o bien en el [foro de Servicios de recuperación de Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).

## Información general
Las organizaciones necesitan una estrategia de continuidad empresarial y recuperación ante desastres (BCDR) que determine la forma en que las aplicaciones, las cargas de trabajo y los datos van a seguir estando disponibles durante los tiempos de inactividad planeados y no planeados, y cómo recuperar las condiciones de trabajo normales lo antes posible.

Site Recovery es un servicio de Azure que contribuye a su estrategia de BCDR, ya que permite implementar soluciones de resistencia que reconocen aplicaciones mediante la coordinación de la replicación de servidores físicos locales y máquinas virtuales en la nube (Azure) o en un sitio secundario. Si las aplicaciones se basan en Windows o Linux, y se ejecutan en servidores físicos, VMware o máquinas virtuales de Hyper-V, puede usar Site Recovery para organizar la replicación, realizar pruebas de recuperación ante desastres y ejecutar conmutaciones por error y conmutación por recuperación.

Site Recovery se integra con aplicaciones de Microsoft, incluidas SharePoint, Exchange, Dynamics, SQL Server y Active Directory. Microsoft también trabaja en estrecha colaboración con los proveedores más importantes a nivel mundial, como Oracle, SAP, IBM y Red Hat para asegurarse de que sus aplicaciones y servicios funcionan bien en plataformas de Microsoft, entre las que se incluye Azure. Las soluciones pueden personalizarse aplicación a aplicación.

## ¿Por qué es aconsejable usar Site Recovery para la protección de aplicaciones?
Site Recovery contribuye a la recuperación y protección de nivel de aplicación como se indica a continuación:

* Replicación casi sincrónica con los RPO en solo 30 segundos, con el fin de cubrir las necesidades de las aplicaciones empresariales más críticas.
* Instantáneas coherente con las aplicaciones para aplicaciones de uno o varios niveles.
* Integración con SQL Server AlwaysOn y asociación con otras tecnologías de replicación de nivel de aplicación, incluida la replicación de AD, SQL AlwaysOn, Grupos de disponibilidad de base de datos (DAG) de Exchange y Oracle Data Guard.
* Planes de recuperación flexibles que permiten recuperar toda una pila de aplicaciones con un solo clic e incluyen scripts externos y acciones manuales en el plan.
* Administración de red avanzada en Site Recovery y Azure para simplificar los requisitos de red de las aplicaciones, incluidas la reserva de direcciones IP, la configuración del equilibrio de carga y la integración con el Administrador de tráfico de Azure, lo que reduce los cambios de red de RTO.
* Una biblioteca de automatización enriquecida que proporciona scripts específicos de la aplicación y preparados para la producción que pueden descargarse e integrarse con los planes de recuperación.

## Resumen de cargas de trabajo
Site Recovery puede replicar cualquier aplicación que se ejecute en una máquina virtual compatible. Además, nos hemos asociado con los equipos de producto para llevar a cabo pruebas de adicionales específicas de la aplicación.

| **Carga de trabajo** | **Replicación de máquinas virtuales de Hyper-V a un sitio secundario** | **Replicación de máquinas virtuales de Hyper-V en Azure** | **Replicación de máquinas virtuales VMware en un sitio secundario** | **Replicación de máquinas virtuales VMware en Azure** |
| --- | --- | --- | --- | --- |
| Active Directory, DNS |Y |Y |Y |Y |
| Aplicaciones web (IIS, SQL) |Y |Y |Y |Y |
| SCOM |Y |Y |Y |Y |
| SharePoint |Y |Y |Y |Y |
| SAP<br/><br/>Replicación de un sitio de SAP en Azure para no clúster |S (probado por Microsoft) |S (probado por Microsoft) |S (probado por Microsoft) |S (probado por Microsoft) |
| Exchange (no DAG) |Y |Próximamente |Y |Y |
| Escritorio remoto/VDI |Y |Y |Y |N/D |
| Linux (sistema operativo y aplicaciones) |S (probado por Microsoft) |S (probado por Microsoft) |S (probado por Microsoft) |S (probado por Microsoft) |
| Dynamics AX |Y |Y |Y |Y |
| Dynamics CRM |Y |Próximamente |Y |Próximamente |
| Oracle |S (probado por Microsoft) |S (probado por Microsoft) |S (probado por Microsoft) |S (probado por Microsoft) |
| Servidor de archivos de Windows |Y |Y |Y |Y |

## Replicación de Active Directory y DNS
Las infraestructuras de DNS y Active Directory son esenciales para la mayoría de las aplicaciones empresariales. Durante la recuperación ante desastres será preciso que proteja y recupere estos componentes de la infraestructura antes de recuperar las aplicaciones y las cargas de trabajo.

Puede usar Site Recovery para crear un plan de recuperación ante desastres automatizada completa para Active Directory y DNS. Por ejemplo, si desea conmutar por error SharePoint y SAP de un servidor principal a un sitio secundario, puede configurar un plan de recuperación que conmute Active Directory primero y, después, un plan de recuperación adicional específico de la aplicación para conmutar por error las demás aplicaciones que usan Active Directory.

[Más información](site-recovery-active-directory.md) sobre la protección de Active Directory y DNS.

## Protección de SQL Server
SQL Server proporciona una base de servicios de datos para los servicios de datos de muchas aplicaciones empresariales de un centro de datos local. Site Recovery se puede utilizar junto con las tecnologías de HA/DR de SQL Server para proteger las aplicaciones empresariales de niveles que utilizan SQL Server. Site Recovery proporciona:

* Una solución de recuperación ante desastres simple y rentable para SQL Server. Replique varias versiones y ediciones de servidores independientes y clústeres de SQL Server en Azure o en un sitio secundario.
* Integración con grupos de disponibilidad AlwaysOn de SQL para administrar la conmutación por error y la conmutación por recuperación con planes de recuperación de Azure Site Recovery.
* Planes de recuperación de un extremo a otro para todos los niveles de una aplicación, incluidas las bases de datos de SQL Server.
* Escalado de SQL Server para cargas máximas con Site Recovery "protegiéndolos" en tamaños de máquina virtual de IaaS más grandes en Azure.
* Pruebas fáciles de recuperación ante desastres de SQL Server. Puede ejecutar conmutaciones por error de prueba para analizar datos y realizar comprobaciones de cumplimento sin que ello afecte al entorno de producción.

[Más información](site-recovery-sql.md) sobre cómo proteger SQL Server.

## Protección de SharePoint
Azure Site Recovery ayuda a proteger las implementaciones de SharePoint como se indica a continuación:

* Elimina la necesidad y los costos de infraestructura asociados a la recuperación ante desastres en una granja en espera. Use Site Recovery para replicar toda la granja (niveles de base de datos, aplicación y web) en Azure o en un sitio secundario.
* Simplifica la administración e implementación de las aplicaciones. Las actualizaciones implementadas en el sitio principal se replican automáticamente y, por consiguiente, están disponibles después de la conmutación por error y recuperación de una granja en un sitio secundario. También reduce la complejidad de la administración y los costes asociados con el hecho de mantener actualizada una granja en espera.
* Simplifica el desarrollo y las pruebas de aplicaciones de SharePoint mediante la creación de un entorno de réplica de copia de producción a petición para realizar pruebas y depuraciones.
* Simplifica la transición a la nube mediante el uso de Site Recovery para migrar las implementaciones de SharePoint a Azure.

[Más información](https://gallery.technet.microsoft.com/SharePoint-DR-Solution-f6b4aeae) sobre cómo proteger SharePoint.

## Protección de Dynamics AX
Azure Site Recovery le ayuda a proteger soluciones ERP de Dynamics AX mediante:

* La organización de la replicación de todo el entorno de Dynamics AX (niveles web y de AOS, niveles de base de datos y SharePoint) en Azure o en un sitio secundario.
* La simplificación de la migración de las implementaciones de Dynamics AX a la nube (Azure).
* La simplificación del desarrollo y de las pruebas de aplicaciones de Dynamics AX mediante la creación de una copia de producción a petición para realizar pruebas y depuraciones.

[Más información](https://gallery.technet.microsoft.com/Dynamics-AX-DR-Solution-b2a76281) sobre cómo proteger Dynamic AX.

## Protección de RDS
Servicios de Escritorio remoto (RDS) habilita Infraestructura de escritorio virtual (VDI), escritorios basados en sesión y aplicaciones, lo que permite a los usuarios trabajar desde cualquier lugar. Con Azure Site Recovery, puede:

* Replicar escritorios virtuales agrupados administrados o no administrados en un sitio secundario y aplicaciones remotas y sesiones en un sitio secundario o Azure.
* Esto es lo que se puede replicar:

| **RDS** | **Replicación de máquinas virtuales de Hyper-V a un sitio secundario** | **Replicación de máquinas virtuales de Hyper-V en Azure** | **Replicación de máquinas virtuales VMware en un sitio secundario** | **Replicación de máquinas virtuales VMware en Azure** | **Replicación de servidores físicos en un sitio secundario** | **Replicación de servidores físicos a Azure** |
| --- | --- | --- | --- | --- | --- | --- |
| **Escritorio virtual agrupado (no administrado)** |Sí |No |Sí |No |Sí |No |
| **Escritorio virtual agrupado (administrado y sin UPD)** |Sí |No |Sí |No |Sí |No |
| **Aplicaciones remotas y sesiones de escritorio (sin UPD)** |Sí |Sí |Sí |Sí |Sí |Sí |

[Más información](https://gallery.technet.microsoft.com/Remote-Desktop-DR-Solution-bdf6ddcb) sobre cómo proteger RDS.

## Protección de Exchange
Site Recovery ayuda a proteger Exchange como se indica a continuación:

* En implementaciones de Exchange pequeñas, como servidores individuales o que no están en clúster, Site Recovery puede realizar la replicación y la conmutación por error en Azure o en un sitio secundario.
* En implementaciones mayores Site Recovery se integra con los DAG de Exchange.
* Los DAG de Exchange son la solución recomendada para la recuperación ante desastres de Exchange en una empresa. Los planes de recuperación de Site Recovery puede incluir varios DAG para organizar la conmutación por error de DAG entre sitios.

[Más información](https://gallery.technet.microsoft.com/Exchange-DR-Solution-using-11a7dcb6) sobre cómo proteger Exchange.

## Protección de SAP
Use Site Recovery para proteger una implementación de SAP como se indica a continuación:

* Habilite la protección de toda la implementación de SAP mediante la replicación de diferentes niveles de implementación en Azure o en un sitio secundario.
* Simplifique la migración a la nube con Site Recovery para migrar la implementación de SAP en Azure.
* Simplifique el desarrollo y las pruebas de SAP mediante la creación de una copia de producción a petición para probar y depurar aplicaciones.

[Más información](http://aka.ms/asr-sap) sobre cómo proteger SAP.

## Pasos siguientes
[Preparación](site-recovery-best-practices.md) para la implementación de Azure Site Recovery

<!---HONumber=AcomDC_0706_2016-->