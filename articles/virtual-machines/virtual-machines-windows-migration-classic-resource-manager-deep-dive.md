<properties
	pageTitle="Profundización técnica en la migración compatible con la plataforma de la implementación clásica a Azure Resource Manager"
	description="Este artículo realiza una profundización técnica de las funcionalidades del servicio de migración compatible con la plataforma. Administración de servicios para Azure Resource Manager"
	services="virtual-machines-windows"
	documentationCenter=""
	authors="mahthi"
	manager="drewm"
	editor=""
	tags="azure-resource-manager"/>

<tags
	ms.service="virtual-machines-windows"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-windows"
	ms.devlang="na"
	ms.topic="article"
	ms.date="05/04/2016"
	ms.author="mahthi"/>

# Profundización técnica en la migración compatible con la plataforma de la implementación clásica a Azure Resource Manager

En este artículo, realizamos una profundización técnica acerca de la migración en un nivel de recursos y características que le ayudarán a comprender cómo la plataforma migra los recursos desde el modo clásico al modo Resource Manager. Lea el artículo de anuncio de servicio [Migración compatible con la plataforma de recursos de IaaS del modelo clásico a Azure Resource Manager](virtual-machines-windows-migration-classic-resource-manager.md).

## Instrucciones detalladas acerca de la migración de nivel de características y recursos

Puede encontrar la representación del modo clásico y de Resource Manager de los recursos siguientes. Las características y los recursos que no aparecen en esta tabla no se admiten en este momento.

| Representación clásica | Representación de Resource Manager | Notas detalladas | | |
|--------------------------------------------------------|-------------------------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|---|---|
| Nombre del servicio de nube | Nombre DNS | Durante la migración, crearemos un nuevo grupo de recursos para cada servicio en la nube con el patrón de nomenclatura `<cloudservicename>-migrated`, que contendrá todos los recursos. El nombre de servicio de nube se convierte en un nombre DNS que está asociado a la dirección IP pública. | | |
| Máquina virtual | Máquina virtual | Las propiedades específicas de la máquina virtual migrarán tal cual. Tenga en cuenta que determinada información de osProfile, como el nombre de equipo, no se almacenó en el modelo de implementación clásica y, por tanto, permanecerá vacía después de la migración. | | |
| Recursos de disco conectados a la máquina virtual | Implícitos discos conectados a la máquina virtual | Los discos no se modelan como recursos de nivel superior en el modelo de implementación de Resource Manager. Migrarán como discos implícitos en la máquina virtual. En este momento, solo se admiten discos conectados a una máquina virtual. Pronto se admitirá la migración de discos no asociados. Para habilitar la migración, las cuentas de almacenamiento clásico ahora tienen la posibilidad de que las utilicen las máquinas virtuales de Resource Manager. Esto permite que los discos puedan migrarse fácilmente en el modelo de Resource Manager con las actualizaciones. | | |
| Extensiones de máquina virtual | Extensiones de máquina virtual | Todas las extensiones de recursos, excepto las extensiones XML, migrarán desde el modelo de implementación clásica. | | |
| Certificados de la máquina virtual | Certificados en el Almacén de claves de Azure | Si un servicio en la nube contiene certificados de servicio, la plataforma creará una nueva instancia de Almacén de claves de azure por cada servicio en la nube y moverá los certificados al almacén de claves. Las máquinas virtuales se actualizarán para hacer referencia a los certificados del almacén de claves. | | |
| Configuración de WinRM | Configuración de WinRM en osProfile | La configuración de WinRM se trasladará tal cual como parte de la migración. | | |
| Propiedad del conjunto de disponibilidad | Recurso del conjunto de disponibilidad | La especificación del conjunto de disponibilidad era una propiedad en la máquina virtual en el modelo de implementación clásica. Los conjuntos de disponibilidad se convertirán en un recurso de nivel superior como parte de la migración. Tenga en cuenta que no se admite la siguiente configuración: 'varios conjuntos de disponibilidad por servicio en la nube' o 'uno o varios conjuntos de disponibilidad junto con máquinas virtuales que no están en ningún conjunto de disponibilidad de un servicio en la nube'. | | |
| Configuración de red en una máquina virtual | Interfaz de red principal | La configuración de red en una máquina virtual ahora se representará como el recurso de la interfaz de red principal después de la migración. En el caso de las máquinas virtuales que no están en una red virtual, la dirección IP interna cambiará durante la migración. | | |
| Varias interfaces de red en una máquina virtual | Interfaces de red | Si una máquina virtual tiene varias interfaces de red asociadas, cada interfaz de red se convertirá en un recurso de nivel superior como parte de la migración en el modelo de implementación de Resource Manager, junto con todas las propiedades. | | |
| Conjunto de puntos de conexión con equilibrio de carga | Equilibrador de carga | En el modelo de implementación clásica, la plataforma asigna un equilibrador de carga implícito para cada servicio de nube. Durante la migración, se creará un nuevo recurso de equilibrador de carga y el conjunto de puntos de conexión con equilibrio de carga se convertirá en reglas de equilibrador de carga. | | |
| Reglas NAT de entrada | Reglas NAT de entrada | Los puntos de conexión de entrada definidos en la máquina virtual se convertirán en reglas NAT de entrada en el equilibrador de carga durante la migración. | | |
| Dirección IP virtual o VIP | Dirección IP pública con nombre DNS | La dirección IP virtual o VIP pasará a ser una dirección IP pública y se asociará al equilibrador de carga. | | |
| Red virtual | Red virtual | La red virtual migrará con todas sus propiedades al modelo de implementación de Resource Manager. Se creará un grupo de recursos con el nombre `-migrated`. Consulte [aquí](virtual-machines-windows-migration-classic-resource-manager.md) las configuraciones no admitidas. | | |
| Direcciones IP reservadas | Dirección IP pública con método de asignación estático | La IP reservada asociada con el equilibrador de carga se migra junto con la migración del servicio en la nube o de la máquina virtual. En este momento no se admite la migración de IP reservada no asociada. | | |
| Dirección IP pública por máquina virtual | Dirección IP pública con método de asignación dinámico | La dirección IP pública asociada a la máquina virtual se convierte como un recurso de dirección IP público con el método de asignación establecido en estático. | | |
| Grupos de seguridad de red | Grupos de seguridad de red | El grupo de seguridad de red asociado a una subred se clona como parte de la migración para el modelo de implementación de Resource Manager. Tenga en cuenta que el grupo de seguridad de red no se quita en el modelo de implementación clásica durante la migración. Sin embargo, las operaciones de plano de administración para el grupo de seguridad de red se bloqueará cuando la migración está en curso. | | |
| Servidores DNS | Servidores DNS | Los servidores DNS asociados con la máquina virtual o una red virtual migran como parte de la migración de recursos correspondiente junto con todas las propiedades. | | |
| Rutas definidas por el usuario | Rutas definidas por el usuario | Las rutas definidas por el usuario (UDR) asociadas a una subred se clonan como parte de la migración para el modelo de implementación de Resource Manager. Tenga en cuenta que las UDR no se quitan en el modelo de implementación clásica durante la migración. Sin embargo, las operaciones de plano de administración para la UDR se bloqueará cuando la migración está en curso. | | |
| Propiedad de reenvío de IP en una configuración de red de máquina virtual | Propiedad de reenvío de IP en la NIC | La propiedad de reenvío de IP en una máquina virtual se convierte en una propiedad en la interfaz de red durante la migración. | | |
| Equilibrador de carga con varias IP | Equilibrador de carga con varios recursos de IP pública | Todas las direcciones IP públicas asociadas con el equilibrador de carga se convertirán en un recurso IP público y se asociarán con el equilibrador de carga después de la migración. | | |
| Nombre DNS interno en la máquina virtual | Nombre DNS interno en la NIC | Durante la migración, los sufijos DNS internos se actualizará para coincidir con los sufijos del modelo de implementación de Resource Manager. Tenga en cuenta que la resolución de nombres seguirá funcionando después de la migración. Sin embargo, se cambiarán los sufijos asociados a las máquinas virtuales. | | |

## Ilustración de ejemplo del tutorial de migración simple

En las capturas de pantalla de ejemplo que se muestran a continuación puede ver la representación de un servicio en la nube con una máquina virtual (no en una red virtual) después de la fase de preparación.

![Captura de pantalla que muestra la representación clásica después de la preparación](./media/virtual-machines-windows-migration-classic-resource-manager/classic-migration-prepare-portal.png) ![Captura de pantalla que muestra la representación de Resource Manager después de la preparación](./media/virtual-machines-windows-migration-classic-resource-manager/resourcemanager-migration-prepare-portal.png)

## Pasos siguientes

Ahora que comprende mejor la migración de recursos IaaS del modelo clásico al de Resource Manager, puede empezar a migrar los recursos.

- [Migración de recursos IaaS del modelo clásico al de Azure Resource Manager con Azure PowerShell](virtual-machines-windows-ps-migration-classic-resource-manager.md)
- [Migración de recursos IaaS del modelo clásico al de Azure Resource Manager mediante la CLI de Azure](virtual-machines-linux-cli-migration-classic-resource-manager.md)
- [Migración compatible con la plataforma de recursos de IaaS del modelo clásico a Azure Resource Manager](virtual-machines-windows-migration-classic-resource-manager.md)
- [Clonación de una máquina virtual clásica en Azure Resource Manager con scripts de PowerShell](virtual-machines-windows-migration-scripts.md)

<!---HONumber=AcomDC_0511_2016-->