<properties
	pageTitle="Profundización técnica en la migración compatible con la plataforma de la implementación clásica a la de Azure Resource Manager | Microsoft Azure"
	description="En este artículo se realiza una profundización técnica en la migración compatible con la plataforma de la implementación clásica a la de Azure Resource Manager."
	services="virtual-machines-windows"
	documentationCenter=""
	authors="mahthi"
	manager="timlt"
	editor=""
	tags="azure-resource-manager"/>

<tags
	ms.service="virtual-machines-windows"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-windows"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/22/2016"
	ms.author="mahthi"/>

# Profundización técnica en la migración compatible con la plataforma de la implementación clásica a la de Azure Resource Manager
Profundicemos en la migración desde el modelo de implementación clásica de Azure hasta el modelo de implementación de Azure Resource Manager. Nos centramos en los recursos en un nivel de recursos y función que le ayudarán a comprender cómo la Plataforma de Azure migra los recursos entre los dos modelos de implementación. Para más información, lea el artículo de anuncio de servicio [Migración compatible con la plataforma de recursos de IaaS del modelo clásico al de Azure Resource Manager](virtual-machines-windows-migration-classic-resource-manager.md).

## Instrucciones detalladas acerca de la migración

Puede encontrar las representaciones del modelo clásico y de Resource Manager de los recursos en la tabla siguiente. Actualmente no se admiten otras funciones y recursos.

| Representación clásica | Representación de Resource Manager | Notas detalladas | | |
|--------------------------------------------------------|-------------------------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|---|---|
| Nombre del servicio en la nube | Nombre DNS | Durante la migración, se crea un nuevo grupo de recursos para cada servicio en la nube con el patrón de nomenclatura `<cloudservicename>-migrated`. Este grupo de recursos contiene todos los recursos. El nombre del servicio en la nube se convierte en un nombre DNS que está asociado a la dirección IP pública. | | |
| Máquina virtual | Máquina virtual | Las propiedades específicas de la máquina virtual se migran sin cambios. Determinada información de osProfile, como el nombre de equipo, no se almacena en el modelo de implementación clásica y permanecerá vacía después de la migración. | | |
| Recursos de disco conectados a la máquina virtual | Implícitos discos conectados a la máquina virtual | Los discos no se modelan como recursos de nivel superior en el modelo de implementación de Resource Manager. Se migran como discos implícitos en la máquina virtual. Actualmente, se admiten solo los discos que están conectados a una VM. Las VM de Resource Manager pueden usar ahora cuentas de almacenamiento clásico, que permite que los discos se migren fácilmente sin necesidad de las actualizaciones. | | |
| Extensiones de máquina virtual | Extensiones de máquina virtual | Todas las extensiones de recursos, excepto las extensiones XML, se migran desde el modelo de implementación clásica. | | |
| Certificados de la máquina virtual | Certificados en el Almacén de claves de Azure | Si un servicio en la nube contiene certificados de servicio, la plataforma crea una nueva instancia de Almacén de claves de Azure por cada servicio en la nube y mueve los certificados al almacén de claves. Las máquinas virtuales se actualizan para hacer referencia a los certificados del almacén de claves. | | |
| Configuración de WinRM | Configuración de WinRM en osProfile | La configuración de Administración remota de Windows se traslada tal cual como parte de la migración. | | |
| Propiedad de conjunto de disponibilidad | Recurso de conjunto de disponibilidad | La especificación del conjunto de disponibilidad era una propiedad en la máquina virtual en el modelo de implementación clásica. Los conjuntos de disponibilidad se convierten en un recurso de nivel superior como parte de la migración. Las siguientes configuraciones no son compatibles: varios conjuntos de disponibilidad por servicio en la nube o uno o varios conjuntos de disponibilidad junto con máquinas virtuales que no están en ningún conjunto de disponibilidad de un servicio en la nube. | | |
| Configuración de red en una máquina virtual | Interfaz de red principal | La configuración de red en una máquina virtual se representa como el recurso de la interfaz de red principal después de la migración. En el caso de las máquinas virtuales que no están en una red virtual, la dirección IP interna cambia durante la migración. | | |
| Varias interfaces de red en una máquina virtual | Interfaces de red | Si una VM tiene varias interfaces de red asociadas, cada interfaz de red se convierte en un recurso de nivel superior como parte de la migración en el modelo de implementación de Resource Manager, junto con todas las propiedades. | | |
| Conjunto de punto de conexión de carga equilibrada | Equilibrador de carga | En el modelo de implementación clásica, la plataforma asignaba un equilibrador de carga implícito para cada servicio en la nube. Durante la migración, se crea un nuevo recurso de equilibrador de carga, y el conjunto de puntos de conexión con equilibrio de carga se convierte en reglas de equilibrador de carga. | | |
| Reglas NAT de entrada | Reglas NAT de entrada | Los puntos de conexión de entrada definidos en la VM se convierten en reglas de traducción de direcciones de red de entrada en el equilibrador de carga durante la migración. | | |
| Dirección VIP | Dirección IP pública con nombre DNS | La dirección IP virtual se convierte en una dirección IP pública y se asocia con el equilibrador de carga. | | |
| Red virtual | Red virtual | La red virtual se migra con todas sus propiedades al modelo de implementación de Resource Manager. Se crea un nuevo grupo de recursos con el nombre `-migrated`. Existen [configuraciones no admitidas](virtual-machines-windows-migration-classic-resource-manager.md). | | |
| Direcciones IP reservadas | Dirección IP pública con método de asignación estático | Las direcciones IP reservadas asociadas con el equilibrador de carga se migran junto con la migración del servicio en la nube o de la máquina virtual. Actualmente, no se admite la migración de direcciones IP reservadas no asociadas. | | |
| Dirección IP pública por máquina virtual | Dirección IP pública con método de asignación dinámico | La dirección IP pública asociada a la máquina virtual se convierte como un recurso de dirección IP público con el método de asignación establecido en estático. | | |
| Grupos de seguridad de red | Grupos de seguridad de red | Los grupos de seguridad de red asociados a una subred se clonan como parte de la migración al modelo de implementación de Resource Manager. El grupo de seguridad de red no se quita en el modelo de implementación clásica durante la migración. Sin embargo, las operaciones de plano de administración para el grupo de seguridad de red se bloquean cuando la migración está en curso. | | |
| Servidores DNS | Servidores DNS | Los servidores DNS asociados con la máquina virtual o una red virtual se migran como parte de la migración de recursos correspondiente junto con todas las propiedades. | | |
| Rutas definidas por el usuario | Rutas definidas por el usuario | Las rutas definidas por el usuario asociadas a una subred se clonan como parte de la migración al modelo de implementación de Resource Manager. Las rutas definidas por el usuario no se quitan en el modelo de implementación clásica durante la migración. Las operaciones de plano de administración para las rutas definidas por el usuario se bloquean cuando la migración está en curso. | | |
| Propiedad de reenvío de IP en una configuración de red de máquina virtual | Propiedad de reenvío de IP en la NIC | La propiedad de reenvío de IP en una máquina virtual se convierte en una propiedad en la interfaz de red durante la migración. | | |
| Equilibrador de carga con varias IP | Equilibrador de carga con varios recursos de dirección IP pública | Todas las direcciones IP públicas asociadas con el equilibrador de carga se convierten a un recurso de dirección IP pública y se asocian con el equilibrador de carga después de la migración. | | |
| Nombres DNS internos en la máquina virtual | Nombres DNS internos en la NIC | Durante la migración, se migran los sufijos DNS internos para la máquina virtual a una propiedad de solo lectura denominada "InternalDomainNameSuffix" en la NIC. El sufijo no cambia después de la migración y la resolución de la máquina virtual debería seguir funcionando como antes. | | |

## Ilustración del tutorial de migración simple

La siguiente captura muestra un servicio en la nube existente con una VM (no en una red virtual) después de la fase de preparación:

![Representación clásica después de la preparación](./media/virtual-machines-windows-migration-classic-resource-manager/classic-migration-prepare-portal.png)

Después de que haya completado el proceso de migración, las capturas de pantalla siguientes muestran que se han creado nuevos recursos en un nuevo grupo de recursos: ![Representación de Resource Manager después de la preparación](./media/virtual-machines-windows-migration-classic-resource-manager/resourcemanager-migration-prepare-portal.png)

## Pasos siguientes

Ahora que comprende la migración de recursos de IaaS del modelo clásico al de Resource Manager, puede empezar a migrar los recursos.

- [Migración de recursos de IaaS de la implementación clásica a Resource Manager con Azure PowerShell](virtual-machines-windows-ps-migration-classic-resource-manager.md)
- [Migración de recursos de IaaS de la implementación clásica a Azure Resource Manager con la CLI de Azure](virtual-machines-linux-cli-migration-classic-resource-manager.md)
- [Migración compatible con la plataforma de recursos de IaaS del modelo clásico al de Azure Resource Manager](virtual-machines-windows-migration-classic-resource-manager.md)
- [Clonación de una máquina virtual clásica en Azure Resource Manager con scripts de PowerShell](virtual-machines-windows-migration-scripts.md)

<!---HONumber=AcomDC_0824_2016-->