---
title: 'Azure Site Recovery: Preguntas más frecuentes (P+F) | Microsoft Docs'
description: En este artículo se analizan las preguntas más frecuentes acerca de Azure Site Recovery.
services: site-recovery
documentationcenter: ''
author: rayne-wiselman
manager: jwhit
editor: ''

ms.service: get-started-article
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 07/12/2016
ms.author: raynew

---
# Azure Site Recovery: preguntas más frecuentes (P+F)
## Información acerca de este artículo
En este artículo se incluyen las preguntas más frecuentes sobre Azure Site Recovery. Si tiene alguna pregunta después de leer el artículo, publíquela en el [Foro de Servicios de recuperación de Azure](https://social.msdn.microsoft.com/Forums/azure/home?forum=hypervrecovmgr).

## General
### ¿Qué hace Site Recovery?
Site Recovery contribuye a su estrategia de continuidad empresarial y recuperación ante desastres mediante la coordinación y la automatización de la replicación desde máquinas virtuales locales y servidores físicos a Azure o a un centro de datos secundario. [Más información](site-recovery-overview.md).

### ¿Qué se puede proteger con Site Recovery?
* **Máquinas virtuales de Hyper-V**: Site Recovery puede proteger cualquier carga de trabajo que se ejecute en una máquina virtual de Hyper-V.
* **Servidores físicos**: Site Recovery puede proteger servidores físicos con Windows o Linux.
* **Máquinas virtuales de VMware**: Site Recovery puede proteger cualquier carga de trabajo que se ejecute en una máquina virtual de VMware.

### ¿Site Recovery admite el modelo de Azure Resource Manager?
Además de Site Recovery en el Portal de Azure clásico, Site Recovery está disponible en el Portal de Azure con compatibilidad para Resource Manager. Para la mayoría de los escenarios de implementación, Site Recovery en el Portal de Azure ofrece una experiencia de implementación optimizada y puede replicar las máquinas virtuales y los servidores físicos en el almacenamiento clásico o el almacenamiento de Resource Manager. Estas son las implementaciones compatibles:

* [Replicación de servidores físicos o máquinas virtuales de VMware en Azure en el Portal de Azure](site-recovery-vmware-to-azure.md)
* [Replicación de máquinas virtuales de Hyper-V en nubes VMM en Azure en el Portal de Azure](site-recovery-vmm-to-azure.md)
* [Replicación de máquinas virtuales de Hyper-V (sin VMM) en Azure en el Portal de Azure](site-recovery-hyper-v-site-to-azure.md)
* [Replicación de máquinas virtuales de Hyper-V en nubes VMM en un sitio secundario en el Portal de Azure](site-recovery-vmm-to-vmm.md)

### ¿Qué necesito en Hyper-V para orquestar la replicación con Site Recovery?
Los requisitos para el servidor host de Hyper-V dependen del escenario de implementación. Revise los requisitos previos de Hyper-V en:

* [Replicación de máquinas virtuales de Hyper-V (sin VMM) a Azure](site-recovery-hyper-v-site-to-azure.md#before-you-start)
* [Replicación de máquinas virtuales de Hyper-V (con VMM) a Azure](site-recovery-vmm-to-azure.md#before-you-start)
* [Replicación de máquinas virtuales de Hyper-V a un centro de datos secundario](site-recovery-vmm-to-vmm.md#before-you-start)
* Si está replicando en un centro de datos secundario, consulte [Sistemas operativos invitados compatibles para máquinas virtuales Hyper-V](https://technet.microsoft.com/library/mt126277.aspx).
* Si está replicando a Azure, Site Recovery es compatible con todos los sistemas operativos invitados [admitidos por Azure](https://technet.microsoft.com/library/cc794868%28v=ws.10%29.aspx).

### ¿Se pueden proteger las máquinas virtuales cuando se ejecuta Hyper-V en un sistema operativo cliente?
No, las máquinas virtuales deben estar ubicadas en un servidor host de Hyper-V en el que se esté ejecutando una máquina de servidor de Windows compatible. Si necesita proteger un equipo cliente podría replicarlo como una máquina física [en Azure](site-recovery-vmware-to-azure.md) o en un [centro de datos secundario](site-recovery-vmware-to-vmware.md).

### ¿Qué cargas de trabajo se pueden proteger con Site Recovery?
Puede usar Site Recovery para proteger la mayoría de las cargas de trabajo que se ejecutan en una máquina virtual o un servidor físico. Site Recovery proporciona compatibilidad para la replicación con reconocimiento de aplicaciones para que estas se puedan recuperar a un estado inteligente. Se integra con aplicaciones de Microsoft como SharePoint, Exchange, Dynamics, SQL Server y Active Directory; además, colabora estrechamente con los principales proveedores, como Oracle, SAP, IBM y Red Hat. [Obtenga más información](site-recovery-workload.md) acerca de la protección de la carga de trabajo.

### ¿Los hosts de Hyper-V deben estar en nubes VMM?
Si desea replicar en un centro de datos secundario, las máquinas virtuales de Hyper-V deben estar en servidores host de Hyper-V ubicados en una nube VMM. Si desea replicar en Azure, puede replicar las máquinas virtuales en los servidores host de Hyper-V con o sin nubes VMM. [Más información](site-recovery-hyper-v-site-to-azure.md)

### ¿Se puede implementar Site Recovery con VMM si solo se tiene un servidor de VMM?
Sí. Puede replicar en Azure las máquinas virtuales que están en servidores de Hyper-V en la nube de VMM, o bien puede replicar entre nubes de VMM en el mismo servidor. Para la replicación de local a local es recomendable que tenga un servidor de VMM tanto en el sitio principal como en el secundario. [Más información](site-recovery-single-vmm.md)

### ¿Qué servidores físicos se pueden proteger?
Puede replicar servidores físicos con Windows y Linux, en Azure o en un sitio secundario. [Obtenga información](site-recovery-vmware-to-azure.md#protected-machine-prerequisites) acerca de los requisitos del sistema operativo. Se aplican los mismos requisitos si está replicando servidores físicos en Azure o en un sitio secundario.

Tenga en cuenta que los servidores físicos se ejecutarán como máquinas virtuales en Azure si se desactiva el servidor local. Actualmente no se admite la conmutación por recuperación a un servidor físico local, pero puede conmutar por recuperación a una máquina virtual que se ejecute en Hyper-V o VMware.

### ¿Qué máquinas virtuales de VMware se pueden proteger?
Para proteger las máquinas virtuales de VMWare, necesitará un hipervisor vSphere y máquinas virtuales que ejecuten herramientas de VMware. También recomendamos que tenga un servidor VMware vCenter para administrar los hipervisores. [Obtenga más información](site-recovery-vmware-to-azure.md#protected-machine-prerequisites) sobre los requisitos exactos para replicar los servidores de VMware y las máquinas virtuales en Azure o en un sitio secundario.

### ¿Es posible administrar la recuperación ante desastres para las sucursales con Site Recovery?
Sí. Si usa Site Recovery para coordinar la replicación y la conmutación por error en las sucursales, disfrutará de una coordinación unificada y una vista de todas las cargas de trabajo de las sucursales desde un mismo punto. Puede ejecutar con facilidad conmutaciones por error y administrar la recuperación ante desastres de todas las sucursales desde la sede central, sin necesidad de visitar estas sucursales.

## Seguridad
### ¿Se envían mis datos de replicación al servicio de Site Recovery?
No, Site Recovery no intercepta los datos replicados ni tiene información sobre lo que se ejecuta en sus máquinas virtuales o servidores físicos. Los datos de replicación se intercambian entre hosts locales de Hyper-V, hipervisores de VMware o servidores físicos y el Almacenamiento de Azure en el sitio secundario. Site Recovery no tiene capacidad para interceptar los datos. Únicamente se envían los metadatos necesarios para coordinar la replicación y la conmutación por error al servicio Site Recovery.

Site Recovery está certificado según la norma ISO 27001:2013, 27018, además de HIPAA y DPA, y está completando sus evaluaciones de SOC2 y FedRAMP JAB.

### Para garantizar el cumplimiento, incluso los metadatos de nuestros entornos locales deben permanecer dentro de la misma región geográfica. ¿Site Recovery puede ayudarnos?
Sí. Al crear un almacén de Site Recovery en una región, garantizamos que todos los metadatos que necesitamos para habilitar y coordinar la replicación y la conmutación por error permanezcan dentro del límite geográfico de esa región.

### ¿Site Recovery cifra la replicación?
Al replicar máquinas virtuales y servidores físicos entre sitios locales, se admite el cifrado en tránsito. Al replicar máquinas virtuales y servidores físicos en Azure, se admite tanto el cifrado en tránsito como el cifrado en reposo (en Azure).

## Replicación
### ¿Hay algún requisito previo para replicar las máquinas virtuales a Azure?
Las máquinas virtuales que quiera replicar a Azure deben cumplir con los [Requisitos de Azure](site-recovery-best-practices.md#virtual-machines).

### ¿Se pueden replicar máquinas virtuales de generación 2 de Hyper-V a Azure?
Sí. Site Recovery convierte de la generación 2 a la generación 1 durante una conmutación por error. En la conmutación por recuperación, la máquina se convierte de nuevo a la generación 2. [Más información](http://azure.microsoft.com/blog/2015/04/28/disaster-recovery-to-azure-enhanced-and-were-listening/).

### Si se replica a Azure ¿cómo se paga por las máquinas virtuales de Azure?
Durante una replicación normal, los datos se replican en el almacenamiento de Azure con redundancia geográfica y no es necesario pagar los cargos de máquina virtual de IaaS de Azure, lo cual proporciona una ventaja considerable. Cuando se realiza una conmutación por error a Azure, Site Recovery crea automáticamente las máquinas virtuales de IaaS de Azure, después de lo cual se le factura por los recursos de proceso que se consumen en Azure.

### ¿Hay un SDK que se pueda usar para automatizar el flujo de trabajo de Site Recovery?
Sí. Puede automatizar los flujos de trabajo de Site Recovery mediante la API de Rest, PowerShell o el SDK de Azure. Escenarios admitidos actualmente para la implementación de Site Recovery con PowerShell:

* [Replicación de máquinas virtuales de Hyper-V en nubes VMM en Azure PowerShell clásico](site-recovery-deploy-with-powershell.md)
* [Replicación de máquinas virtuales de Hyper-V en nubes VMM en Azure PowerShell Resource Manager](site-recovery-vmm-to-azure-powershell-resource-manager.md)
* [Replicación de máquinas virtuales de Hyper-V sin VMM en Azure PowerShell clásico](site-recovery-hyper-v-site-to-azure-classic.md)
* [Replicación de máquinas virtuales de Hyper-V sin VMM en Azure PowerShell Resource Manager](site-recovery-deploy-with-powershell-resource-manager.md)

### Si se replica a Azure, ¿qué tipo de cuenta de almacenamiento se necesita?
* **Portal de Azure clásico**: si está implementando Site Recovery en el Portal de Azure clásico, necesitará una [cuenta de almacenamiento estándar con redundancia geográfica](../storage/storage-redundancy.md#geo-redundant-storage). El Almacenamiento premium no se admite actualmente. La cuenta debe estar en la misma región que el almacén de Site Recovery.
* **Portal de azure**: si está implementando Site Recovery en el Portal de Azure, necesitará una cuenta de almacenamiento LRS o GRS. Se recomienda GRS para que los datos sean resistentes si se produce una interrupción regional o si no se puede recuperar la región principal. La cuenta debe estar en la misma región que el almacén de Servicios de recuperación. Actualmente se admite el almacenamiento premium solo si está replicando servidores físicos o máquinas virtuales de VMware.

### ¿Con qué frecuencia se pueden replicar los datos?
* **Hyper-V:** las máquinas virtuales de Hyper-V se pueden replicar cada 30 segundos, 5 minutos o 15 minutos. Si ha configurado la replicación de SAN, la replicación es sincrónica.
* **VMware y servidores físicos:** en este caso no es relevante la frecuencia de replicación. La replicación es continua.

### ¿Se puede ampliar la replicación desde el sitio de recuperación existente a otro tercer sitio?
No se admite la replicación extendida o encadenada. Solicite esta característica en el [foro de comentarios](http://feedback.azure.com/forums/256299-site-recovery/suggestions/6097959-support-for-exisiting-extended-replication).

### ¿Se puede hacer una replicación sin conexión la primera vez que se replique en Azure?
No es una opción admitida. Solicite esta característica en el [foro de comentarios](http://feedback.azure.com/forums/256299-site-recovery/suggestions/6227386-support-for-offline-replication-data-transfer-from).

### ¿Se pueden excluir discos específicos de la replicación?
Esto se admite cuando está [replicando servidores físicos y máquinas virtuales de VMware](site-recovery-vmware-to-azure.md#exclude-disks-from-replication) en Azure mediante el Portal de Azure.

### ¿Se pueden replicar máquinas virtuales con discos dinámicos?
Los discos dinámicos se admiten al replicar máquinas virtuales de Hyper-V. También se admiten al replicar máquinas virtuales de VMware y máquinas físicas en Azure si utiliza el [Portal de Azure](site-recovery-vmware-to-azure.md) o el [Portal de Azure clásico con la implementación mejorada](site-recovery-vmware-to-azure-classic.md). Tenga en cuenta que el disco del sistema operativo debe ser un disco básico.

### ¿Puedo limitar el ancho de banda asignado para el tráfico de replicación de Hyper-V?
Sí. Puede leer más acerca de la limitación de ancho de banda en los artículos de implementación donde se tratan los siguientes temas:

* [Capacity planning for replicating VMware VMs and physical servers](site-recovery-vmware-to-azure.md#step-5-capacity-planning) (Planeamiento de capacidad para la replicación de servidores físicos y máquinas virtuales de VMware)
* [Capacity planning for replicating Hyper-V VMs in VMM clouds](site-recovery-vmm-to-azure.md#step-5-capacity-planning) (Planeamiento de capacidad para la replicación de máquinas virtuales de Hyper-V en nubes VMM)
* [Capacity planning for replicating Hyper-V VMs without VMM](site-recovery-hyper-v-site-to-azure.md#step-5-capacity-planning) (Planeamiento de capacidad para la replicación de máquinas virtuales de Hyper-V sin VMM)

## Conmutación por error
### Si se realiza una conmutación por error a Azure, ¿cómo se puede tener acceso a las máquinas virtuales de Azure tras este proceso?
Es posible tener acceso a las máquinas virtuales de Azure a través de una conexión segura a Internet o a través de una VPN de sitio a sitio o mediante Azure ExpressRoute. Debe preparar algunas cosas para la conexión. Obtenga más información en los artículos siguientes:

* [Connect to Azure VMs after failover of VMware VMs or physical servers](hsite-recovery-vmware-to-azure.md#step-7-test-the-deployment) (Conexión a máquinas virtuales de Azure tras la conmutación por error de servidores físicos o máquinas virtuales de VMware)
* [Connect to Azure VMs after failover of Hyper-V VMs in VMM clouds](site-recovery-vmm-to-azure.md#step-7-test-your-deployment) (Conexión a VM de Azure tras la conmutación por error de máquinas virtuales de Hyper-V en nubes VMM)
* [Connect to Azure VMs after failover of Hyper-V VMs without VMM](site-recovery-hyper-v-site-to-azure.md#step-7-test-the-deployment) (Conexión a máquinas virtuales de Azure tras la conmutación por error de máquinas virtuales de Hyper-V sin VMM)

### Si se realiza una conmutación por error a Azure, ¿cómo se asegura Azure de que los datos resistan el proceso?
Azure está diseñado para la resistencia. Site Recovery ya se ha diseñado para la conmutación por error en un centro de datos de Azure secundario según el SLA de Azure por si surge la necesidad de hacerlo. Si esto ocurre, nos aseguraremos de que los metadatos y los almacenes permanecen en la misma región geográfica que eligió para su almacén.

### Si se replica entre dos centros de datos, ¿qué ocurre si el centro de datos principal experimenta una interrupción inesperada?
Puede desencadenar una conmutación por error no planeada desde el sitio secundario. Site Recovery no necesita conectividad desde el sitio principal para realizar la conmutación por error.

### ¿La conmutación por error es automática?
La conmutación por error no es automática. Puede iniciar las conmutaciones por error con solo un clic en el portal o bien puede usar [Site Recovery PowerShell](https://msdn.microsoft.com/library/dn850420.aspx) para desencadenar una conmutación por error. La conmutación por recuperación también es una acción sencilla en el portal de Site Recovery.

Para automatizar estos procesos, puede utilizar Orchestrator u Operations Manager locales para detectar un error de la máquina virtual y desencadenar luego la conmutación por error mediante el SDK.

* [Obtenga más información](site-recovery-create-recovery-plans.md) sobre los planes de recuperación.
* [Más información](site-recovery-failover.md) acerca de la conmutación por error.
* [Más información](site-recovery-failback-azure-to-vmware.md) acerca de la conmutación por recuperación de servidores físicos y máquinas virtuales de VMware

## Proveedores de servicios
### Soy un proveedor de servicios. ¿Site Recovery funciona para los modelos de infraestructura compartida o dedicada?
Sí, Site Recovery admite ambos modelos de infraestructura, dedicados y compartidos.

### Para un proveedor de servicios, ¿la identidad de mi inquilino se comparte con el servicio Site Recovery?
No. La identidad del inquilino permanece anónima. Los inquilinos no necesitan acceso al portal de Site Recovery. Solo el administrador del proveedor de servicios realiza acciones en el portal.

### ¿Los datos de la aplicación de mis inquilinos llegarán a Azure?
Cuando se replica entre sitios que pertenecen al proveedor de servicios, los datos de la aplicación nunca llegan a Azure. Los datos se cifran en tránsito y se replican directamente entre los sitios del proveedor de servicios.

Si está replicando a Azure, los datos de la aplicación se envían al almacenamiento de Azure, pero no al servicio Site Recovery. Los datos se cifran en tránsito y permanecen cifrados en Azure.

### ¿Recibirán mis inquilinos una factura por los servicios de Azure?
No. La relación de facturación de Azure se entabla directamente con el proveedor de servicios. Los proveedores de servicios son responsables de generar facturas específicas para sus inquilinos.

### Si se está replicando a Azure, ¿es necesario ejecutar máquinas virtuales en Azure en todo momento?
No, los datos se replican en una cuenta de almacenamiento de Azure en su suscripción. Al realizar una conmutación por error de prueba (obtención de detalles de recuperación ante desastres) o una conmutación por error real, Site Recovery crea automáticamente las máquinas virtuales en su suscripción.

### ¿Se puede garantizar el aislamiento a nivel de inquilino al replicar a Azure?
Sí.

### ¿Qué plataformas se admiten actualmente?
Se admite Azure Pack, el sistema de plataforma en la nube e implementaciones basadas en System Center (2012 y superiores). [Más información](https://technet.microsoft.com/library/dn850370.aspx) acerca de la integración de Azure Pack y Site Recovery.

### ¿Se admite un paquete Azure Pack sencillo e implementaciones de servidor VMM individuales?
Sí, es posible replicar máquinas virtuales de Hyper-V en Azure, o replicar entre sitios del proveedor de servicios. Tenga en cuenta que si se replica entre sitios del proveedor de servicios, la integración de runbooks de Azure no estará disponible.

## Pasos siguientes
* Lea la [Información general sobre Site Recovery](site-recovery-overview.md)
* Obtenga información acerca de la [Arquitectura de Site Recovery](site-recovery-components.md)

<!---HONumber=AcomDC_0720_2016-->