---
title: 'Azure AD Domain Services: directrices de redes | Microsoft Docs'
description: Consideraciones de red de Azure Active Directory Domain Services
services: active-directory-ds
documentationcenter: 
author: mahesh-unnikrishnan
manager: mtillman
editor: curtand
ms.assetid: 23a857a5-2720-400a-ab9b-1ba61e7b145a
ms.service: active-directory-ds
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/01/2017
ms.author: maheshu
ms.openlocfilehash: a6f0089f13de10ba8bc1f9a656a2d21f9c559047
ms.sourcegitcommit: f1c1789f2f2502d683afaf5a2f46cc548c0dea50
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/18/2018
---
# <a name="networking-considerations-for-azure-ad-domain-services"></a>Consideraciones de red de Azure AD Domain Services
## <a name="how-to-select-an-azure-virtual-network"></a>Selección de una instancia de Azure Virtual Network
Las siguientes directrices le ayudan a seleccionar una red virtual para usarla con Azure AD Domain Services.

### <a name="type-of-azure-virtual-network"></a>Tipo Azure Virtual Network
* **Redes virtuales de Resource Manager**: Azure AD Domain Services se puede habilitar en las redes virtuales creadas mediante Azure Resource Manager.
* Azure AD Domain Services no se puede habilitar en una instancia clásica de Azure Virtual Network.
* Se pueden conectar otras redes virtuales a la red virtual en la que Azure AD Domain Services está habilitado. Para más información, consulte la sección [Conectividad de red](active-directory-ds-networking.md#network-connectivity).

### <a name="azure-region-for-the-virtual-network"></a>Región de Azure de la red virtual
* Un dominio administrado de Azure AD Domain Services se implementa en la misma región de Azure que se elija para habilitar el servicio.
* Seleccione una red virtual en una región de Azure compatible con Azure AD Domain Services.
* Consulte la página de [servicios de Azure por región](https://azure.microsoft.com/regions/#services/) para saber en qué regiones de Azure está disponible Azure AD Domain Services.

### <a name="requirements-for-the-virtual-network"></a>Requisitos de la red virtual
* **Proximidad a las cargas de trabajo de Azure**: seleccione la red virtual que hospeda, o va a hospedar, las máquinas virtuales que necesitan acceso a Azure AD Domain Services. Si las cargas de trabajo se implementan en una red virtual diferente a la del dominio administrado, también puede conectar las redes virtuales.
* **Servidores DNS personalizados o propios**: asegúrese de que no haya ningún servidor DNS personalizado configurado para la red virtual. Un ejemplo de un servidor DNS personalizado es una instancia de Windows Server DNS que se ejecuta en una máquina virtual de Windows Server que haya implementado en la red virtual. Azure AD Domain Services no se integra con ningún servidor DNS personalizado que esté implementado en la red virtual.
* **Dominios existentes con el mismo nombre de dominio**: asegúrese de que no tiene un dominio existente con el mismo nombre de dominio disponible en esa red virtual. Por ejemplo, supongamos que tiene un dominio llamado 'contoso.com' ya disponible en la red virtual seleccionada. Posteriormente, intenta habilitar un dominio administrado de Azure AD Domain Services con el mismo nombre de dominio (es decir, "contoso.com") en esa red virtual. Al intentar habilitar Azure AD Domain Services, aparece un error, que se debe a los conflictos de nombre en el nombre de dominio de la red virtual. En esta situación, debe utilizar un nombre diferente para configurar el dominio administrado de los Servicios de dominio de Azure AD. Como alternativa, puede aprovisionar el dominio existente y luego proceder a habilitar los Servicios de dominio de Azure AD.

> [!WARNING]
> Domain Services no se puede mover a otra red virtual después de haber habilitado el servicio.
>
>


## <a name="guidelines-for-choosing-a-subnet"></a>Guías para elegir una subred

![Diseño de subred recomendado](./media/active-directory-domain-services-design-guide/vnet-subnet-design.png)

* Implemente Azure AD Domain Services en una **subred dedicada independiente** dentro de la instancia de Azure Virtual Network.
* No aplique los NSG a la subred dedicada para el dominio administrado. Si debe aplicarlos a la subred dedicada, asegúrese de **no bloquear los puertos necesarios para mantener y administrar el dominio**.
* No restrinja en exceso el número de direcciones IP disponibles dentro de la subred dedicada para el dominio administrado. Esta restricción impide que el servicio ponga dos controladores de dominio a disposición del dominio administrado.
* **No habilite Azure AD Domain Services en la subred de la puerta de enlace** de la red virtual.

> [!WARNING]
> Al asociar un NSG a una subred en la que Azure AD Domain Services está habilitado, puede interrumpir la capacidad de Microsoft para mantener y administrar el dominio. Además, se interrumpe la sincronización entre el inquilino de Azure AD y el dominio administrado. **El SLA no es pertinente para aquellas implementaciones en las que se ha aplicado un NSG que impide que Azure AD Domain Services actualice y administre el dominio.**
>
>

## <a name="ports-required-for-azure-ad-domain-services"></a>Puertos necesarios para Azure AD Domain Services
Los siguientes puertos son necesarios para que Azure AD Domain Services mantenga el dominio administrado. Asegúrese de que no estén bloqueados para la subred en la que ha habilitado el dominio administrado.

| Número de puerto | ¿Necesario? | Propósito |
| --- | --- | --- |
| 443 | Obligatorio |Sincronización con el inquilino de Azure AD |
| 5986 | Obligatorio | Administración del dominio |
| 3389 | Opcional | Administración del dominio |
| 636 | Opcional | Acceso LDAP seguro (LDAPS) a su dominio administrado |

**Puerto 443 (sincronización con Azure AD)**
* Se utiliza para sincronizar el directorio de Azure AD con el dominio administrado.
* Es obligatorio permitir el acceso a este puerto en el grupo de seguridad de red. Sin acceso a este puerto, el dominio administrado no está sincronizado con el directorio de Azure AD. Los usuarios no podrán iniciar sesión ya que los cambios en sus contraseñas no se sincronizan con el dominio administrado.
* Puede restringir el acceso de entrada a este puerto a las direcciones IP que pertenezcan al intervalo de direcciones IP de Azure.

**Puerto 5986 (comunicación remota de PowerShell)**
* Se usa para realizar tareas de administración usando la comunicación remota de PowerShell en el dominio administrado.
* Es obligatorio permitir el acceso por este puerto en el grupo de seguridad de red. Sin acceso a este puerto, el dominio administrado no se puede actualizar, configurar, incluir en una copia de seguridad ni supervisar.
* Puede restringir el acceso de entrada a este puerto a las siguientes direcciones IP de origen: : 52.180.183.8, 23.101.0.70, 52.225.184.198, 52.179.126.223, 13.74.249.156, 52.187.117.83, 52.161.13.95, 104.40.156.18, 104.40.87.209, 52.180.179.108, 52.175.18.134, 52.138.68.41, 104.41.159.212, 52.169.218.0, 52.187.120.237, 52.161.110.169, 52.174.189.149, 13.64.151.161
* Los controladores de dominio para el dominio administrado no suelen escuchar en este puerto. El servicio abre este puerto en los controladores de dominio administrados solo cuando debe llevarse a cabo una operación de administración o mantenimiento para el dominio administrado. Tan pronto como finaliza la operación, el servicio cierra este puerto en los controladores de dominio administrados.

**Puerto 3389 (Escritorio remoto)**
* Se utiliza para las conexiones de escritorio remoto a los controladores de dominio del dominio administrado.
* Es opcional abrir este puerto mediante el grupo de seguridad de red.
* Este puerto también permanece desactivado en gran medida en el dominio administrado. Este mecanismo no se utiliza de forma continuada, puesto que las tareas de administración y supervisión se realizan usando la comunicación remota de PowerShell. Este puerto se usa únicamente en el caso excepcional de que Microsoft necesite conectarse de forma remota al dominio administrado para llevar a cabo estrategias avanzadas para solucionar problemas. El puerto se cierra en cuanto se completa la operación de solución de problemas.

**Puerto 636 (LDAP seguro)**
* Se usa para habilitar el acceso mediante LDAP seguro al dominio administrado a través de Internet.
* Es opcional abrir este puerto mediante el grupo de seguridad de red. Abra el puerto solo si tiene habilitado el acceso mediante LDAP seguro a través de Internet.
* Puede restringir el acceso de entrada a este puerto para las direcciones IP de origen desde las que espera conectarse a través de LDAP seguro.


## <a name="network-security-groups"></a>Grupos de seguridad de red
Un [grupo de seguridad de red (NSG)](../virtual-network/virtual-networks-nsg.md) contiene una lista de reglas de lista de control de acceso (ACL) que permiten o deniegan el tráfico de red a sus instancias de máquina virtual en una red virtual. Los NSG se pueden asociar con las subredes o las instancias individuales de máquina virtual dentro de esa subred. Cuando un NSG está asociado a una subred, las reglas de la ACL se aplican a todas las instancias de la máquina virtual de esa subred. Además, el tráfico que se llega a una máquina virtual se puede restringir aún más, para lo que se debe asociar un NSG directamente a dicha máquina virtual.

### <a name="sample-nsg-for-virtual-networks-with-azure-ad-domain-services"></a>Grupo de seguridad de red (NSG) de ejemplo para redes virtuales con Azure AD Domain Services
En la tabla siguiente se muestra un NSG de ejemplo que se puede configurar para una red virtual con un dominio administrado de Azure AD Domain Services. Esta regla permite el tráfico de entrada en los puertos necesarios para garantizar que el dominio administrado se mantiene revisado, actualizado y puede ser supervisado por Microsoft. La regla predeterminada 'DenyAll' se aplica a todo el tráfico de entrada de Internet.

Además, el NSG muestra también cómo bloquear el acceso LDAP seguro a través de Internet. Omita esta regla si no ha habilitado el acceso de LDAP seguro al dominio administrado a través de Internet. NSG contiene un conjunto de reglas que permiten el acceso LDAPS de entrada a través del puerto TCP 636 solo desde un conjunto especificado de direcciones IP. La regla NSG para permitir el acceso LDAPS a través de Internet desde direcciones IP especificadas tiene una prioridad mayor que la regla NSG DenyAll.

![NSG de muestra para el acceso LDAPS seguro a través de Internet](.\media\active-directory-domain-services-alerts\default-nsg.png)

**Más información** - [Creación de un grupo de seguridad de red](../virtual-network/virtual-networks-create-nsg-arm-pportal.md).


## <a name="network-connectivity"></a>Conectividad de red
Un dominio administrado de Azure AD Domain Services solo se puede habilitar dentro de una única red virtual en Azure.

### <a name="scenarios-for-connecting-azure-networks"></a>Escenarios para la conexión de Azure Networks
Conecte instancias de Azure Virtual Network para usar el dominio administrado en cualquiera de los siguientes escenarios de implementación:

#### <a name="use-the-managed-domain-in-more-than-one-azure-virtual-network"></a>Utilice el dominio administrado en más de una red virtual de Azure
Puede conectar otras redes virtuales de Azure a la red virtual de Azure en la que ha habilitado Azure AD Domain Services. Esta conexión VPN/emparejamiento de VNet le permite usar el dominio administrado con sus cargas de trabajo implementadas en otras redes virtuales.

![Conectividad de red virtual clásica](./media/active-directory-domain-services-design-guide/classic-vnet-connectivity.png)

#### <a name="use-the-managed-domain-in-a-resource-manager-based-virtual-network"></a>Uso del dominio administrado en una instancia de Virtual Network basada en Resource Manager
Una red virtual basada en Resource Manager se puede conectar a una instancia clásica de Azure Virtual Network en la que Azure AD Domain Services se ha habilitado. Esta conexión le permite usar el dominio administrado con las cargas de trabajo implementados en la instancia de Virtual Network basada en Resource Manager.

![Conectividad de Resource Manager a una instancia clásica de Virtual Network](./media/active-directory-domain-services-design-guide/classic-arm-vnet-connectivity.png)

### <a name="network-connection-options"></a>Opciones de conexión de red
* **Conexiones de red virtual a red virtual mediante el emparejamiento de red virtual**: el emparejamiento de red virtual es un mecanismo que conecta dos redes virtuales de la misma región mediante la red troncal de Azure. Una vez emparejadas, las dos redes virtuales aparecen como una sola a efectos de conectividad. No obstante, se administran como recursos independientes, pero las máquinas virtuales de estas redes virtuales pueden comunicarse entre sí directamente mediante sus direcciones IP privadas.

    ![Conectividad de Virtual Network mediante emparejamiento](./media/active-directory-domain-services-design-guide/vnet-peering.png)

    [Más información: emparejamiento de red virtual](../virtual-network/virtual-network-peering-overview.md)

* **Conexiones de red virtual a red virtual mediante conexiones VPN de sitio a sitio**: la conexión de una red virtual a otra (de red virtual a red virtual) es parecida a la conexión de una red virtual a la ubicación de un sitio local. Ambos tipos de conectividad usan una instancia de VPN Gateway para proporcionar un túnel seguro con IPsec/IKE.

    ![Conectividad de Virtual Network mediante VPN Gateway](./media/active-directory-domain-services-design-guide/vnet-connection-vpn-gateway.jpg)

    [Más información: conectar redes virtuales mediante VPN Gateway](../vpn-gateway/virtual-networks-configure-vnet-to-vnet-connection.md)

<br>

## <a name="related-content"></a>Contenido relacionado
* [Emparejamiento de Azure Virtual Network](../virtual-network/virtual-network-peering-overview.md)
* [Configuración de una conexión de red virtual a red virtual para el modelo de implementación clásico](../vpn-gateway/virtual-networks-configure-vnet-to-vnet-connection.md)
* [Grupos de seguridad de la red de Azure](../virtual-network/virtual-networks-nsg.md)
* [Creación de un grupo de seguridad de red](../virtual-network/virtual-networks-create-nsg-arm-pportal.md)
