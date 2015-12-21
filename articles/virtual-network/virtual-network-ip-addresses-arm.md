<properties 
   pageTitle="Direcciones IP de Red virtual de Azure | Microsoft Azure"
   description="Obtenga información sobre direcciones IP, públicas y privadas, para el proveedor de recursos de red en Administrador de recursos de Azure"
   services="virtual-network"
   documentationCenter="na"
   authors="joaoma"
   manager="carmonm"
   editor="tysonn" />
<tags 
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="12/07/2015"
   ms.author="joaoma" />

# Direcciones IP de Red virtual de Azure
En este artículo se trata el direccionamiento IP para Máquinas virtuales, Equilibradores de carga, Puertas de enlace de VPN y puertas de enlace de aplicación.

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/learn-about-deployment-models-rm-include.md)].

## Direcciones IP públicas
Las direcciones IP públicas permiten a los recursos de Azure comunicarse con Internet y otros servicios de acceso público de Azure como [Caché en Redis de Azure](https://azure.microsoft.com/services/cache), [Centros de eventos de Azure](https://azure.microsoft.com/services/event-hubs). Una dirección IP pública es un recurso independiente y se puede asociar a distintos tipos de recursos de Azure como [Máquinas virtuales](virtual-machines-about.md) (VM) y [Equilibradores de carga](load-balancer-overview.md) (LB).

### Método de asignación de direcciones IP públicas
Las direcciones IP públicas se asignan desde un grupo de direcciones IP disponibles en una ubicación especificada. La lista completa de intervalos de direcciones IP usadas por los centros de datos de Microsoft Azure está publicada en [Azure Datacenter IP ranges](https://www.microsoft.com/download/details.aspx?id=41653).

Hay dos métodos en los que se asigna una dirección IP a un recurso de IP pública: *dinámico* o *estático*. El método de asignación predeterminado es *dinámico*. En el método *dinámico*, la dirección IP **no** se asigna al *recurso de dirección IP pública* en el momento de su creación. Cuando se crea o se inicia un recurso (como un equilibrador de carga o máquina virtual) asociado al *recurso de dirección IP pública*, se asigna una dirección IP del grupo de direcciones IP. Esta dirección IP anula la asignación y se libera al grupo disponible cuando se elimina o se detiene el recurso asociado.

Si usa el método de asignación *estático*, la dirección IP se asigna al *recurso de dirección IP pública* en el momento de su creación. En este caso, independientemente del estado del recurso asociado, la misma dirección IP permanecerá asignada. Se libera al grupo disponible solo cuando el *recurso de dirección IP pública* se elimina o se modifica su método de asignación en *dinámico*.

### Resolución DNS
Puede especificarse una etiqueta de nombre de dominio DNS para un recurso IP público, que crea una entrada DNS correspondiente en los servidores DNS de Azure. El FQDN *domainnamelabel*.*location*.cloudapp.azure.com correspondiente se resolverá de manera global en la dirección IP asignada al recurso IP público.

A continuación se verán los diferentes tipos de recursos que pueden asociarse con una dirección IP pública.

### Máquina virtual
Una dirección IP pública está asociada a una [máquina virtual](virtual-machines-about.md) (VM) a través de una tarjeta de interfaz de red (NIC). Cada máquina virtual solo puede tener una dirección IP pública, con independencia del número de tarjetas NIC asociadas a ella. Solo se puede asociar un *recurso de dirección IP pública* asignado *dinámicamente* a una tarjeta NIC de máquina virtual. En el caso de una máquina virtual con varias tarjetas NIC, el *recurso de dirección IP pública* puede asociarse solo a la tarjeta NIC principal.

### Equilibrador de carga de Azure
Se puede asociar una dirección IP pública a una configuración de front-end del [Equilibrador de carga de Azure](load-balancer-overview.md), que actúa como la dirección IP virtual (VIP) con equilibrio de carga accesible a través de Internet. Ambos recursos de IP pública *dinámicos* o *estáticos* pueden asociarse a un equilibrador de carga. Es posible asociar varios *recursos de dirección IP pública* a una configuración de front-end de equilibrador de carga, lo que permite escenarios como un entorno de varios inquilinos con varios sitios web basados en SSL.

### Puerta de enlace de aplicaciones
Se puede asociar una dirección IP pública a una configuración de front-end de [Puerta de enlace de aplicaciones de Azure](application-gateway-introduction.md) para configurarla con una IP virtual (VIP) con equilibrio de carga accesible por Internet. De momento, solo se puede asociar un recurso de dirección IP pública asignado *dinámicamente* a una puerta de enlace de aplicaciones. Sin embargo, es posible asociar varias direcciones IP públicas.

### Puerta de enlace de VPN
Una dirección IP pública debe asociarse a una configuración de IP de [Puerta de enlace de VPN de Azure](vpn-gateway-about-vpngateways.md), en el proceso de creación de la conexión VPN entre una red virtual de Azure y una red local u otra red virtual de Azure. Actualmente, solo se puede asociar un recurso de IP pública asignado *dinámicamente* a una Puerta de enlace de VPN.

### De un vistazo

|Recurso|Asignación estática|Asignación dinámica|Varias direcciones IP|
|---|---|---|---|
|Máquina virtual (VM)/Tarjeta de interfaz de red (NIC)|Sí|No|No|
|Front-end de Equilibrador de carga|Sí|Sí|Sí|
|Front-end de Puerta de enlace de aplicaciones|Sí|No|Sí|
|Puerta de enlace de VPN|Sí|No|No|

## Direcciones IP privadas
Las direcciones IP privadas permiten la comunicación entre los recursos de una red virtual, sin usar direcciones IP accesibles por Internet. Esta dirección IP se asigna desde el intervalo de direcciones de la subred en la red virtual.

### Método de asignación de direcciones IP privadas
Hay dos métodos de asignación de direcciones IP: *dinámico* o *estático*. El método de asignación predeterminado es *dinámico*, cuya dirección IP se asigna mediante DHCP. Además, el método de asignación debe especificarse explícitamente como *estático* junto con la especificación de una dirección IP. En este caso, el recurso se asigna a la dirección IP especificada siempre que se encuentre dentro del intervalo de direcciones de la subred y esté libre (es decir, no asignado a ningún otro recurso).

Hay diferentes tipos de recursos que se pueden asignar a una dirección IP privada. Tenga en cuenta que ambos métodos de asignación (*estático* o *dinámica*) funcionan en todos estos tipos de recursos.

### Máquina virtual
Se asigna una dirección IP privada a una tarjeta de interfaz de red (NIC) de una [máquina virtual](virtual-machines-about.md) (VM). Cada máquina virtual puede tener tantas direcciones IP privadas como el número de tarjetas NIC asociadas a ella.

#### Resolución de nombre de host DNS internos
Todas las máquinas virtuales de Azure se configuran con [servidores DNS administrados por Azure](virtual-networks-name-resolution-for-vms-and-role-instances.md#azure-provided-name-resolution), a menos que se especifique explícitamente durante la creación. En caso de usar servidores DNS administrados por Azure, se crea automáticamente un registro DNS que resuelve el nombre de host de la máquina virtual en la dirección IP privada de la máquina virtual. En el caso de una máquina virtual con varias tarjetas NIC, el nombre de host se resuelve en la dirección IP privada de la tarjeta NIC principal.

### Equilibrador de carga interno
Una dirección IP privada puede asignarse a un front-end de [Equilibrador de carga interno de Azure](load-balancer-internal-overview.md)(ILB), que actúa como la dirección IP virtual (VIP) con equilibrio de carga para los recursos que están dentro de su red virtual. Esto permite el equilibrio de carga sin exponer la dirección IP a Internet.

### Puerta de enlace de aplicaciones
Se puede asignar una dirección IP privada a una [Puerta de enlace de aplicaciones de Azure](application-gateway-introduction.md) para configurarla con un punto de conexión interno no expuesto a Internet, también conocido como punto de conexión del Equilibrador de carga interno (ILB). Los métodos de asignación y comportamiento son similares al ILB descrito anteriormente.

### De un vistazo
|Recurso|Asignación estática|Asignación dinámica|Varias direcciones IP|
|---|---|---|---|
|Máquina virtual (VM)/Tarjeta de interfaz de red (NIC)|Sí|Sí|Sí|
|Front-end de Equilibrador de carga|Sí|Sí|Sí|
|Front-end de Puerta de enlace de aplicaciones|Sí|Sí|Sí|

## Pasos siguientes


[Referencia de Azure PowerShell para redes](https://msdn.microsoft.com/library/azure/mt163510.aspx)

[Idioma de la plantilla del Administrador de recursos de Azure](../resource-group-authoring-templates.md)

[Redes de Azure: plantillas utilizadas habitualmente](https://github.com/Azure/azure-quickstart-templates)

[Proveedor de recursos de proceso](../virtual-machines-azurerm-versus-azuresm)

[Información general del Administrador de recursos de Azure](../resource-group-overview)

[Control de acceso basado en rol en el Administrador de recursos de Azure](https://msdn.microsoft.com/library/azure/dn906885.aspx)

[Uso de etiquetas en el Administrador de recursos de Azure](https://msdn.microsoft.com/library/azure/dn848368.aspx)

[Implementaciones de plantilla](https://msdn.microsoft.com/library/azure/dn790549.aspx)

<!---HONumber=AcomDC_1210_2015-->