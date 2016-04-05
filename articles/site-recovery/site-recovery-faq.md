<properties 
	pageTitle="Azure Site Recovery: Preguntas más frecuentes (P+F) | Microsoft Azure" 
	description="En este artículo se responde a las preguntas más frecuentes acerca de Azure Site Recovery."
	services="site-recovery" 
	documentationCenter=""
	authors="rayne-wiselman"
	manager="jwhit"
	editor=""/>

<tags 
	ms.service="site-recovery"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.tgt_pltfrm="na" 
	ms.workload="storage-backup-recovery"
	ms.date="03/27/2016"
	ms.author="raynew"/>


# Azure Site Recovery: preguntas más frecuentes (P+F)

Lea este artículo para revisar las preguntas más frecuentes sobre el servicio Azure Site Recovery.

Si aún tiene alguna pregunta después de leer este artículo, publíquela en la parte inferior de este artículo o en el [Foro de servicios de recuperación de Azure](https://social.msdn.microsoft.com/Forums/azure/home?forum=hypervrecovmgr).

## General

### ¿Qué hace Site Recovery?

Site Recovery contribuye a su estrategia de continuidad empresarial y recuperación ante desastres mediante la coordinación y la automatización de la replicación desde máquinas virtuales locales y servidores físicos a Azure o a un centro de datos secundario. [Más información](site-recovery-overview.md).

### ¿Qué puede replicar Site Recovery?

- **Máquinas virtuales de Hyper-V**: Site Recovery puede replicar cualquier carga de trabajo que se ejecute en una máquina virtual de Hyper-V compatible. 
- **Servidores físicos**: Site Recovery puede replicar cualquier carga de trabajo que se ejecute en servidores físicos compatibles con Windows o Linux.
- **Máquinas virtuales de VMware**: Site Recovery puede replicar cualquier carga de trabajo que se ejecute en una máquina virtual de VMware compatible.


### ¿Qué necesito en Hyper-V?

Los requisitos para el servidor host de Hyper-V dependen del escenario de implementación. Revise los requisitos previos de Hyper-V en:

- [Replicación de máquinas virtuales de Hyper-V (sin VMM) a Azure](site-recovery-hyper-v-site-to-azure.md#before-you-start)
- [Replicación de máquinas virtuales de Hyper-V (con VMM) a Azure](site-recovery-vmm-to-azure.md#before-you-start)
- [Replicación de máquinas virtuales de Hyper-V a un centro de datos secundario](site-recovery-vmm-to-vmm.md#before-you-start)

Para los invitados que se ejecutan en el servidor host de Hyper-V:

- Si está replicando en un sitio secundario, consulte [Sistemas operativos invitados compatibles para máquinas virtuales Hyper-V](https://technet.microsoft.com/library/mt126277.aspx).
- Si está replicando en Azure, Site Recovery es compatible con los sistemas operativos invitados [admitidos por Azure](https://technet.microsoft.com/library/cc794868%28v=ws.10%29.aspx).


### ¿Puedo proteger las máquinas virtuales cuando Hyper-V se ejecuta en un sistema operativo cliente?

No, las máquinas virtuales deben estar ubicadas en un servidor host de Hyper-V en el que se esté ejecutando una máquina de servidor de Windows compatible. Si necesita proteger un equipo cliente, podría replicarlo como una máquina física [en Azure](site-recovery-vmware-to-azure-classic.md) o en un [centro de datos secundario](site-recovery-vmware-to-vmware.md).


### ¿Qué cargas de trabajo se pueden proteger con Site Recovery?

Site Recovery puede replicar cualquier carga de trabajo que se ejecute en una máquina virtual o servidor físico compatible. Site Recovery proporciona compatibilidad para la replicación con reconocimiento de aplicaciones para que estas se puedan recuperar a un estado inteligente. Se integra con aplicaciones de Microsoft como SharePoint, Exchange, Dynamics, SQL Server y Active Directory; además, colabora estrechamente con los principales proveedores, como Oracle, SAP, IBM y Red Hat. [Obtenga más información](site-recovery-workload.md) acerca de la protección de la carga de trabajo.


### ¿Los hosts de Hyper-V tienen que estar en nubes de System Center VMM para replicarse con Site Recovery? 

Si desea replicar a un centro de datos secundario, las máquinas virtuales de Hyper-V deben administrarse en servidores de host de Hyper-V en una nube de VMM. Si desea replicar en Azure, puede replicar las máquinas virtuales en los servidores host de Hyper-V con o sin nubes de VMM. [Más información](site-recovery-hyper-v-site-to-azure.md).

### ¿Se puede implementar Site Recovery con VMM si solo se tiene un servidor de VMM? 

Sí. Puede replicar en Azure las máquinas virtuales que están en servidores de Hyper-V en la nube de VMM, o bien puede replicar entre nubes de VMM en el mismo servidor. Para la replicación de local a local es recomendable que tenga un servidor de VMM tanto en el sitio principal como en el secundario. [Más información](site-recovery-single-vmm.md)

### ¿Qué servidores físicos se pueden proteger?

Puede proteger servidores físicos con sistemas operativos Windows y Linux compatibles, en Azure o en un sitio secundario. [Aprenda](site-recovery-vmware-to-azure-classic.md#before-you-start-deployment) acerca de los requisitos del sistema operativo para la replicación en Azure o en un sitio secundario. Tenga en cuenta que los servidores físicos que se replican en Almacenamiento de Azure se ejecutan como máquinas virtuales en Azure cuando se produce la conmutación por error. Cuando se produce una conmutación por recuperación de Azure a su sitio local, actualmente no se admite la conmutación por recuperación a un servidor físico. Solo puede conmutar por error a una máquina virtual que se ejecutan en VMware.

### ¿Qué máquinas virtuales de VMware se pueden proteger?

Para proteger las máquinas virtuales de VMWare, necesitará un hipervisor vSphere y máquinas virtuales que ejecuten herramientas de VMware. También recomendamos que tenga un servidor VMware vCenter para administrar los hipervisores. [Aprenda más](site-recovery-vmware-to-azure-classic.md#before-you-start-deployment) sobre los requisitos exactos para replicar los servidores de VMware y las máquinas virtuales en Azure o en un sitio secundario.

### ¿Es posible administrar la recuperación ante desastres para las sucursales con Site Recovery?

Sí. Si usa Site Recovery para organizar la replicación y la conmutación por error en las sucursales, podrá organizar y ver todas las cargas de trabajo de las sucursales desde un punto central. Puede ejecutar con facilidad conmutaciones por error y administrar la recuperación ante desastres de todas las sucursales desde la sede central, sin necesidad de visitar estas sucursales.

## Seguridad


### ¿Se envían mis datos de replicación al servicio de Site Recovery?

No, Site Recovery no intercepta los datos replicados ni tiene información sobre lo que se ejecuta en sus máquinas virtuales o servidores físicos.

Los datos de replicación se intercambian entre hosts locales de Hyper-V, hipervisores de VMware o servidores físicos y el Almacenamiento de Azure en el sitio secundario. Site Recovery no tiene capacidad para interceptar los datos. Únicamente se envían los metadatos necesarios para coordinar la replicación y la conmutación por error al servicio Site Recovery.

Site Recovery está certificado según la norma ISO 27001:2005 y está completando sus evaluaciones de HIPAA, DPA y FedRAMP JAB.

### Para garantizar el cumplimiento, incluso los metadatos de nuestros entornos locales deben permanecer dentro de la misma región geográfica. ¿Site Recovery puede hacer esto?

Sí. Al crear un almacén de Site Recovery en una región, garantizamos que todos los metadatos que necesitamos para habilitar y organizar la replicación y la conmutación por error permanezcan dentro del límite geográfico de esa región.

### ¿Site Recovery cifra la replicación?
Al replicar máquinas virtuales y servidores físicos entre sitios locales, se admite el cifrado en tránsito. Al replicar máquinas virtuales y servidores físicos a Azure, se admite tanto el cifrado en tránsito como el cifrado en reposo (en Azure).



## Replicación

### ¿Hay algún requisito previo para replicar las máquinas virtuales a Azure?

Las máquinas virtuales que quiera replicar a Azure deben cumplir con los [Requisitos de Azure](site-recovery-best-practices.md#azure-virtual-machine-requirements).

### ¿Se pueden replicar máquinas virtuales de generación 2 de Hyper-V a Azure?

Sí. Site Recovery convierte las máquinas virtuales de la generación 2 a la generación 1 durante una conmutación por error. En la conmutación por recuperación, la máquina virtual se convierte de nuevo a la generación 2. [Más información](https://azure.microsoft.com/blog/2015/04/28/disaster-recovery-to-azure-enhanced-and-were-listening/).

### Si se replica a Azure ¿cómo se paga por las máquinas virtuales de Azure? 

Durante una replicación normal, los datos se replican en Almacenamiento de Azure con redundancia geográfica y no es necesario pagar los cargos de máquina virtual de IaaS de Azure. Cuando se realiza una conmutación por error a Azure, Site Recovery crea automáticamente las máquinas virtuales de IaaS de Azure, después de lo cual se le factura por los recursos de proceso que se consumen en Azure.

### ¿Hay un SDK que se pueda usar para automatizar el flujo de trabajo de Site Recovery?

Sí. Puede automatizar los flujos de trabajo de Site Recovery mediante la API de Rest, PowerShell o el SDK de Azure. Aprenda más acerca de [Azure Site Recovery con PowerShell y Azure Resource Manager](site-recovery-deploy-with-powershell-resource-manager.md).

### Si se replica a Azure, ¿qué tipo de cuenta de almacenamiento se necesita?

Necesitará una cuenta de almacenamiento con [almacenamiento con redundancia geográfica estándar](../storage/storage-introduction.md#replication-for-durability-and-high-availability). El Almacenamiento premium no se admite actualmente.

### ¿Con qué frecuencia se pueden replicar los datos?

- **Hyper-V:** las máquinas virtuales de Hyper-V que se estén ejecutando en Windows Server 2012 R2 se pueden replicar cada 30 segundos, 5 minutos o 15 minutos. Si ha configurado la replicación de SAN, la replicación debe ser sincrónica.
- **VMware y servidores físicos:** en este caso no es relevante la frecuencia de replicación. La replicación será continua. 

### ¿Se puede ampliar la replicación desde el sitio de recuperación existente a otro sitio de recuperación?

No se admite la replicación extendida o encadenada. [Envíenos los comentarios que tenga](https://feedback.azure.com/forums/256299-site-recovery/suggestions/6097959-support-for-exisiting-extended-replication/) acerca de esta característica.


### ¿Se puede hacer una replicación sin conexión la primera vez que se replique en Azure? 

No es una opción admitida. [Envíenos los comentarios que tenga](https://feedback.azure.com/forums/256299-site-recovery/suggestions/6227386-support-for-offline-replication-data-transfer-from/) acerca de esta característica.

### ¿Se pueden excluir discos específicos de la replicación?

No es una opción admitida. [Envíenos los comentarios que tenga](https://feedback.azure.com/forums/256299-site-recovery/suggestions/6418801-exclude-disks-from-replication/) acerca de esta característica.

### ¿Se pueden replicar máquinas virtuales con discos dinámicos?

Los discos dinámicos se admiten al replicar máquinas virtuales de Hyper-V. También se admiten al replicar las máquinas virtuales de VMware y los equipos físicos si usa la [implementación mejorada](site-recovery-vmware-to-azure-classic.md). Tenga en cuenta que el disco del sistema operativo debe ser un disco básico.

### ¿Puedo limitar el ancho de banda asignado para el tráfico de replicación de Hyper-V?
- Si va a replicar entre dos sitios locales de máquinas virtuales de Hyper-V, puede usar QoS de Windows. Este es un script de ejemplo: 

    	New-NetQosPolicy -Name ASRReplication -IPDstPortMatchCondition 8084 -ThrottleRate (2048*1024)
    	gpupdate.exe /force

- Si está replicando máquinas virtuales de Hyper-V a Azure, puede configurar el límite mediante el siguiente cmdlet de PowerShell de ejemplo:

    	Set-OBMachineSetting -WorkDay $mon, $tue -StartWorkHour "9:00:00" -EndWorkHour "18:00:00" -WorkHourBandwidth (512*1024) -NonWorkHourBandwidth (2048*1024)

- [Más información](https://support.microsoft.com/es-ES/kb/3056159) sobre la limitación de tráfico de Hyper-V.
- [Más información](site-recovery-vmware-to-azure-classic.md#capacity-planning) sobre la limitación de replicación de VMware en Azure.


## Conmutación por error

### ¿Cómo puedo acceder a las máquinas virtuales de Azure después de la conmutación por error? 

Es posible tener acceso a las máquinas virtuales de Azure a través de una conexión segura a Internet o a través de una VPN de sitio a sitio o mediante Azure ExpressRoute. Las comunicaciones a través de una conexión VPN van a puertos internos de la red de Azure en la que se encuentra la máquina virtual. Las comunicaciones a través de Internet se asignan a los puntos de conexión públicos en el servicio en la nube de Azure para máquinas virtuales.

### Durante una conmutación por error a Azure, ¿cómo se asegura Azure de que los datos resistan el proceso?

Azure está diseñado para la resistencia. Site Recovery ya se ha diseñado para la conmutación por error en un centro de datos de Azure secundario según el SLA de Azure por si surge la necesidad de hacerlo. Si esto ocurre, nos aseguraremos de que los metadatos y los almacenes permanecen en la misma región geográfica que eligió para su almacén.

### Si se replica entre dos sitios, ¿qué ocurre si el centro de datos principal experimenta una interrupción inesperada?

Puede desencadenar una conmutación por error no planeada desde el sitio secundario. Site Recovery no necesita conectividad desde el sitio principal para realizar la conmutación por error.

### ¿La conmutación por error es automática?

La conmutación por error no es automática. Puede iniciar las conmutaciones por error con solo un clic en el portal o bien utilizar los [cmdlets de PowerShell de Site Recovery](https://msdn.microsoft.com/library/dn850420.aspx) para desencadenar una conmutación por error. La conmutación por recuperación está compuesta por una serie de acciones en el portal de Site Recovery.

Para automatizar la conmutación por error, puede utilizar Orchestrator u Operations Manager locales para detectar un error de la máquina virtual y desencadenar luego la conmutación por error mediante el SDK.


##
### Soy un proveedor de servicios. ¿Site Recovery funciona para los modelos de infraestructura compartida o dedicada?
Sí, Site Recovery admite ambos modelos de infraestructura, dedicados y compartidos.

### Para proveedores de servicios, ¿la identidad de mi inquilino se comparte con el servicio Site Recovery?
No. En realidad, los inquilinos no tienen que acceder al portal de Site Recovery. Solo el administrador del proveedor de servicios realiza acciones en el portal.


### ¿Los datos de la aplicación de mis inquilinos llegarán a Azure?

Cuando se replica entre sitios que pertenecen al proveedor de servicios, los datos de la aplicación nunca llegan a Azure. Los datos se cifran en tránsito y se replican directamente entre los sitios del proveedor de servicios.

Si está replicando a Azure, los datos de la aplicación se envían al almacenamiento de Azure, pero no al servicio Site Recovery. Los datos se cifran en tránsito y permanecen cifrados en Azure.

### ¿Recibirán mis inquilinos una factura por los servicios de Azure?

No. La relación de facturación de Azure se entabla directamente con el proveedor de servicios. Los proveedores de servicios son responsables de generar facturas específicas para sus inquilinos.

### Si se está replicando a Azure, ¿es necesario ejecutar máquinas virtuales en Azure en todo momento?

No, los datos se replican a una cuenta de Almacenamiento de Azure con redundancia geográfica en su suscripción. Al realizar una conmutación por error de prueba (obtención de detalles de recuperación ante desastres) o una conmutación por error real, Site Recovery crea automáticamente las máquinas virtuales en su suscripción.

### ¿Se puede garantizar el aislamiento a nivel de inquilino al replicar a Azure?

Sí.

### ¿Qué plataformas se admiten actualmente?

Se admite Azure Pack, el sistema de plataforma en la nube e implementaciones basadas en System Center (2012 y superiores). Aprenda más acerca de la integración de Azure Pack en [Configurar la protección para máquinas virtuales](https://technet.microsoft.com/library/dn850370.aspx).

### ¿Se admite un paquete Azure Pack sencillo e implementaciones de servidor VMM individuales?

Sí, es posible replicar máquinas virtuales de Hyper-V y Azure, o entre sitios del proveedor de servicios. Tenga en cuenta que si se replica entre sitios del proveedor de servicios, la integración de runbooks de Azure no estará disponible.


## Pasos siguientes

- Lea la [Información general sobre Site Recovery](site-recovery-overview.md)
- Obtenga información acerca de la [Arquitectura de Site Recovery](site-recovery-components.md)  

<!---HONumber=AcomDC_0330_2016-->