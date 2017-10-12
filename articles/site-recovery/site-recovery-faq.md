---
title: "Azure Site Recovery: Preguntas más frecuentes (P+F) | Microsoft Docs"
description: "En este artículo se analizan las preguntas más frecuentes acerca de Azure Site Recovery."
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: 5cdc4bcd-b4fe-48c7-8be1-1db39bd9c078
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 05/22/2017
ms.author: raynew
ms.openlocfilehash: 95e31d0ca5983e0946ad6fb993e7a89a6a63d2c3
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="azure-site-recovery-frequently-asked-questions-faq"></a>Azure Site Recovery: preguntas más frecuentes (P+F)
En este artículo se incluyen las preguntas más frecuentes sobre Azure Site Recovery. Si tiene alguna pregunta después de leer el artículo, publíquela en el [Foro de Servicios de recuperación de Azure](https://social.msdn.microsoft.com/Forums/azure/home?forum=hypervrecovmgr).

## <a name="general"></a>General
### <a name="what-does-site-recovery-do"></a>¿Qué hace Site Recovery?
Site Recovery contribuye a su estrategia de continuidad empresarial (BCDR por su sigla en inglés) y recuperación ante desastres mediante la coordinación y la automatización de la replicación de máquinas virtuales de Azure entre regiones, máquinas virtuales locales y servidores físicos a Azure, y máquinas locales a un centro de datos secundario. [Más información](site-recovery-overview.md).

### <a name="what-can-site-recovery-protect"></a>¿Qué se puede proteger con Site Recovery?
* **Máquinas virtuales de Azure**: Site Recovery puede replicar cualquier carga de trabajo que se ejecute en una máquina virtual de Azure compatible.
* **Máquinas virtuales de Hyper-V**: Site Recovery puede proteger cualquier carga de trabajo que se ejecute en una máquina virtual de Hyper-V.
* **Servidores físicos**: Site Recovery puede proteger servidores físicos con Windows o Linux.
* **Máquinas virtuales de VMware**: Site Recovery puede proteger cualquier carga de trabajo que se ejecute en una máquina virtual de VMware.

### <a name="does-site-recovery-support-the-azure-resource-manager-model"></a>¿Site Recovery admite el modelo de Azure Resource Manager?
Site Recovery está disponible en Azure Portal y es compatible con Resource Manager. Site Recovery admite implementaciones heredadas en el Portal de Azure clásico. No se puede crear almacenes nuevos en el portal clásico y no se admiten nuevas características.

### <a name="can-i-replicate-azure-vms"></a>¿Puedo replicar máquinas virtuales de Azure?
Sí, puede replicar máquinas virtuales de Azure compatibles entre regiones de Azure. [Más información](site-recovery-azure-to-azure.md).

### <a name="what-do-i-need-in-hyper-v-to-orchestrate-replication-with-site-recovery"></a>¿Qué necesito en Hyper-V para orquestar la replicación con Site Recovery?
Los requisitos para el servidor host de Hyper-V dependen del escenario de implementación. Revise los requisitos previos de Hyper-V en:

* [Replicación de máquinas virtuales de Hyper-V (sin VMM) a Azure](site-recovery-hyper-v-site-to-azure.md)
* [Replicación de máquinas virtuales de Hyper-V (con VMM) a Azure](site-recovery-vmm-to-azure.md)
* [Replicación de máquinas virtuales de Hyper-V a un centro de datos secundario](site-recovery-vmm-to-vmm.md)
* Si está replicando en un centro de datos secundario, consulte [Sistemas operativos invitados compatibles para máquinas virtuales Hyper-V](https://technet.microsoft.com/library/mt126277.aspx).
* Si está replicando a Azure, Site Recovery es compatible con todos los sistemas operativos invitados [admitidos por Azure](https://technet.microsoft.com/library/cc794868%28v=ws.10%29.aspx).

### <a name="can-i-protect-vms-when-hyper-v-is-running-on-a-client-operating-system"></a>¿Se pueden proteger las máquinas virtuales cuando se ejecuta Hyper-V en un sistema operativo cliente?
No, las máquinas virtuales deben estar ubicadas en un servidor host de Hyper-V en el que se esté ejecutando una máquina de servidor de Windows compatible. Si necesita proteger un equipo cliente, podría replicarlo como una máquina física [en Azure](site-recovery-vmware-to-azure.md) o en un [centro de datos secundario](site-recovery-vmware-to-vmware.md).

### <a name="what-workloads-can-i-protect-with-site-recovery"></a>¿Qué cargas de trabajo se pueden proteger con Site Recovery?
Puede usar Site Recovery para proteger la mayoría de las cargas de trabajo que se ejecutan en una máquina virtual o un servidor físico. Site Recovery proporciona compatibilidad para la replicación con reconocimiento de aplicaciones para que estas se puedan recuperar a un estado inteligente. Se integra con aplicaciones de Microsoft como SharePoint, Exchange, Dynamics, SQL Server y Active Directory; además, colabora estrechamente con los principales proveedores, como Oracle, SAP, IBM y Red Hat. [Obtenga más información](site-recovery-workload.md) acerca de la protección de la carga de trabajo.

### <a name="do-hyper-v-hosts-need-to-be-in-vmm-clouds"></a>¿Los hosts de Hyper-V deben estar en nubes VMM?
Si desea replicar en un centro de datos secundario, las máquinas virtuales de Hyper-V deben estar en servidores host de Hyper-V ubicados en una nube VMM. Si desea replicar en Azure, puede replicar las máquinas virtuales en los servidores host de Hyper-V con o sin nubes VMM. [Más información](site-recovery-hyper-v-site-to-azure.md).

### <a name="can-i-deploy-site-recovery-with-vmm-if-i-only-have-one-vmm-server"></a>¿Se puede implementar Site Recovery con VMM si solo se tiene un servidor de VMM?

Sí. Puede replicar en Azure las máquinas virtuales que están en servidores de Hyper-V en la nube de VMM, o bien puede replicar entre nubes de VMM en el mismo servidor. Para la replicación de local a local es recomendable que tenga un servidor de VMM tanto en el sitio principal como en el secundario.  

### <a name="what-physical-servers-can-i-protect"></a>¿Qué servidores físicos se pueden proteger?
Puede replicar servidores físicos con Windows y Linux, en Azure o en un sitio secundario. [Obtenga información](site-recovery-support-matrix-to-azure.md#failed-over-azure-vm-requirements) acerca de los requisitos del sistema operativo.  Se aplican los mismos requisitos si está replicando servidores físicos en Azure o en un sitio secundario.


Tenga en cuenta que los servidores físicos se ejecutarán como máquinas virtuales en Azure si se desactiva el servidor local. Actualmente, no se admite la conmutación por recuperación a un servidor físico local. Para una máquina protegida como física, solo se puede conmutar por recuperación a una máquina virtual de VMware.

### <a name="what-vmware-vms-can-i-protect"></a>¿Qué máquinas virtuales de VMware se pueden proteger?

Para proteger las máquinas virtuales de VMWare, necesitará un hipervisor vSphere y máquinas virtuales que ejecuten herramientas de VMware. También recomendamos que tenga un servidor VMware vCenter para administrar los hipervisores. [Obtenga más información](site-recovery-support-matrix-to-azure.md#failed-over-azure-vm-requirements) sobre los requisitos exactos para replicar los servidores de VMware y las máquinas virtuales en Azure o en un sitio secundario.


### <a name="can-i-manage-disaster-recovery-for-my-branch-offices-with-site-recovery"></a>¿Es posible administrar la recuperación ante desastres para las sucursales con Site Recovery?
Sí. Si usa Site Recovery para coordinar la replicación y la conmutación por error en las sucursales, disfrutará de una coordinación unificada y una vista de todas las cargas de trabajo de las sucursales desde un mismo punto. Puede ejecutar con facilidad conmutaciones por error y administrar la recuperación ante desastres de todas las sucursales desde la sede central, sin necesidad de visitar estas sucursales.

## <a name="pricing"></a>Precios

### <a name="what-charges-do-i-incur-while-using-azure-site-recovery"></a>¿En qué cargos se incurre al utilizar Azure Site Recovery?
Al usar Site Recovery, se producen cargos por la licencia de Site Recovery, Azure Storage, las transacciones de almacenamiento y la transferencia de datos de salida. [Más información](https://azure.microsoft.com/pricing/details/site-recovery).

La licencia de Site Recovery es para cada instancia protegida, y una instancia es una máquina virtual o un servidor físico.

- Si un disco de máquina virtual se replica en una cuenta de almacenamiento estándar, Azure Storage cobra por el consumo de almacenamiento. Por ejemplo, si el tamaño del disco de origen es 1 TB y se usan 400 GB, Site Recovery crea un disco duro virtual de 1 TB en Azure, pero se cobra por un almacenamiento de 400 GB (más la cantidad de espacio de almacenamiento que se usa para los registros de replicación).
- Si un disco de máquina virtual se replica en una cuenta de almacenamiento premium, Azure Storage cobra por el tamaño de almacenamiento aprovisionado, redondeado a la opción de disco de almacenamiento premium más cercana. Por ejemplo, si el tamaño del disco de origen es de 50 GB, Site Recovery crea un disco de 50 GB en Azure y Azure lo asigna al disco de almacenamiento premium (P10) más cercano.  Los costes se calculan sobre P10 y no sobre el tamaño de disco de 50 GB.  [Más información](https://aka.ms/premium-storage-pricing).  Si usa almacenamiento premium, también se necesita una cuenta de almacenamiento estándar para el registro de la replicación, y también se factura la cantidad de espacio de almacenamiento estándar usado para estos registros.
- No se crean discos hasta que se produce una conmutación por error de prueba o una conmutación por error. En el estado de replicación, se incurre en los cargos de almacenamiento en la categoría de "Disco y blob en páginas" según la [Calculadora de precios de Storage](https://azure.microsoft.com/en-in/pricing/calculator/). Estos cargos se basan en el tipo almacenamiento premium o estándar y en el tipo de redundancia de datos (LRS, GRS, RA-GRS, etc.).
- Si se selecciona la opción para usar discos administrados al producirse una conmutación por error, se aplican los [cargos por discos administrados](https://azure.microsoft.com/en-in/pricing/details/managed-disks/) después de una conmutación por error o una conmutación por error de prueba. No se aplican los cargos por discos administrados durante la replicación.
- Si no se selecciona la opción para usar discos administrados al producirse una conmutación por error, tras la conmutación por error se incurre en los cargos de almacenamiento en la categoría de "Disco y blob en páginas" según la [Calculadora de precios de almacenamiento](https://azure.microsoft.com/en-in/pricing/calculator/). Estos cargos se basan en el tipo almacenamiento premium o estándar y en el tipo de redundancia de datos (LRS, GRS, RA-GRS, etc.).
- Las transacciones de almacenamiento se cargan durante la replicación en estado estable y por las operaciones normales de máquina virtual después de una conmutación por error o conmutación por error de prueba. Pero estos cargos son insignificantes.

También se incurre en costes durante la conmutación por error de prueba, y se aplicarán los costes de las transacciones de VM, almacenamiento, salida y almacenamiento.



## <a name="security"></a>Seguridad
### <a name="is-replication-data-sent-to-the-site-recovery-service"></a>¿Se envían mis datos de replicación al servicio de Site Recovery?
No, Site Recovery no intercepta los datos replicados ni tiene información sobre lo que se ejecuta en sus máquinas virtuales o servidores físicos.
Los datos de replicación se intercambian entre hosts locales de Hyper-V, hipervisores de VMware o servidores físicos y el Almacenamiento de Azure en el sitio secundario. Site Recovery no tiene capacidad para interceptar los datos. Únicamente se envían los metadatos necesarios para coordinar la replicación y la conmutación por error al servicio Site Recovery.  

Site Recovery está certificado según la norma ISO 27001:2013, 27018, además de HIPAA y DPA, y está completando sus evaluaciones de SOC2 y FedRAMP JAB.

### <a name="for-compliance-reasons-even-our-on-premises-metadata-must-remain-within-the-same-geographic-region-can-site-recovery-help-us"></a>Para garantizar el cumplimiento, incluso los metadatos de nuestros entornos locales deben permanecer dentro de la misma región geográfica. ¿Site Recovery puede ayudarnos?
Sí. Al crear un almacén de Site Recovery en una región, garantizamos que todos los metadatos que necesitamos para habilitar y coordinar la replicación y la conmutación por error permanezcan dentro del límite geográfico de esa región.

### <a name="does-site-recovery-encrypt-replication"></a>¿Site Recovery cifra la replicación?
Al replicar máquinas virtuales y servidores físicos entre sitios locales, se admite el cifrado en tránsito. En el caso de las máquinas virtuales y los servidores físicos que se replican en Azure, se admite tanto el cifrado en tránsito como el [cifrado en reposo (en Azure)](https://docs.microsoft.com/en-us/azure/storage/storage-service-encryption).

## <a name="replication"></a>Replicación

### <a name="can-i-replicate-over-a-site-to-site-vpn-to-azure"></a>¿Puedo replicar a través de una VPN de sitio a sitio en Azure?
Azure Site Recovery replica los datos en una cuenta de almacenamiento de Azure a través de un punto de conexión público. La replicación no se realiza a través de una VPN de sitio a sitio. Puede crear una VPN de sitio a sitio con una instancia de Azure Virtual Network. Esto no interfiere con la replicación de Site Recovery.

### <a name="can-i-use-expressroute-to-replicate-virtual-machines-to-azure"></a>¿Puedo usar ExpressRoute para replicar máquinas virtuales en Azure?
Sí, se puede usar ExpressRoute para replicar máquinas virtuales en Azure. Azure Site Recovery replica los datos en una cuenta de almacenamiento de Azure a través de un punto de conexión público. Es necesario configurar el [emparejamiento público](../expressroute/expressroute-circuit-peerings.md#azure-public-peering) para usar ExpressRoute para la replicación de Site Recovery. Una vez que las máquinas virtuales se hayan conmutado por error a una red virtual de Azure,podrá acceder a ellas mediante la configuración [entre pares privados](../expressroute/expressroute-circuit-peerings.md#azure-private-peering) con la red virtual de Azure.

### <a name="are-there-any-prerequisites-for-replicating-virtual-machines-to-azure"></a>¿Hay algún requisito previo para replicar las máquinas virtuales a Azure?
Las máquinas virtuales que quiera replicar a Azure deben cumplir con los [Requisitos de Azure](site-recovery-support-matrix-to-azure.md#failed-over-azure-vm-requirements).

Su cuenta de usuario de Azure debe tener ciertos [permisos](site-recovery-role-based-linked-access-control.md#permissions-required-to-enable-replication-for-new-virtual-machines) para habilitar la replicación de una nueva máquina virtual en Azure.

### <a name="can-i-replicate-hyper-v-generation-2-virtual-machines-to-azure"></a>¿Se pueden replicar máquinas virtuales de generación 2 de Hyper-V a Azure?
Sí. Site Recovery convierte de la generación 2 a la generación 1 durante una conmutación por error. En la conmutación por recuperación, la máquina se convierte de nuevo a la generación 2. [Más información](http://azure.microsoft.com/blog/2015/04/28/disaster-recovery-to-azure-enhanced-and-were-listening/).

### <a name="if-i-replicate-to-azure-how-do-i-pay-for-azure-vms"></a>Si se replica a Azure ¿cómo se paga por las máquinas virtuales de Azure?
Durante una replicación normal, los datos se replican en el almacenamiento de Azure con redundancia geográfica y no es necesario pagar los cargos de máquina virtual de IaaS de Azure, lo cual proporciona una ventaja considerable. Cuando se realiza una conmutación por error a Azure, Site Recovery crea automáticamente las máquinas virtuales de IaaS de Azure, después de lo cual se le factura por los recursos de proceso que se consumen en Azure.

### <a name="can-i-automate-site-recovery-scenarios-with-an-sdk"></a>¿Se pueden automatizar escenarios de Site Recovery con un SDK?
Sí. Puede automatizar los flujos de trabajo de Site Recovery mediante la API de Rest, PowerShell o el SDK de Azure. Escenarios admitidos actualmente para la implementación de Site Recovery con PowerShell:

* [Replicación de máquinas virtuales de Hyper-V en nubes VMM en Azure PowerShell Resource Manager](site-recovery-vmm-to-azure-powershell-resource-manager.md)
* [Replicación de máquinas virtuales de Hyper-V sin VMM en Azure PowerShell Resource Manager](site-recovery-deploy-with-powershell-resource-manager.md)

### <a name="if-i-replicate-to-azure-what-kind-of-storage-account-do-i-need"></a>Si se replica a Azure, ¿qué tipo de cuenta de almacenamiento se necesita?
* **Portal de Azure clásico**: si está implementando Site Recovery en el Portal de Azure clásico, necesitará una [cuenta de almacenamiento estándar con redundancia geográfica](../storage/common/storage-redundancy.md#geo-redundant-storage). El Almacenamiento premium no se admite actualmente. La cuenta debe estar en la misma región que el almacén de Site Recovery.
* **Azure Portal**: si está implementando Site Recovery en Azure Portal, necesitará una cuenta de almacenamiento LRS o GRS. Se recomienda GRS para que los datos sean resistentes si se produce una interrupción regional o si no se puede recuperar la región principal. La cuenta debe estar en la misma región que el almacén de Servicios de recuperación. Premium Storage ahora es compatible con máquinas virtuales de VMware, con máquinas virtuales de Hyper-V VM y con la replicación de servidores físicos si implementa Site Recovery en Azure Portal.

### <a name="how-often-can-i-replicate-data"></a>¿Con qué frecuencia se pueden replicar los datos?
* **Hyper-V:** las máquinas virtuales de Hyper-V se pueden replicar cada 30 segundos (excepto en el caso de Premium Storage), 5 minutos o 15 minutos. Si ha configurado la replicación de SAN, la replicación es sincrónica.
* **VMware y servidores físicos:** en este caso no es relevante la frecuencia de replicación. La replicación es continua.

### <a name="can-i-extend-replication-from-existing-recovery-site-to-another-tertiary-site"></a>¿Se puede ampliar la replicación desde el sitio de recuperación existente a otro tercer sitio?
No se admite la replicación extendida o encadenada. Solicite esta característica en el [foro de comentarios](http://feedback.azure.com/forums/256299-site-recovery/suggestions/6097959-support-for-exisiting-extended-replication).

### <a name="can-i-do-an-offline-replication-the-first-time-i-replicate-to-azure"></a>¿Se puede hacer una replicación sin conexión la primera vez que se replique en Azure?
No es una opción admitida. Solicite esta característica en el [foro de comentarios](http://feedback.azure.com/forums/256299-site-recovery/suggestions/6227386-support-for-offline-replication-data-transfer-from).

### <a name="can-i-exclude-specific-disks-from-replication"></a>¿Se pueden excluir discos específicos de la replicación?
Esto se admite cuando [replica máquinas virtuales de VMware e Hyper-V](site-recovery-exclude-disk.md) en Azure a través de Azure Portal.

### <a name="can-i-replicate-virtual-machines-with-dynamic-disks"></a>¿Se pueden replicar máquinas virtuales con discos dinámicos?
Los discos dinámicos se admiten al replicar máquinas virtuales de Hyper-V. También se admiten al replicar las máquinas virtuales de VMware y los equipos físicos en Azure. El disco del sistema operativo debe ser un disco básico.

### <a name="can-i-add-a-new-machine-to-an-existing-replication-group"></a>¿Puedo agregar un nuevo equipo a un grupo de replicación existente?
Puede agregar nuevos equipos a grupos de replicación existentes. Para ello, seleccione el grupo de replicación (en la hoja Elementos replicados) y haga clic con el botón derecho en el menú contextual del grupo de replicación, y seleccione la opción adecuada.

![Incorporación al grupo de replicación](./media/site-recovery-faq/add-server-replication-group.png)

### <a name="can-i-throttle-bandwidth-allotted-for-hyper-v-replication-traffic"></a>¿Puedo limitar el ancho de banda asignado para el tráfico de replicación de Hyper-V?
Sí. Puede leer más acerca de la limitación de ancho de banda en los artículos de implementación donde se tratan los siguientes temas:

* [Capacity planning for replicating VMware VMs and physical servers](site-recovery-plan-capacity-vmware.md)
* [Capacity planning for replicating Hyper-V VMs in VMM clouds](site-recovery-vmm-to-azure.md#capacity-planning)
* [Capacity planning for replicating Hyper-V VMs without VMM](site-recovery-hyper-v-site-to-azure.md)

## <a name="failover"></a>Conmutación por error
### <a name="if-im-failing-over-to-azure-how-do-i-access-the-azure-virtual-machines-after-failover"></a>Si se realiza una conmutación por error a Azure, ¿cómo se puede tener acceso a las máquinas virtuales de Azure tras este proceso?
Es posible tener acceso a las máquinas virtuales de Azure a través de una conexión segura a Internet o a través de una VPN de sitio a sitio o mediante Azure ExpressRoute. Debe preparar algunas cosas para la conexión. [Más información](site-recovery-test-failover-to-azure.md#prepare-to-connect-to-azure-vms-after-failover)


### <a name="if-i-fail-over-to-azure-how-does-azure-make-sure-my-data-is-resilient"></a>Si se realiza una conmutación por error a Azure, ¿cómo se asegura Azure de que los datos resistan el proceso?
Azure está diseñado para la resistencia. Site Recovery ya se ha diseñado para la conmutación por error en un centro de datos de Azure secundario según el Acuerdo de Nivel de Servicio de Azure por si surge la necesidad de hacerlo. Si esto ocurre, nos aseguraremos de que los metadatos y los almacenes permanecen en la misma región geográfica que eligió para su almacén.  

### <a name="if-im-replicating-between-two-datacenters-what-happens-if-my-primary-datacenter-experiences-an-unexpected-outage"></a>Si se replica entre dos centros de datos, ¿qué ocurre si el centro de datos principal experimenta una interrupción inesperada?
Puede desencadenar una conmutación por error no planeada desde el sitio secundario. Site Recovery no necesita conectividad desde el sitio principal para realizar la conmutación por error.

### <a name="is-failover-automatic"></a>¿La conmutación por error es automática?
La conmutación por error no es automática. Puede iniciar las conmutaciones por error con solo un clic en el portal o bien puede usar [Site Recovery PowerShell](/powershell/module/azurerm.siterecovery) para desencadenar una conmutación por error. La conmutación por recuperación también es una acción sencilla en el portal de Site Recovery.

Para automatizar estos procesos, puede utilizar Orchestrator u Operations Manager locales para detectar un error de la máquina virtual y desencadenar luego la conmutación por error mediante el SDK.

* [Obtenga más información](site-recovery-create-recovery-plans.md) sobre los planes de recuperación.
* [Más información](site-recovery-failover.md) acerca de la conmutación por error.
* [Más información](site-recovery-failback-azure-to-vmware.md) acerca de la conmutación por recuperación de servidores físicos y máquinas virtuales de VMware

### <a name="if-my-on-premises-host-is-not-responding-or-crashed-can-i-failover-back-to-a-different-host"></a>¿Si mi host local no responde o se ha bloqueado, puedo conmutar por error a otro host?
Sí, puede usar la recuperación en una ubicación alternativa para realizar la conmutación por recuperación a otro host de Azure. Lea más sobre las opciones de los vínculos siguientes para las máquinas virtuales de Hyper-v y VMware.

* [Para máquinas virtuales de VMware](site-recovery-how-to-failback-azure-to-vmware.md#fail-back-to-the-original-or-alternate-location)
* [Para máquinas virtuales de Hyper-v](site-recovery-failback-from-azure-to-hyper-v.md#failback-to-an-alternate-location)

## <a name="service-providers"></a>Proveedores de servicios
### <a name="im-a-service-provider-does-site-recovery-work-for-dedicated-and-shared-infrastructure-models"></a>Soy un proveedor de servicios. ¿Site Recovery funciona para los modelos de infraestructura compartida o específica?
Sí, Site Recovery admite ambos modelos de infraestructura, dedicados y compartidos.

### <a name="for-a-service-provider-is-the-identity-of-my-tenant-shared-with-the-site-recovery-service"></a>Para un proveedor de servicios, ¿la identidad de mi inquilino se comparte con el servicio Site Recovery?
No. La identidad del inquilino permanece anónima. Los inquilinos no necesitan acceso al portal de Site Recovery. Solo el administrador del proveedor de servicios realiza acciones en el portal.

### <a name="will-tenant-application-data-ever-go-to-azure"></a>¿Los datos de la aplicación de mis inquilinos llegarán a Azure?
Cuando se replica entre sitios que pertenecen al proveedor de servicios, los datos de la aplicación nunca llegan a Azure. Los datos se cifran en tránsito y se replican directamente entre los sitios del proveedor de servicios.

Si está replicando a Azure, los datos de la aplicación se envían al almacenamiento de Azure, pero no al servicio Site Recovery. Los datos se cifran en tránsito y permanecen cifrados en Azure.

### <a name="will-my-tenants-receive-a-bill-for-any-azure-services"></a>¿Recibirán mis inquilinos una factura por los servicios de Azure?
No. La relación de facturación de Azure se entabla directamente con el proveedor de servicios. Los proveedores de servicios son responsables de generar facturas específicas para sus inquilinos.

### <a name="if-im-replicating-to-azure-do-we-need-to-run-virtual-machines-in-azure-at-all-times"></a>Si se está replicando a Azure, ¿es necesario ejecutar máquinas virtuales en Azure en todo momento?
No, los datos se replican en una cuenta de almacenamiento de Azure en su suscripción. Al realizar una conmutación por error de prueba (obtención de detalles de recuperación ante desastres) o una conmutación por error real, Site Recovery crea automáticamente las máquinas virtuales en su suscripción.

### <a name="do-you-ensure-tenant-level-isolation-when-i-replicate-to-azure"></a>¿Se puede garantizar el aislamiento a nivel de inquilino al replicar a Azure?
Sí.

### <a name="what-platforms-do-you-currently-support"></a>¿Qué plataformas se admiten actualmente?
Se admite Azure Pack, el sistema de plataforma en la nube e implementaciones basadas en System Center (2012 y superiores). [Más información](https://technet.microsoft.com/library/dn850370.aspx) acerca de la integración de Azure Pack y Site Recovery.

### <a name="do-you-support-single-azure-pack-and-single-vmm-server-deployments"></a>¿Se admite un paquete Azure Pack sencillo e implementaciones de servidor VMM individuales?
Sí, es posible replicar máquinas virtuales de Hyper-V en Azure, o entre sitios del proveedor de servicios.  Tenga en cuenta que si se replica entre sitios del proveedor de servicios, la integración de runbooks de Azure no estará disponible.

## <a name="next-steps"></a>Pasos siguientes
* Lea la [Información general sobre Site Recovery](site-recovery-overview.md)
* Obtenga información acerca de la [Arquitectura de Site Recovery](site-recovery-components.md)  
