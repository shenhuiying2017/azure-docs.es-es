---
title: 'Preguntas frecuentes: replicación de VMware en Azure con Azure Site Recovery | Microsoft Docs'
description: En este artículo se resumen las preguntas frecuentes al replicar máquinas virtuales de VMware locales en Azure mediante Azure Site Recovery
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: article
ms.date: 03/15/2018
ms.author: raynew
ms.openlocfilehash: 345b73db423c6e12b56bb3308f7700917a372dda
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 03/23/2018
ms.locfileid: "30185227"
---
# <a name="common-questions---vmware-to-azure-replication"></a>Preguntas frecuentes: replicación de VMware en Azure

En este artículo se responden las preguntas frecuentes que vemos al replicar máquinas virtuales de VMware locales en Azure. Si tiene alguna pregunta después de leer el artículo, publíquela en el [Foro de Azure Recovery Services](https://social.msdn.microsoft.com/Forums/azure/home?forum=hypervrecovmgr).


## <a name="general"></a>General
### <a name="how-is-site-recovery-priced"></a>¿Cómo se fija el precio de Site Recovery?
Consulte los detalles de los [precios de Azure Site Recovery](https://azure.microsoft.com/en-in/pricing/details/site-recovery/).

### <a name="how-do-i-pay-for-azure-vms"></a>¿Cómo se pagan las máquinas virtuales de Azure?
Durante la replicación, los datos se replican en Azure Storage y no se paga ningún cambio en las máquinas virtuales. Cuando se ejecuta una conmutación por error en Azure, Site Recovery crea automáticamente máquinas virtuales IaaS de Azure. Después de eso, se le facturan los recursos de proceso que consume en Azure.

### <a name="what-can-i-do-with-vmware-to-azure-replication"></a>¿Qué puedo hacer con la replicación de VMware en Azure?
- **Recuperación ante desastres**: puede configurar la recuperación ante desastres completa. En este escenario, replica máquinas virtuales de VMware locales en Azure Storage. Luego, si la infraestructura local no está disponible, puede conmutar por error en Azure. Cuando conmuta por error, las máquinas virtuales de Azure se crean con los datos replicados. Puede acceder a aplicaciones y cargas de trabajo en las máquinas virtuales de Azure hasta que el centro de datos local vuelva a estar disponible. Luego, puede conmutar por recuperación desde Azure al sitio local.
- **Migración**: puede usar Site Recovery para migrar máquinas virtuales de VMware locales a Azure. En este escenario, replica máquinas virtuales de VMware locales en Azure Storage. Luego, conmuta por error desde el entorno local a Azure. Después de la conmutación por error, las aplicaciones y cargas de trabajo están disponibles y se ejecutan en máquinas virtuales de Azure.



## <a name="azure"></a>Azure
### <a name="what-do-i-need-in-azure"></a>¿Qué necesito tener en Azure?
Necesita una suscripción de Azure, un almacén de Recovery Services, una cuenta de almacenamiento y una red virtual. El almacén, la cuenta de almacenamiento y la red deben estar en la misma región.

### <a name="what-azure-storage-account-do-i-need"></a>¿Qué cuenta de Azure Storage necesito?
Puede usar una cuenta de almacenamiento LRS o GRS. Se recomienda GRS para que los datos sean resistentes si se produce una interrupción regional o si no se puede recuperar la región principal. No se admite Premium Storage.

### <a name="does-my-azure-account-need-permissions-to-create-vms"></a>¿La cuenta de Azure necesita permisos para crear máquinas virtuales?
Si es administrador de una suscripción, ya tiene los permisos de replicación que necesita. Si no es así, necesita permisos para crear una máquina virtual de Azure en el grupo de recursos y en la red virtual que especifica al configurar Site Recovery, además de permisos para escribir en la cuenta de almacenamiento seleccionada. [Más información](site-recovery-role-based-linked-access-control.md#permissions-required-to-enable-replication-for-new-virtual-machines).



## <a name="on-premises"></a>Local 

### <a name="what-do-i-need-on-premises"></a>¿Qué necesito tener en el entorno local?
En el entorno local, necesita los componentes de Site Recovery, instalados en una sola máquina virtual de VMware. También necesita una infraestructura de VMware, con al menos un host ESXi, y le recomendamos que tenga un servidor vCenter. Además, necesita una o varias máquinas virtuales de VMware para realizar la replicación. [Más información](vmware-azure-architecture.md) sobre la arquitectura de VMware en Azure.

El servidor de configuración local se puede implementar de una de las dos maneras siguientes:

1. Implementarlo mediante una plantilla de máquina virtual con el servidor de configuración preinstalado. [Obtenga más información aquí.](vmware-azure-tutorial.md#download-the-vm-template)
2. Implementarlo con el programa de instalación en una máquina de Windows Server 2016 de su elección. [Obtenga más información aquí.](physical-azure-disaster-recovery.md#set-up-the-source-environment)

Para conocer los pasos necesarios para implementar el servidor de configuración en sus propias máquinas de Windows Server, en el objetivo Protección de habilitar la protección, elija **To Azure > Not virtualized/Others** (A Azure > No virtualizados/Otros).

### <a name="where-do-on-premises-vms-replicate-to"></a>¿A dónde se replican las máquinas virtuales locales?
Los datos se replican a Azure Storage. Cuando se ejecuta una conmutación por error, Site Recovery crea automáticamente máquinas virtuales de Azure a partir de la cuenta de almacenamiento.

### <a name="what-apps-can-i-replicate"></a>¿Qué aplicaciones se pueden replicar?
Puede replicar cualquier aplicación o carga de trabajo en ejecución en una máquina virtual de VMware que cumpla con los [requisitos de replicación](vmware-physical-azure-support-matrix.md##replicated-machines). Site Recovery proporciona compatibilidad para la replicación compatible con aplicaciones, para que estas se puedan conmutar por error y por recuperación a un estado inteligente. Site Recovery se integra con aplicaciones de Microsoft como SharePoint, Exchange, Dynamics, SQL Server y Active Directory; además, colabora estrechamente con los principales proveedores, como Oracle, SAP, IBM y Red Hat. [Obtenga más información](site-recovery-workload.md) acerca de la protección de la carga de trabajo.

### <a name="can-i-replicate-to-azure-with-a-site-to-site-vpn"></a>¿Puedo replicar en Azure con una VPN de sitio a sitio?
Site Recovery replica los datos desde el entorno local a una instancia de Azure Storage a través de un punto de conexión público o mediante el uso del emparejamiento público de ExpressRoute. No se admite la replicación a través de una red VPN de sitio a sitio.

### <a name="can-i-replicate-to-azure-with-expressroute"></a>¿Puedo replicar en Azure con ExpressRoute?
Sí, puede usar ExpressRoute para replicar máquinas virtuales en Azure. Site Recovery replica datos en una cuenta de Azure Storage a través de un punto de conexión público y es necesario configurar el [emparejamiento público](../expressroute/expressroute-circuit-peerings.md#azure-public-peering) para la replicación de Site Recovery. Una vez que las máquinas virtuales conmutan por error en una red virtual de Azure, puede acceder a ellas a través del [emparejamiento privado](../expressroute/expressroute-circuit-peerings.md#azure-private-peering).


### <a name="why-cant-i-replicate-over-vpn"></a>¿Por qué no puedo replicar a través de VPN?

Cuando se replica en Azure, el tráfico de replicación alcanza los puntos de conexión públicos de una cuenta de Azure Storage, por lo que solo se puede replicar a través de la red pública de Internet con ExpressRoute (emparejamiento público) y VPN no funciona. 



## <a name="what-are-the-replicated-vm-requirements"></a>¿Cuáles son los requisitos de las máquinas virtuales replicadas?

Para realizar la replicación, una máquina virtual de VMware debe ejecutar un sistema operativo compatible. Además, la máquina virtual debe cumplir con los requisitos de las máquinas virtuales de Azure. [Más información](vmware-physical-azure-support-matrix.md##replicated-machines) en la matriz de compatibilidad.

## <a name="how-often-can-i-replicate-to-azure"></a>¿Con qué frecuencia se puede replicar en Azure?
La replicación es continua cuando se replican máquinas virtuales de VMware en Azure.

## <a name="can-i-extend-replication"></a>¿Se puede extender la replicación?
No se admite la replicación extendida o encadenada. Solicite esta característica en el [foro de comentarios](http://feedback.azure.com/forums/256299-site-recovery/suggestions/6097959-support-for-exisiting-extended-replication).

## <a name="can-i-do-an-offline-initial-replication"></a>¿Se puede realizar una replicación inicial sin conexión?
No es una opción admitida. Solicite esta característica en el [foro de comentarios](http://feedback.azure.com/forums/256299-site-recovery/suggestions/6227386-support-for-offline-replication-data-transfer-from).

### <a name="can-i-exclude-disks"></a>¿Se pueden excluir discos?
Sí, puede excluir discos de la replicación. 

### <a name="can-i-replicate-vms-with-dynamic-disks"></a>¿Se pueden replicar máquinas virtuales con discos dinámicos?
Los discos dinámicos se pueden replicar. El disco del sistema operativo debe ser un disco básico.

### <a name="can-i-add-a-new-vm-to-an-existing-replication-group"></a>¿Puedo agregar una máquina virtual nueva a un grupo de replicación existente?
Sí.

## <a name="configuration-server"></a>Servidor de configuración

### <a name="what-does-the-configuration-server-do"></a>¿Qué hace el servidor de configuración?
El servidor de configuración ejecuta los componentes locales de Site Recovery, incluidos: 
- El servidor de configuración que coordina las comunicaciones entre el entorno local y Azure, además de administrar la replicación de datos.
- El servidor de procesos que actúa como puerta de enlace de replicación. Recibe los datos de la replicación, los optimiza mediante almacenamiento en caché, compresión y cifrado, y los envía a Azure Storage. El servidor de procesos también instala Mobility Service en las máquinas virtuales que desea replicar y realiza la detección automática de las máquinas virtuales de VMware locales.
- El servidor de destino maestro que controla los datos de replicación durante la conmutación por recuperación desde Azure.

### <a name="where-do-i-set-up-the-configuration-server"></a>¿Dónde configuro el servidor de configuración?
Necesita una sola máquina virtual de VMware local de alta disponibilidad para el servidor de configuración.

### <a name="what-are-the-requirements-for-the-configuration-server"></a>¿Cuáles son los requisitos para el servidor de configuración?

Revise los [requisitos previos](vmware-azure-deploy-configuration-server.md#prerequisites).

### <a name="can-i-manually-set-up-the-configuration-server-instead-of-using-a-template"></a>¿Puedo configurar manualmente el servidor de configuración en lugar de usar una plantilla?
Se recomienda usar la versión más reciente de la plantilla de OVF para [crear la máquina virtual del servidor de configuración](vmware-azure-deploy-configuration-server.md). Si por algún motivo no puede hacerlo, por ejemplo, si no tiene acceso al servidor de VMware, puede [descargar el archivo de instalación unificada](physical-azure-set-up-source.md) del portal y ejecutarlo en una máquina virtual. 

### <a name="can-a-configuration-server-replicate-to-more-than-one-region"></a>¿Un servidor de configuración se puede replicar en más de una región?
Nº Para hacerlo, necesita configurar un servidor de configuración en cada región.

### <a name="can-i-host-a-configuration-server-in-azure"></a>¿Puedo hospedar un servidor de configuración en Azure?
Si bien es posible, la máquina virtual de Azure que ejecuta el servidor de configuración necesitaría comunicarse con las máquinas virtuales y la infraestructura de VMware locales. Es probable que la sobrecarga no sea viable.


### <a name="where-can-i-get-the-latest-version-of-the-configuration-server-template"></a>¿Dónde puedo descargar la versión más reciente de la plantilla del servidor de configuración?
Descargue la versión más reciente del [Centro de descarga de Microsoft](https://aka.ms/asrconfigurationserver).

### <a name="how-do-i-update-the-configuration-server"></a>¿Cómo actualizo el servidor de configuración?
Se instalan paquetes acumulativos de actualizaciones. Puede encontrar información sobre las actualizaciones más recientes en la [página de actualizaciones de la wiki](https://social.technet.microsoft.com/wiki/contents/articles/38544.azure-site-recovery-service-updates.aspx).

## <a name="mobility-service"></a>Mobility Service

### <a name="where-can-i-find-the-mobility-service-installers"></a>¿Dónde puedo encontrar los instaladores de Mobility Service?
Los instaladores se encuentran en la carpeta **%ProgramData%\ASR\home\svsystems\pushinstallsvc\repository** del servidor de configuración.

## <a name="how-do-i-install-the-mobility-service"></a>¿Cómo instalo Mobility Service?
Si desea instalarlo en cada máquina virtual que desea replicar, use una [instalación de inserción](vmware-azure-install-mobility-service.md#install-mobility-service-by-push-installation-from-azure-site-recovery) o una instalación manual desde [la interfaz de usuario](vmware-azure-install-mobility-service.md#install-mobility-service-manually-by-using-the-gui), o bien [use PowerShell](vmware-azure-install-mobility-service.md#install-mobility-service-manually-at-a-command-prompt). También puede usar una herramienta de implementación, como [System Center Configuration Manager](vmware-azure-mobility-install-configuration-mgr.md) o [DSC y Azure Automation](vmware-azure-mobility-deploy-automation-dsc.md).



## <a name="security"></a>Seguridad

### <a name="what-access-does-site-recovery-need-to-vmware-servers"></a>¿Qué acceso a los servidores de VMware necesita Site Recovery?
Site Recovery necesita acceso a los servidores de VMware para:

- Configurar una máquina virtual de VMware que ejecute el servidor de configuración y otros componentes locales de Site Recovery. [Más información](vmware-azure-deploy-configuration-server.md)
- Detectar automáticamente máquinas virtuales para su replicación. Más información sobre cómo preparar una cuenta para la detección automática. [Más información](vmware-azure-tutorial-prepare-on-premises.md#prepare-an-account-for-automatic-discovery)


### <a name="what-access-does-site-recovery-need-to-vmware-vms"></a>¿Qué acceso a las máquinas virtuales de VMware necesita Site Recovery?

- Para poder realizar la replicación, una máquina virtual de VMware debe tener Site Recovery Mobility Service instalado y en ejecución. Puede implementar manualmente la herramienta o especificar que Site Recovery debe realizar una instalación de inserción del servicio cuando habilite la replicación de una máquina virtual. Para realizar la instalación de inserción, Site Recovery necesita una cuenta que pueda usar para instalar el componente del servicio. [Más información](vmware-azure-tutorial-prepare-on-premises.md#prepare-an-account-for-mobility-service-installation). El servidor de proceso (que se ejecuta de manera predeterminada en el servidor de configuración) realiza esta instalación. [Más información](vmware-azure-install-mobility-service.md) sobre la instalación de Mobility Service.
- Durante la replicación, las máquinas virtuales se comunican con Site Recovery como se indica a continuación:
    - Las máquinas virtuales se comunican con el servidor de configuración en el puerto HTTPS 443 para la administración de la replicación.
    - Las máquinas virtuales envían los datos de replicación al servidor de procesos en el puerto HTTPS 9443 (se puede modificar).
    - Si habilita la coherencia entre varias máquinas virtuales, estas se comunican entre sí a través del puerto 20004.


### <a name="is-replication-data-sent-to-site-recovery"></a>¿Los datos de replicación se envían a Site Recovery?
No, Site Recovery no intercepta los datos replicados ni tiene información sobre lo que se ejecuta en las máquinas virtuales. Los datos de replicación se intercambian entre los hipervisores de VMware y Azure Storage. Site Recovery no tiene capacidad para interceptar los datos. Únicamente se envían los metadatos necesarios para coordinar la replicación y la conmutación por error al servicio Site Recovery.  

Site Recovery está certificado según la norma ISO 27001:2013, 27018, además de HIPAA y DPA, y está completando sus evaluaciones de SOC2 y FedRAMP JAB.

### <a name="can-we-keep-on-premises-metadata-within-a-geographic-regions"></a>¿Podemos conservar los metadatos locales dentro de las regiones geográficas?
Sí. Cuando crea un almacén en una región, nos aseguramos de que todos los datos que Site Recovery usa permanezcan dentro del límite geográfico de esa región.

### <a name="does-site-recovery-encrypt-replication"></a>¿Site Recovery cifra la replicación?
Sí, se admite tanto el cifrado en tránsito como el [cifrado en Azure](https://docs.microsoft.com/azure/storage/storage-service-encryption).


## <a name="failover-and-failback"></a>Conmutación por error y conmutación por recuperación
### <a name="how-far-back-can-i-recover"></a>¿Hasta cuánto tiempo atrás puedo recuperar?
Para VMware en Azure, el punto de recuperación más antiguo que puede usar es de 72 horas.

### <a name="how-do-i-access-azure-vms-after-failover"></a>¿Cómo accedo a las máquinas virtuales de Azure después de la conmutación por error?
Después de la conmutación por error, puede tener acceso a las máquinas virtuales de Azure a través de una conexión segura a Internet o a través de una VPN de sitio a sitio o mediante Azure ExpressRoute. Debe preparar algunas cosas para la conexión. [Más información](site-recovery-test-failover-to-azure.md#prepare-to-connect-to-azure-vms-after-failover)

### <a name="is-failed-over-data-resilient"></a>¿Son resistentes los datos conmutados por error?
Azure está diseñado para la resistencia. Site Recovery está diseñado para la conmutación por error en un centro de datos de Azure secundario según el Acuerdo de Nivel de Servicio de Azure. Cuando se produce una conmutación por error, nos aseguramos de que los metadatos y los almacenes permanecen en la misma región geográfica que eligió para el almacén.

### <a name="is-failover-automatic"></a>¿La conmutación por error es automática?
La [conmutación por error](site-recovery-failover.md) no es automática. Puede iniciar las conmutaciones por error con solo un clic en el portal o bien puede usar [PowerShell](/powershell/module/azurerm.siterecovery) para desencadenar una conmutación por error.

### <a name="can-i-fail-back-to-a-different-location"></a>¿Es posible conmutar por recuperación en otra ubicación?
Sí, si conmutó por error en Azure, puede conmutar por recuperación en otra ubicación si la ubicación original no está disponible. [Más información](concepts-types-of-failback.md#alternate-location-recovery-alr).

### <a name="why-do-i-need-a-vpn-or-expressroute-to-fail-back"></a>¿Por qué se necesita una VPN o ExpressRoute para conmutar por recuperación?

Cuando conmuta por recuperación desde Azure, los datos provenientes de Azure se copia nuevamente en la máquina virtual local y se requiere acceso privado. 



## <a name="automation-and-scripting"></a>Automatización y scripts

### <a name="can-i-set-up-replication-with-scripting"></a>¿Puedo configurar la replicación con scripts?
Sí. Puede automatizar los flujos de trabajo de Site Recovery mediante la API de Rest, PowerShell o el SDK de Azure.[Más información](vmware-azure-disaster-recovery-powershell.md).

## <a name="performance-and-capacity"></a>Rendimiento y capacidad
### <a name="can-i-throttle-replication-bandwidth"></a>¿Puedo limitar el ancho de banda de replicación?
Sí. [Más información](site-recovery-plan-capacity-vmware.md).


## <a name="next-steps"></a>Pasos siguientes
* [Revise](vmware-physical-azure-support-matrix.md) los requisitos de compatibilidad.
* [Configure](vmware-azure-tutorial.md) la replicación de VMware en Azure.
