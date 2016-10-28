<properties
	pageTitle="Azure AD Domain Services: directrices de redes | Microsoft Azure"
	description="Consideraciones de red de Azure Active Directory Domain Services"
	services="active-directory-ds"
	documentationCenter=""
	authors="mahesh-unnikrishnan"
	manager="stevenpo"
	editor="curtand"/>

<tags
	ms.service="active-directory-ds"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/20/2016"
	ms.author="maheshu"/>

# Consideraciones de red de Azure AD Domain Services

## Selección de una red virtual de Azure
Las siguientes directrices le ayudan a seleccionar una red virtual para usarla con Azure AD Domain Services.

### Tipo de red virtual de Azure

- Azure AD Domain Services se puede habilitar en una red virtual de Azure clásico.

- Azure AD Domain Services **no se puede habilitar en las redes virtuales creadas mediante Azure Resource Manager**.

- Una red virtual basada en Resource Manager se puede conectar a una red virtual clásica en la que Azure AD Domain Services está habilitado. A partir de ahí, Azure AD Domain Services se puede usar en la red virtual basada en Resource Manager.

- **Redes virtuales regionales**: si planea usar una red virtual existente, asegúrese de que sea una red virtual regional.

    - Las redes virtuales que usan el mecanismo de grupos de afinidad heredados no se puede usar con Azure AD Domain Services.

	- Para usar Azure AD Domain Services, [migre las redes virtuales heredadas a redes virtuales regionales](../virtual-network/virtual-networks-migrate-to-regional-vnet.md).


### Región de Azure de la red virtual

- Un dominio administrado de Azure AD Domain Services se implementa en la misma región de Azure que se elija para habilitar el servicio.

- Seleccione una red virtual en una región de Azure compatible con Azure AD Domain Services.

- Consulte la página de [servicios de Azure por región](https://azure.microsoft.com/regions/#services/) para saber en qué regiones de Azure está disponible Azure AD Domain Services.


### Requisitos de la red virtual

- **Proximidad a las cargas de trabajo de Azure**: seleccione la red virtual que hospeda, o va a hospedar, las máquinas virtuales que necesitan acceso a Azure AD Domain Services.

- **Servidores DNS personalizados o propios**: asegúrese de que no haya ningún servidor DNS personalizado configurado para la red virtual.

- **Dominios existentes con el mismo nombre de dominio**: asegúrese de que no tiene un dominio existente con el mismo nombre de dominio disponible en la red virtual. Por ejemplo, supongamos que tiene un dominio llamado 'contoso.com' ya disponible en la red virtual seleccionada. Posteriormente, intenta habilitar un dominio administrado de Azure AD Domain Services con el mismo nombre de dominio (es decir, "contoso.com") en esa red virtual. Al intentar habilitar Azure AD Domain Services, aparece un error, que se debe a los conflictos de nombre en el nombre de dominio de la red virtual. En esta situación, debe utilizar un nombre diferente para configurar el dominio administrado de los Servicios de dominio de Azure AD. Como alternativa, puede aprovisionar el dominio existente y luego proceder a habilitar los Servicios de dominio de Azure AD.

> [AZURE.WARNING] Domain Services no se puede mover a otra red virtual después de haber habilitado el servicio.


## Grupos de seguridad de red y diseño de subred
Un [grupo de seguridad de red (NSG)](../virtual-network/virtual-networks-nsg.md) contiene una lista de reglas de la lista de control de acceso (ACL) que permiten o deniegan el tráfico de red a sus instancias de VM en una red virtual. Los NSG se pueden asociar con las subredes o las instancias individuales de máquina virtual dentro de esa subred. Cuando un NSG está asociado a una subred, las reglas de la ACL se aplican a todas las instancias de la máquina virtual de esa subred. Además, el tráfico que se llega a una máquina virtual se puede restringir aún más, para lo que se debe asociar un NSG directamente a dicha máquina virtual.

> [AZURE.NOTE] **Implemente Azure AD Domain Services en una subred dedicada independiente dentro de la red virtual de Azure. No aplique ningún NSG a dicha subred. No habilite Azure AD Domain Services en la subred de la puerta de enlace de la red virtual.**

![Diseño de subred recomendado](./media/active-directory-domain-services-design-guide/vnet-subnet-design.png)

> [AZURE.WARNING] Al asociar un NSG a una subred en la que Azure AD Domain Services está habilitado, puede interrumpir la capacidad de Microsoft para mantener y administrar el dominio. Además, se interrumpe la sincronización entre el inquilino de Azure AD y el dominio administrado. **El SLA no se aplica a las implementaciones en las que un NSG se ha aplicado a la subred en la que Azure AD Domain Services está habilitado.**


## Conectividad de red
Un dominio administrado de Azure AD Domain Services solo se puede habilitar dentro de una única red virtual de Azure clásico. No se admiten las redes virtuales creadas mediante Azure Resource Manager.

### Escenarios para la conexión de redes de Azure
Conecte las redes virtuales de Azure para usar el dominio administrado en cualquiera de los siguientes escenarios de implementación:

#### Utilice el dominio administrado en más de una red virtual de Azure clásico
Puede conectar otras redes virtuales de Azure clásico a la red virtual la que ha habilitado Azure AD Domain Services. Esta conexión le permite usar el dominio administrado con sus cargas de trabajo implementadas en otras redes virtuales.

![Conectividad de red virtual clásica](./media/active-directory-domain-services-design-guide/classic-vnet-connectivity.png)

#### Uso del dominio administrado en una red virtual basada en Resource Manager
Una red virtual basada en Resource Manager se puede conectar a una red virtual de Azure clásico en la que Azure AD Domain Services se ha habilitado. Esta conexión le permite usar el dominio administrado con las cargas de trabajo implementados en la red virtual basada en Resource Manager.

![Conectividad de Resource Manager a red virtual clásica](./media/active-directory-domain-services-design-guide/classic-arm-vnet-connectivity.png)


### Opciones de conexión de red

- **Conexiones de red virtual a red virtual mediante conexiones VPN de sitio a sitio**: la conexión de una red virtual a otra (de red virtual a red virtual) es parecida a la conexión de una red virtual a la ubicación de un sitio local. Ambos tipos de conectividad usan una puerta de enlace de VPN para proporcionar un túnel seguro con IPsec/IKE.

	![Conectividad de red virtual mediante VPN Gateway](./media/active-directory-domain-services-design-guide/vnet-connection-vpn-gateway.jpg)

    [Más información: conectar redes virtuales mediante VPN Gateway](../vpn-gateway/virtual-networks-configure-vnet-to-vnet-connection.md)


- **Conexiones de red virtual a red virtual mediante el emparejamiento de red virtual**: el emparejamiento de red virtual es un mecanismo que conecta dos redes virtuales de la misma región mediante la red troncal de Azure. Una vez emparejadas, las dos redes virtuales aparecen como una sola a efectos de conectividad. No obstante, se administran como recursos independientes, pero las máquinas virtuales de estas redes virtuales pueden comunicarse entre sí directamente mediante sus direcciones IP privadas.

    ![Conectividad de red virtual mediante emparejamiento](./media/active-directory-domain-services-design-guide/vnet-peering.png)

	[Más información: emparejamiento de red virtual](../virtual-network/virtual-network-peering-overview.md)



<br>

## Contenido relacionado

- [Emparejamiento de red virtual de Azure](../virtual-network/virtual-network-peering-overview.md)

- [Configuración de una conexión de red virtual a red virtual para el modelo de implementación clásico](../vpn-gateway/virtual-networks-configure-vnet-to-vnet-connection.md)

- [Grupos de seguridad de la red de Azure](../virtual-network/virtual-networks-nsg.md)

<!---HONumber=AcomDC_0921_2016-->