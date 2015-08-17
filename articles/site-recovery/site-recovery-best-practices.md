<properties
	pageTitle="Prácticas recomendadas para la implementación de Site Recovery"
	description="Azure Site Recovery coordina la replicación, la conmutación por error y la recuperación de máquinas virtuales ubicadas en servidores locales de Azure o en un centro de datos secundario."
	services="site-recovery"
	documentationCenter=""
	authors="rayne-wiselman"
	manager="jwhit"
	editor="tysonn"/>

<tags
	ms.service="site-recovery"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.tgt_pltfrm="na"
	ms.workload="storage-backup-recovery"
	ms.date="05/08/2015"
	ms.author="raynew"/>

# Prácticas recomendadas para la implementación de Site Recovery


<a id="overview" name="overview" href="#overview"></a>

## Información acerca de este artículo

El artículo incluye las prácticas recomendadas que debe leer y poner en práctica antes de implementar Azure Site Recovery. Si está buscando una introducción a los escenarios de implementación de Site Recovery y relacionados, consulte [Información general sobre Site Recovery](site-recovery-overview.md).

Si tiene alguna pregunta después de leer este artículo, publíquela en el [Foro de servicios de recuperación de Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).


## Preparación de Azure

- **Cuenta de Azure**: necesitará una cuenta de [Microsoft Azure](http://azure.microsoft.com/). Si no tiene una, comience con una [evaluación gratuita](pricing/free-trial/).
- Lea acerca de los [precios](pricing/details/site-recovery/) para Site Recovery. 
- **Almacenamiento de Azure**: si implementa Site Recovery con replicación en Azure, necesitará una cuenta de almacenamiento de Azure. Puede configurar una durante la implementación o preparar una antes de empezar. La cuenta debe tener habilitada la replicación geográfica. Además, debe estar en la misma región que el almacén de Azure Site Recovery y estar asociada a la misma suscripción. Lea [Introducción a Almacenamiento de Microsoft Azure](../storage/storage-introduction.md). 

## Máquinas virtuales

Si desea replicar al Almacenamiento de Azure, tenga en cuenta lo siguiente:

- **Sistema de operativo compatible**: puede implementar Site Recovery para orquestar la protección de máquinas virtuales que ejecutan cualquier sistema operativo compatible con Azure. Incluye la mayoría de las versiones de Windows y Linux.
- **Compatibilidad con VHDX**: aunque VHDX no se admite en Azure en este momento, Site Recovery convierte automáticamente VHDX en VHD al conmutar por error en Azure. Cuando se realiza la conmutación por recuperación en local, las máquinas virtuales siguen usando el formato VHDX.
- **Requisitos de Azure**: deberá asegurarse de que las máquinas virtuales que desea proteger cumplen los requisitos de Azure.

**Característica de la máquina virtual** | **Soporte técnico** | **Detalles**
---|---|---
Sistema operativo host | Windows Server 2012 R2 | Se producirá un error en la comprobación de los requisitos previos si no es compatible.
Sistema operativo invitado | <p>Windows Server 2008 R2 o posterior</p><p>Linux: Centos, openSUSE, SUSE, Ubuntu</p> | Se producirá un error en la comprobación de los requisitos previos si no es compatible. Actualice el valor en la consola VMM.
Arquitectura del sistema operativo invitado | 64 bits | Se producirá un error en la comprobación de los requisitos previos si no es compatible.
Tamaño del disco del sistema operativo | Hasta 1023 GB | Se producirá un error en la comprobación de los requisitos previos si no es compatible.
Número de discos del sistema operativo | 1 | Se producirá un error en la comprobación de los requisitos previos si no es compatible. Actualización del valor en la consola VMM
Número de discos de datos | 16 o menos (el valor máximo es una función del tamaño de la máquina virtual que se está creando. 16 = XL) | Se producirá un error en la comprobación de los requisitos previos si no es compatible.
Tamaño de VHD del disco de datos | Hasta 1023 GB | Se producirá un error en la comprobación de los requisitos previos si no es compatible.
Adaptadores de red | Se admiten varios adaptadores |
Dirección IP estática | Compatible | Si la máquina virtual principal está usando una dirección IP estática, puede especificar la dirección IP estática para la máquina virtual que se crea en Azure
Disco iSCSI | No compatible | Se producirá un error en la comprobación de los requisitos previos si no es compatible.
VHD compartido | No compatible | Se producirá un error en la comprobación de los requisitos previos si no es compatible.
Disco FC | No compatible | Se producirá un error en la comprobación de los requisitos previos si no es compatible.
Formato de disco duro| <p>VHD</p><p>VHDX</p> |
Nombre de la máquina virtual| Entre 1 y 63 caracteres. Restringido a letras, números y guiones. El nombre debe empezar y terminar por una letra o un número. | Actualizar el valor de las propiedades de la máquina virtual en Site Recovery
Tipo de máquina virtual | <p>Generación 1</p> <p>Generación 2 - Windows</p> | Se admiten las máquinas virtuales de generación 2 con el tipo de disco de SO de disco básico que incluye uno o dos volúmenes de datos con un formato de disco como VHDX, inferior a 300 GB. No se admiten máquinas virtuales Linux de generación 2. [Más información](http://azure.microsoft.com/blog/2015/04/28/disaster-recovery-to-azure-enhanced-and-were-listening/) 


## Servidores VMM

Site Recovery puede orquestar la replicación para máquinas virtuales ubicadas en servidores host de Hyper-V en nubes de System Center Virtual Machine Manager (VMM) como sigue: - Replicación de un servidor VMM local a Azure (mediante la réplica de Hyper-V) - Replicación a un sitio local secundario (con la réplica de Hyper-V) de sitio local. Se recomienda que realice la implementación con un servidor VMM en el sitio principal y otro en el sitio secundario. Sin embargo si se necesita, puede [implementar un único servidor VMM](site-recovery-single-vmm.md) para ambos sitios. - Replicar a un sitio local secundario (mediante SAN). Necesitará de un centro de datos principal y secundario con un servidor VMM en cada sitio. Si desea implementar VMM con Site Recovery, deberá configurar la infraestructura de VMM. Si no dispone de un servidor VMM, obtenga más información [aquí](site-recovery-hyper-v-site-to-azure.md).


### Comprobación de la versión de VMM

**Versión de VMM compatible** | **Escenario de Site Recovery admitido** | **Host de Hyper-V compatible (origen/destino)** | **Proveedor/agente compatibles**
---|---|---|---
VMM en System Center 2012 R2 con al menos el paquete acumulativo de actualizaciones 5 (virtual solo en clúster) | Protección de local a local mediante replicación de SAN | Al menos Windows Server 2012 con las últimas actualizaciones | Versión más reciente del proveedor de Azure Site Recovery (instalar en servidores VMM) |
VMM en System Center 2012 R2 (recomendado) (clúster o independiente) | <p>Protección de local a local con la replicación de Hyper-V</p> <p>Protección de local a Azure con replicación de Hyper-V</p> | <p>Al menos Windows Server 2012 con las últimas actualizaciones</p><p>Windows Server 2012 R2 (origen, no aplicable para el destino)</p> | <p>La versión más reciente del proveedor de Azure Site Recovery (instalar en servidores VMM)</p> <p>Versión más reciente del agente de Servicios de recuperación de Azure (instalar en servidores host de Hyper-V solo para la replicación en Azure)</p>
VMM en System Center 2012 SP1 con la actualización acumulativa más reciente (clúster o independiente) | Protección de local a local | Windows Server 2012 con las últimas actualizaciones | Versión más reciente del proveedor de Azure Site Recovery (instalar en servidores VMM) |

### Configuración de la infraestructura de nube VMM

Sea cual sea el escenario VMM que desee implementar con Site Recovery, tendrá que crear al menos dos nubes privadas (una en el servidor VMM de origen y la otra en el destino) en VMM para beneficiarse de las ventajas del modelo de nube privada al definir, administrar y acceder a la nube y a sus recursos subyacentes mediante la consola VMM. Una nube VMM recopila y presenta un conjunto agregado de recursos, cuyo uso está limitado por la configuración de capacidad de nube y por las cuotas de rol de usuario. Los usuarios de autoservicio pueden administrar y usar recursos de la nube sin entender completamente su infraestructura subyacente. Se pueden agregar fácilmente recursos para reducir o aumentar la elasticidad de la nube. Si necesita configurar las nubes, use los recursos siguientes:


- [Novedades en la nube privada con System Center 2012 R2 VMM](http://channel9.msdn.com/Events/TechEd/NorthAmerica/2013/MDC-B357#fbid=)
- [VMM 2012 y las nubes](http://www.server-log.com/blog/2011/8/26/vmm-2012-and-the-clouds.html).
- [Configuración del tejido de nube de VMM](https://msdn.microsoft.com/library/azure/dn469075.aspx#BKMK_Fabric)
- [Creación de una nube privada en VMM](https://technet.microsoft.com/es-es/library/jj860425.aspx)
- [Tutorial: Creación de nubes privadas con System Center 2012 SP1 VMM](http://blogs.technet.com/b/keithmayer/archive/2013/04/18/walkthrough-creating-private-clouds-with-system-center-2012-sp1-virtual-machine-manager-build-your-private-cloud-in-a-month.aspx).




## Proveedores y agentes

Los proveedores y los agentes se instalan en servidores locales para que puedan conectarse a Site Recovery en Azure. Se conectan a través de Internet con una conexión HTTPS cifrada.

- **Requisitos del proveedor**: durante la implementación instalará un par de componentes en los servidores locales:
	- **En servidores VMM**: instalará el proveedor de Azure Site Recovery en los servidores VMM que desee registrar en el almacén.
	- **En servidores host de Hyper-V ubicados en una nube VMM**: podrá instalar el agente de Servicios de recuperación de Azure.
	- **En servidores host de Hyper-V sin servidor VMM**: instalará el proveedor de Azure Site Recovery y el agente de Servicios de recuperación de Azure en cada nodo de clúster o servidor host de Hyper-V.
-  **Instalación del proveedor**: cuando instala el proveedor y los agentes, tenga en cuenta lo siguiente:
	-  Debe instalar la misma versión del proveedor en todos los servidores de un almacén de Site Recovery. No se admite el uso de diferentes versiones en un único almacén.
	-  Si desea replicar a Azure desde servidores de Hyper-V ubicados en una nube de VMM, el servidor VMM debe ejecutar System Center 2012 R2. Si desea replicar a un centro de datos secundario, VMM debe estar ejecutando System Center 2012 con SP1 o R2.
- **Proxy**: no es necesario agregar excepciones del firewall ni crear un proxy específico. Si no desea utilizar un proxy personalizado para la conexión del proveedor, esto es lo que necesita hacer antes de empezar la implementación:

	- Configurar el servidor proxy personalizado antes de instalar el proveedor.
	- Permitir estas direcciones URL a través del firewall:
		- **.hypervrecoverymanager.windowsazure.com
- **.accesscontrol.windows.net
- **.backup.windowsazure.com
- **.blob.core.windows.net
- **.store.core.windows.net

	- Si está implementando Site Recovery con VMM y usa un proxy personalizado, se creará una cuenta de ejecución de VMM (DRAProxyAccount) automáticamente con las credenciales de proxy, especificadas en la configuración del proxy personalizada del portal de Site Recovery. Tendrá que configurar el servidor proxy para que esta cuenta pueda autenticarse correctamente.



## Conectividad local

- **Conectividad de proveedores**: los proveedores y los agentes se instalan en servidores locales para que puedan conectarse a Site Recovery. Se conectan a Site Recovery a través de Internet con una conexión HTTPS cifrada. No es necesario agregar excepciones del firewall ni crear un proxy específico.
- **Conectividad a Internet**: necesita conectividad a Internet del servidor como sigue:
	- Si está protegiendo máquinas virtuales en servidores host de Hyper-V en una nube de VMM, solo el servidor VMM necesita una conexión a Internet.
	- Si está protegiendo máquinas virtuales en servidores host de Hyper-V sin un servidor VMM, solo los servidores host de Hyper-V necesitan una conexión a Internet.
	- No se necesita ninguna conexión a Internet en las máquinas virtuales que desea proteger.
- **VPN sitio a sitio**: no necesita una conexión VPN de sitio a sitio para conectarse a Site Recovery. Sin embargo si dispone de una conexión de sitio a sitio, podrá acceder máquinas virtuales conmutadas por error de la misma forma que hizo antes. Tenga en cuenta que cuando se replican a Azure, configurará una red VPN de sitio a sitio entre el sitio local y una red específica de Azure. No se utiliza para el tráfico de replicación cifrada. Pasa a través de Internet a la cuenta de Almacenamiento de Azure en su suscripción.
- **Conectividad después de la conmutación por error**: para asegurarse de que los usuarios pueden conectarse a las máquinas virtuales después de la conmutación por error en Azure, haga lo siguiente:
	- Habilite RDP para las máquinas virtuales antes de la conmutación por error.
	- Después de la conmutación por error, utilice una conexión de sitio a sitio para que se conecte a ellos como hizo anteriormente o habilite el extremo RDP para la máquina.

## Almacenamiento

- **Cuenta de Almacenamiento de Azure**: si está replicando en Azure, necesitará una cuenta de Almacenamiento de Azure. La cuenta debe tener habilitada la replicación geográfica. Además, debe estar en la misma región que el almacén de Azure Site Recovery y estar asociada a la misma suscripción. Para obtener más información, consulte [Introducción al Almacenamiento de Microsoft Azure](../storage/storage-introduction.md).
- **Asignación de almacenamiento**: si se están replicando máquinas virtuales en servidores VMM locales, puede establecer la asignación de almacenamiento para asegurarse de que las máquinas virtuales están conectadas óptimamente al almacenamiento de información después de la conmutación por error. Al replicar entre dos sitios VMM locales, de forma predeterminada la máquina virtual de réplica se almacenará en la ubicación indicada en el servidor host de Hyper-V de destino. Puede configurar la asignación entre las clasificaciones de almacenamiento VMM en los servidores VMM de origen y de destino. Si desea utilizar este característica, asegúrese de tener clasificaciones de almacenamiento configuradas antes de empezar la implementación. [Obtenga más información](site-recovery-storage-mapping.md) sobre la asignación de almacenamiento.
- **SAN**: si desea replicar entre dos sitios locales con la replicación de SAN, tenga en cuenta que:
	- Solo se pueden replicar máquinas virtuales de Hyper-V en un centro de datos secundaria con la replicación de SAN. No se puede replicar a Azure.
	- Puede utilizar el entorno de SAN existente. No es necesario realizar ningún cambio en las matrices de SAN.
	- Deberá comprobar que la matriz de SAN se [admite](http://social.technet.microsoft.com/wiki/contents/articles/28317.deploying-azure-site-recovery-with-vmm-and-san-supported-storage-arrays.aspx).
	- Necesitará dos servidores VMM en los sitios de origen y de destino para la implementación de SAN.
	- Asegúrese de que los clústeres host de Hyper-V están ejecutando Windows Server 2012 o 2012 R2 si desea implementar la replicación de SAN. [Obtenga más información](https://technet.microsoft.com/library/cc794868%28v=ws.10%29.aspx) sobre los sistemas operativos invitados admitidos por las distintas versiones de Hyper-V.
	- Necesitará detectar y clasificar el almacenamiento de SAN en WMM.
	- Si todavía no está replicando, después la detección, tendrá que crear LUN y asignar el almacenamiento en la consola VMM. Si ya está replicando, puede omitir este paso.
	- Todas las instrucciones están disponibles en este [artículo](site-recovery-vmm-san.md).


## Redes

- **Obtenga más información acerca de las consideraciones de red**: [Obtenga más información](http://azure.microsoft.com/blog/2014/09/04/networking-infrastructure-setup-for-microsoft-azure-as-a-disaster-recovery-site/) sobre las consideraciones de redes y prácticas recomendadas.

- **Configuración de la asignación de redes**: la asignación de redes es un elemento importante al implementar VMM y Site Recovery. Coloca de forma óptima las máquinas virtuales replicadas en los servidores host de Hyper-V de destino y garantiza que las máquinas virtuales replicadas se conecten a redes adecuadas después de la conmutación por error. Puede configurar la asignación de redes cuando está replicando en Azure o en un centro de datos secundario:
	- **Asignaciones de redes en Azure**: si está replicando en Azure, la asignación de redes asegura que todas las máquinas virtuales que conmuten por error en la misma red se pueden conectar entre sí, con independencia del plan de recuperación en el que se encuentren. Además, si se configura una puerta de enlace de red en la red Azure de destino, las máquinas virtuales se pueden conectar a otras máquinas virtuales locales. Si no configura la asignación de redes, solo se pueden conectar las máquinas que conmutan por error en el mismo plan de recuperación.
	- **Asignación de redes al sitio secundario**: si se está replicando en un sitio VMM secundario, la asignación de redes se asegura de que las máquinas virtuales estén conectadas a redes adecuadas después de la conmutación por error y de que las máquinas virtuales de réplica se colocan de forma óptima en los servidores host de Hyper-V. Si no configura la asignación de redes, las máquinas virtuales replicadas no se conectarán a ninguna red de máquinas virtuales.
	- [Obtenga más información](site-recovery-network-mapping) sobre la asignación de redes.
- **Configuración de redes VMM**:
	- Configure redes lógicas y redes de máquinas virtuales correctamente en VMM. Obtenga información sobre [redes lógicas](http://blogs.technet.com/b/scvmm/archive/2013/02/14/networking-in-vmm-2012-sp1-logical-networks-part-i.aspx) y [redes de máquinas virtuales](https://technet.microsoft.com/library/jj721575.aspx).
	- Asegúrese de que todas las máquinas virtuales en el servidor VMM de origen están conectados a una red de máquinas virtuales.
	- Compruebe que las redes de máquinas virtuales estén vinculadas a una red lógica asociada con la nube.
	- Si se está replicando en Azure, cree redes virtuales en Azure. Tenga en cuenta que pueden asignarse varias redes de VM a una sola red de Azure. Obtenga información en [Tareas de configuración de la red virtual](../vpn-gateway/vpn-gateway-site-to-site-create.md).

## Optimización del rendimiento

- **Tamaño del volumen del sistema operativo**: al replicar una máquina virtual a Azure, el volumen del sistema operativo tiene que ser inferior a 1 TB. Si tiene más volúmenes, puede moverlos manualmente a otro disco antes de comenzar la implementación.
- **Tamaño de disco de datos**: si se está replicando en Azure, podrá tener hasta 32 discos de datos en una máquina virtual, cada una con un máximo de 1 TB. Puede replicar y conmutar por error de manera eficaz una máquina virtual de \~32 TB.
- **Límites del plan de recuperación**: Site Recovery puede escalar a miles de máquinas virtuales. Los planes de recuperación están diseñados como un modelo para las aplicaciones que deben conmutar por error entre sí por lo que se limita el número de máquinas en un plan de recuperación a 50.
- **Límites del servicio Azure**: cada suscripción de Azure incluye un conjunto de límites predeterminados sobre núcleos, servicios en la nube, etc. Le recomendamos que ejecute una conmutación por error de prueba para validar la disponibilidad de los recursos de la suscripción. Puede modificar estos límites a través de soporte técnico de Azure.
- **Planificación de capacidad**: para obtener instrucciones, use la [Planificación de capacidad para la réplica de Hyper-V](http://www.microsoft.com/en-in/download/details.aspx?id=39057).
- **Ancho de banda de replicación**: si tiene poco ancho de banda de replicación, tenga en cuenta lo siguiente:
	- **ExpressRoute**: Site Recovery funciona con los optimizadores de ExpressRoute de Azure y WAN, como Riverbed. [Obtenga más información](http://blogs.technet.com/b/virtualization/archive/2014/07/20/expressroute-and-azure-site-recovery.aspx) sobre ExpressRoute.
	- **Tráfico de replicación**: Site Recovery realiza una replicación inicial inteligente usando solo bloques de datos y no todo el VHD. Solo se replican los cambios durante la replicación continua.
	- **Tráfico de red**: puede controlar el tráfico de red que se utiliza para la replicación mediante la configuración de [Windows QoS](https://technet.microsoft.com/library/hh967468.aspx) con una directiva basada en la dirección IP de destino y el puerto. Además, si se está replicando en Azure Site Recovery mediante el agente Copia de seguridad de Azure. Puede configurar la limitación para ese agente. [Más información](https://support.microsoft.com/kb/3056159).
- **RTO**: si desea medir el objetivo de tiempo de recuperación (RTO) que se puede esperar con Site Recovery, le sugerimos que ejecute una conmutación por error de prueba y vea los trabajos de Site Recovery para analizar la cantidad de tiempo que se tarda en completarse las operaciones. Si está conmutando por error en Azure, para obtener un mejor RTO se recomienda que automatice todas las acciones manuales mediante la integración con Automatización de Azure y los planes de recuperación.
- **RPO**: Site Recovery admite un objetivo de punto de recuperación casi sincrónico (RPO) al replicar a Azure. Esto supone suficiente ancho de banda entre el centro de datos y Azure.

## Conmutación por error
- **Interrupción en el principal**: si está replicando de un centro de datos local a otro y ambos centros de datos experimentan una interrupción en el sitio principal, ejecute una conmutación por error no planeada desde el portal de Site Recovery. No se necesita conectividad desde el centro de datos principal para realizar la conmutación por error.
- **Conservar la dirección IP tras la conmutación por error en el sitio secundario**: si desea conservar la dirección IP de una máquina virtual de origen después de que se produzca un error en un centro de datos secundario, siga los pasos que se indican [aquí](http://blogs.technet.com/b/scvmm/archive/2014/04/04/retaining-ip-address-after-failover-using-hyper-v-recovery-manager.aspx).
- **Conservar la dirección IP tras la conmutación por error en Azure**: puede especificar la dirección IP que se asignará a máquina virtual conmutada por error en la pestaña Configurar de la máquina virtual. Para obtener información, consulte cómo [configurar las propiedades de red de la máquina virtual](site-recovery-vmm-to-azure.md#step-8-enable-protection-for-virtual-machines)
- **Conservar la dirección IP pública**: si desea conservar una dirección IP pública después de la conmutación por error en un sitio secundario, Site Recovery no impedirá que se realice si su ISP lo admite. No puede conservar una dirección IP pública después de la conmutación por error en Azure.
- **Conservar las direcciones internas no RFC en Azure**: puede conservar 1918 de espacios de direcciones no RFC después de la conmutación por error en Azure.
- **Conmutación por error parcial para el centro de datos secundario**: si se conmuta por error un sitio parcial al centro de datos secundario y desea volver a conectarse al sitio principal, puede usar la VPN de sitio a sitio para conectarse a una aplicación conmutada por error en el sitio secundario en componentes de infraestructura que se ejecutan en el sitio primario. Tenga en cuenta que si la subred completa conmuta por error, se puede conservar la dirección IP de la máquina virtual. Si se realiza la conmutación por error de una subred parcial, no se puede conservar la dirección IP de la máquina virtual porque las subredes no pueden dividirse entre sitios.
- **Conmutación por error parcial en Azure**: si se conmuta por error un sitio parcial en Azure y desea volver a conectarse al sitio principal, puede usar la VPN de sitio a sitio para conectarse a una aplicación conmutada por error de Azure en componentes de infraestructura que se ejecutan en el sitio primario. Tenga en cuenta que si la subred completa conmuta por error, se puede conservar la dirección IP de la máquina virtual. Si se realiza la conmutación por error de una subred parcial, no se puede conservar la dirección IP de la máquina virtual porque las subredes no pueden dividirse entre sitios.
- **Conservar la letra de unidad**: si desea conservar la letra de unidad en las máquinas virtuales después de la conmutación por error, puede establecer la directiva SAN para la máquina virtual en **Activado**. [Más información](https://support.microsoft.com/kb/3031135).
- **Enrutamiento de solicitudes de cliente después de la conmutación por error en Azure**: Site Recovery funciona con el Administrador de tráfico de Azure para enrutar las solicitudes de cliente a la aplicación después de la conmutación por error. Puede utilizar scripts en los planes de recuperación (con Automatización de Azure) para realizar las actualizaciones de DNS.

## Integración

- **Integración con otras tecnologías BCDR**: Site Recovery se integra con otras tecnologías de recuperación ante desastres y continuidad de negocio (BCDR). Para la replicación basada en aplicación, se recomienda utilizar SQL Server AlwaysOn para replicar máquinas virtuales que ejecutan la base de datos y la réplica de Hyper-V para máquinas virtuales de front-end. AlwaysOn requiere licencias de SQL Server Enterprise en el sitio principal y secundario y una máquina virtual Azure que se ejecuta en su suscripción. El uso de Site Recovery en la replicación integrada es útil donde las aplicaciones puedan controlar el tiempo de inactividad y, en este caso, se recomienda Site Recovery para orquestar la replicación de máquinas virtuales de bases de datos, aplicaciones y front-end. Este enfoque ahorra dinero en la versión de SQL Server que necesita, el número de licencias y el coste de mantener en ejecución las máquinas virtuales en Azure en todo momento.

## Pasos siguientes

Después de revisar estas prácticas recomendadas, puede iniciar la implementación de Site Recovery:

- [Configuración de la protección entre un sitio de VMM local y Azure](site-recovery-vmm-to-azure.md)
- [Configuración de la protección entre un sitio de Hyper-V local y Azure](site-recovery-hyper-v-site-to-azure.md)
- [Configuración de la protección entre dos sitios VMM locales](site-recovery-vmm-to-vmm.md)
- [Configuración de la protección entre dos sitios de VMM locales con SAN](site-recovery-vmm-san.md)
- [Configuración de la protección con un único servidor VMM](site-recovery-single-vmm.md)
 

<!---HONumber=August15_HO6-->