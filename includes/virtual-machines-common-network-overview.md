Cuando se crea una máquina virtual (VM) de Azure, es preciso crear una [red virtual](../articles/virtual-network/virtual-networks-overview.md) (VNet) o usar una red virtual existente. También es preciso decidir la forma en que pretende que se acceda a las máquinas virtuales en la red virtual. Es importante [planear antes de crear recursos](../articles/virtual-network/virtual-network-vnet-plan-design-arm.md) y asegurarse de que se conocen los [límites de los recursos de red](../articles/azure-subscription-service-limits.md#networking-limits).

En la siguiente ilustración, las máquinas virtuales se representan como servidores web y servidores de base de datos. Cada conjunto de máquinas virtuales se asignan a subredes separadas en la red virtual.

![red virtual de Azure](./media/virtual-machines-common-network-overview/vnetoverview.png)

Puede crear una red virtual antes de crear una máquina virtual, o bien hacerlo al crearla. Para que se pueda establecer comunicación con una máquina virtual, debe crear estos recursos:

- Interfaces de red
- Direcciones IP
- Red virtual y subredes

Además de estos recursos básicos, también debe considerar estos recursos opcionales:

- Grupos de seguridad de red
- Equilibradores de carga 

## <a name="network-interfaces"></a>Interfaces de red

Una [interfaz de red (NIC)](../articles/virtual-network/virtual-network-network-interface.md) es la interconexión entre una máquina virtual y una red virtual (VNet). Una máquina virtual debe tener al menos una NIC, pero puede tener varias, en función del tamaño de la máquina virtual que se cree. Más información sobre cuántas NIC admite cada tamaño de máquina virtual [Windows](../articles/virtual-machines/windows/sizes.md) o [Linux](../articles/virtual-machines/linux/sizes.md).

Puede crear una máquina virtual con varias NIC y agregarlas o quitarlas a lo largo del ciclo de vida de una máquina virtual. Disponer de varias NIC permite a una máquina virtual conectarse a distintas subredes y enviar o recibir tráfico a través de la interfaz más adecuada.

Si la máquina virtual se agrega a un conjunto de disponibilidad, todas las máquinas virtuales de este deben tener una o varias NIC. No es preciso que las máquinas virtuales con más de una NIC tengan el mismo número de NIC, pero todas ellas deben tener al menos dos.

Todas las NIC conectadas a una máquina virtual deben existir en la misma ubicación y suscripción que esta. Cada NIC debe estar conectada a una red virtual que exista en la misma ubicación y suscripción de Azure en la que se encuentre la NIC. Una vez creada la NIC, puede cambiar la subred a la que la máquina virtual está conectada, pero no la red virtual. A cada NIC conectada a una máquina virtual se le asigna una dirección MAC que no cambia hasta que se elimine la máquina virtual.

En esta tabla se enumeran los métodos que se pueden usar para crear una interfaz de red.

| Método | Descripción |
| ------ | ----------- |
| Portal de Azure | Cuando se crea una máquina virtual en Azure Portal, se crea automáticamente una interfaz de red (no se puede usar una NIC que se cree de manera independiente). El portal crea una máquina virtual con una sola NIC. Si desea crear una máquina virtual con más de una, debe usar otro método para hacerlo. |
| [Azure PowerShell](../articles/virtual-machines/windows/multiple-nics.md) | Use [New-AzureRmNetworkInterface](/powershell/module/azurerm.network/new-azurermnetworkinterface) con el parámetro **PublicIpAddressId -** para proporcionar el identificador de la dirección IP pública que creó anteriormente. |
| [CLI de Azure](../articles/virtual-machines/linux/multiple-nics.md) | Para proporcionar el identificador de la dirección IP pública que creó anteriormente, use [az network nic create](https://docs.microsoft.com/cli/azure/network/nic#create) con el parámetro **--public-ip-address**. |
| [Plantilla](../articles/virtual-network/virtual-network-deploy-multinic-arm-template.md) | Use [Network Interface in a Virtual Network with Public IP Address](https://github.com/Azure/azure-quickstart-templates/tree/master/101-nic-publicip-dns-vnet) (Interfaz de red en una red virtual con dirección IP pública) como guía para implementar una interfaz de red mediante una plantilla. |

## <a name="ip-addresses"></a>Direcciones IP 

En Azure se pueden asignar estos tipos de [direcciones IP](../articles/virtual-network/virtual-network-ip-addresses-overview-arm.md) a una NIC:

- **Direcciones IP públicas**: se usan para la comunicación entrante y saliente [sin traducción de direcciones de red (NAT)] con Internet y otros recursos de Azure no conectados a una red virtual. La asignación de una dirección IP pública a una NIC es opcional. Las direcciones IP públicas tienen un cargo nominal y se puede usar un número máximo de ellas por suscripción.
- **Direcciones IP privadas**: se usan para la comunicación dentro de una red virtual, una red local e Internet (con NAT). Debe asignar al menos una dirección IP privada a una máquina virtual. Para más información acerca de NAT en Azure, lea [Información sobre las conexiones salientes en Azure](../articles/load-balancer/load-balancer-outbound-connections.md).

Las direcciones IP públicas se pueden asignar a las máquinas virtuales o a los equilibradores de carga con acceso a Internet. Las direcciones IP privadas se pueden asignar a las máquinas virtuales y a los equilibradores de carga internos. Las direcciones IP se asignan a una máquina virtual mediante una interfaz de red.

Existen dos métodos en los que se asigna una dirección IP a un recurso: dinámico o estático. El predeterminado es el dinámico, en el que no se asigna ninguna dirección IP durante la creación. En su lugar, la dirección IP se asigna cuando se crea una máquina virtual o se inicia una máquina virtual detenida. La dirección IP se libera cuando se detiene o se elimina la máquina virtual. 

Para asegurarse de que la dirección IP de la máquina virtual no cambia, puede establecer explícitamente el método de asignación en estático. En este caso, se asigna de inmediato una dirección IP. Solo se libera cuando se elimina la máquina virtual o cuando se cambia su método de asignación a dinámico.
    
En esta tabla se enumeran los métodos que se pueden usar para crear una dirección IP.

| Método | Descripción |
| ------ | ----------- |
| [Portal de Azure](../articles/virtual-network/virtual-network-deploy-static-pip-arm-portal.md) | De manera predeterminada, las direcciones IP públicas son dinámicas y la dirección asociada a ellas puede cambiar cuando la máquina virtual se detiene o elimina. Para garantizar que la máquina virtual siempre usa la misma dirección IP pública, cree una dirección IP pública estática. De manera predeterminada, el portal asigna una dirección IP privada dinámica a una NIC al crear una máquina virtual. Una vez creada esta, puede cambiar la dirección IP a estática.|
| [Azure PowerShell](../articles/virtual-network/virtual-network-deploy-static-pip-arm-ps.md) | Use [New-AzureRmPublicIpAddress](/powershell/module/azurerm.network/new-azurermpublicipaddress) con el parámetro **AllocationMethod -** como Dynamic o Static. |
| [CLI de Azure](../articles/virtual-network/virtual-network-deploy-static-pip-arm-cli.md) | Use [az network public-ip create](https://docs.microsoft.com/cli/azure/network/public-ip#create) con el parámetro **--allocation-method** como Dynamic o Static. |
| [Plantilla](../articles/virtual-network/virtual-network-deploy-static-pip-arm-template.md) | Use [Network Interface in a Virtual Network with Public IP Address](https://github.com/Azure/azure-quickstart-templates/tree/master/101-nic-publicip-dns-vnet) (Interfaz de red en una red virtual con dirección IP pública) como guía para implementar una dirección IP pública mediante una plantilla. |

Después de crear una dirección IP pública, se puede asociar a una máquina virtual. Para ello, es preciso asignarla a una NIC.

## <a name="virtual-network-and-subnets"></a>Red virtual y subredes

Una subred es un intervalo de direcciones IP en la red virtual. Una red virtual se puede dividir en varias subredes para facilitar su organización o por motivos de seguridad. Cada una de las NIC de una máquina virtual está conectada a una subred de una red virtual. Las NIC conectadas a subredes (iguales o distintas) dentro de una red virtual pueden comunicarse entre sí sin ninguna configuración adicional.

Al configurar una red virtual, especifique la topología, incluidos los espacios de direcciones y las subredes disponibles. Si la red virtual se va a conectar a otras redes virtuales o a redes locales, es preciso seleccionar intervalos de direcciones que no se superpongan. Las direcciones IP son privadas y no se puede acceder a ellas desde Internet, lo cual sucedía solo en el caso de las direcciones IP no enrutables como 10.0.0.0/8, 172.16.0.0/12 o 192.168.0.0/16. Ahora, Azure trata todos los intervalos de direcciones como parte del espacio de direcciones IP de redes virtuales privadas al que solo se puede acceder dentro de la red virtual, de redes virtuales interconectadas y desde su ubicación local. 

Si trabaja en una organización en la que otra persona es responsable de las redes internas, póngase en contacto con ella antes de seleccionar el espacio de direcciones. Asegúrese de que no se superpone a otro e infórmele acerca del espacio que desea usar, con el fin de que no intente usar el mismo intervalo de direcciones IP. 

De forma predeterminada, no hay ningún límite de seguridad entre subredes, por lo que las máquinas virtuales de cada una de estas subredes pueden comunicarse entre sí. Sin embargo, se pueden configurar grupos de seguridad de red (NSG), que permiten controlar el flujo de tráfico que llega y sale tanto de las subredes como de las máquinas virtuales. 

En esta tabla se enumeran los métodos que se pueden usar para crear una red virtual y subredes. 

| Método | Descripción |
| ------ | ----------- |
| [Portal de Azure](../articles/virtual-network/virtual-networks-create-vnet-arm-pportal.md) | Si deja que Azure cree una red virtual cuando cree una máquina virtual, el nombre será una combinación del nombre del grupo de recursos que contiene la red virtual y **- vnet**. El espacio de direcciones será 10.0.0.0/24, el nombre de subred requerida será **default** y el intervalo de direcciones de la subred será 10.0.0.0/24. |
| [Azure PowerShell](../articles/virtual-network/virtual-networks-create-vnet-arm-ps.md) | Para crear una subred y una red virtual se usan [New-AzureRmVirtualNetworkSubnetConfig](https://docs.microsoft.com/powershell/resourcemanager/AzureRM.Network/v1.0.13/New-AzureRmVirtualNetworkSubnetConfig) y [New-AzureRmVirtualNetwork](https://docs.microsoft.com/powershell/resourcemanager/AzureRM.Network/v1.0.13/New-AzureRmVirtualNetwork). También se puede usar [AzureRmVirtualNetworkSubnetConfig agregar](/powershell/module/azurerm.network/add-azurermvirtualnetworksubnetconfig) para agregar una subred a una red virtual existente. |
| [CLI de Azure](../articles/virtual-network/virtual-networks-create-vnet-arm-cli.md) | La subred y la red virtual se crean al mismo tiempo. Especifique el parámetro **--subnet-name** en [az network vnet create](https://docs.microsoft.com/cli/azure/network/vnet#create) con el nombre de la subred. |
| [Plantilla](../articles/virtual-network/virtual-networks-create-vnet-arm-template-click.md) | La manera más fácil de crear una red virtual y subredes es descargar una plantilla existente, como [Virtual Network with two Subnets](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vnet-two-subnets) (Red virtual con dos subredes) y modificarla hasta ajustarla a sus necesidades. |

## <a name="network-security-groups"></a>Grupos de seguridad de red

Un [grupo de seguridad de red (NSG)](../articles/virtual-network/virtual-networks-nsg.md) contiene una lista de reglas de la lista de control de acceso (ACL) que permiten o deniegan el tráfico de red a subredes, NIC, o ambas. Los grupos de seguridad de red se pueden asociar con subredes o con NIC individuales conectadas a una subred. Si un grupo de seguridad de red está asociado a una subred, las reglas de la ACL se aplican a todas las máquinas virtuales de dicha subred. Además, se puede limitar el tráfico que llega a una NIC individual, para lo que se debe asociar un NSG directamente a la NIC.

Contiene dos tipos de reglas: de entrada y de salida. La prioridad de una regla debe ser única dentro de cada conjunto. Cada regla tiene propiedades de protocolo, intervalos de puertos de origen y destino, prefijos de direcciones, dirección de tráfico, prioridad y tipo de acceso. 

Todos los grupos de seguridad de red contienen un conjunto de reglas predeterminadas. No se pueden eliminar las reglas predeterminadas, pero dado que tienen asignada la mínima prioridad, pueden reemplazarse por las reglas que cree. 

Al asociar un grupo de seguridad de red a una NIC, las reglas de acceso de red del grupo de seguridad de red se aplican solo a esa NIC. Si se aplica un grupo de seguridad de red a una sola NIC en una máquina virtual con varias NIC, el tráfico de las restantes no se verá afectado. Se pueden asociar distintos grupos de seguridad de red a una NIC (o a una máquina virtual, según el modelo de implementación) y a la subred a la que una NIC o una máquina virtual están enlazadas. La prioridad depende de la dirección del tráfico.

Cuando planee las máquinas virtuales y red virtual, no olvide [planear](../articles/virtual-network/virtual-networks-nsg.md#planning) los grupos de seguridad de red.

En esta tabla se enumeran los métodos que se pueden usar para crear un grupo de seguridad de red.

| Método | Descripción |
| ------ | ----------- |
| [Portal de Azure](../articles/virtual-network/virtual-networks-create-nsg-arm-pportal.md) | Cuando se crea una máquina virtual en Azure Portal, se crea automáticamente un grupo de seguridad de red, que se asocia a la NIC que crea el portal. El nombre de dicho grupo es una combinación del nombre de la máquina virtual y **- nsg**. Este grupo de seguridad de red contiene una regla de entrada con una prioridad de 1000, un servicio establecido en RDP, el protocolo establecido en TCP, el puerto establecido en 3389 y la acción establecida en Allow (Permitir). Si desea permitir que otro tráfico de entrada a la máquina virtual, debe agregar más reglas al NSG. |
| [Azure PowerShell](../articles/virtual-network/virtual-networks-create-nsg-arm-ps.md) | Use [New-AzureRmNetworkSecurityRuleConfig](https://docs.microsoft.com/powershell/resourcemanager/AzureRM.Network/v1.0.13/New-AzureRmNetworkSecurityRuleConfig) y especifique la información de las reglas requerida. Use [New-AzureRmNetworkSecurityGroup](https://docs.microsoft.com/powershell/resourcemanager/AzureRM.Network/v1.0.13/New-AzureRmNetworkSecurityGroup) para crear el grupo de seguridad de red. Use [Set-AzureRmVirtualNetworkSubnetConfig](https://docs.microsoft.com/powershell/resourcemanager/AzureRM.Network/v1.0.13/Set-AzureRmVirtualNetworkSubnetConfig) para configurar el grupo de seguridad de red para la subred. Use [Set-AzureRmVirtualNetwork](/powershell/module/azurerm.network/set-azurermvirtualnetwork) para agregar el grupo de seguridad de red a la red virtual. |
| [CLI de Azure](../articles/virtual-network/virtual-networks-create-nsg-arm-cli.md) | Use [az network nsg create](https://docs.microsoft.com/cli/azure/network/nsg#create) para crear inicialmente el grupo de seguridad de red. Use [az network nsg rule create](https://docs.microsoft.com/cli/azure/network/nsg/rule#create) para agregar reglas al grupo de seguridad de red. Use [az network vnet subnet update](https://docs.microsoft.com/cli/azure/network/vnet/subnet#update) para agregar el grupo de seguridad de red a la subred. |
| [Plantilla](../articles/virtual-network/virtual-networks-create-nsg-arm-template.md) | Use [Create a Network Security Group](https://github.com/Azure/azure-quickstart-templates/tree/master/101-security-group-create) (Creación de un grupo de seguridad de red) como guía para la implementación de un grupo de seguridad de red mediante una plantilla. |

## <a name="load-balancers"></a>Equilibradores de carga

[Azure Load Balancer](../articles/load-balancer/load-balancer-overview.md) proporciona una alta disponibilidad y un elevado rendimiento de red a las aplicaciones. Se puede configurar un equilibrador de carga para [equilibrar el tráfico entrante de Internet](../articles/load-balancer/load-balancer-internet-overview.md) a las máquinas virtuales o [equilibrar el tráfico entre las máquinas virtuales de una red virtual](../articles/load-balancer/load-balancer-internal-overview.md). Un equilibrador de carga también puede equilibrar el tráfico entre los equipos locales y las máquinas virtuales de una red entre entornos, o reenviar el tráfico externo a una máquina virtual concreta.

El equilibrador de carga asigna el tráfico entrante y saliente entre la dirección IP pública y el puerto del equilibrador de carga y la dirección IP privada y puerto de la máquina virtual.

Al crear un equilibrador de carga, también es preciso tener en cuenta estos elementos de configuración:

- **Configuración de IP de front-end**: un equilibrador de carga puede incluir una o varias direcciones IP de front-end, conocidas también como IP virtuales (VIP). Estas direcciones IP sirven como entrada para el tráfico.
- **Grupo de direcciones de back-end**: direcciones IP que están asociadas a la NIC a la que se distribuye la carga.
- **Reglas NAT**: define el flujo del tráfico entrante a través de la IP de front-end y su distribución a la IP de back-end.
- **Reglas del equlibrador de carga**: asigna una combinación dada de IP de front-end y puerto a una combinación de un conjunto de direcciones IP de back-end y puerto. Un solo equilibrador de carga puede tener varias reglas de equilibrio de carga. Cada regla tiene una combinación de una IP de front-end y un puerto y una IP de back-end asociados a las máquinas virtuales.
- **[Sondeos](../articles/load-balancer/load-balancer-custom-probe-overview.md)**: supervisa el estado de las máquinas virtuales. Si un sondeo no responde, el equilibrador de carga deja de enviar nuevas conexiones a la máquina virtual que no funciona correctamente. Las conexiones existentes no resultan afectadas y las nuevas conexiones se envían a las máquinas virtuales que están en buen estado.

En esta tabla se enumeran los métodos que se pueden usar para crear un equilibrador de carga con acceso a Internet.

| Método | Descripción |
| ------ | ----------- |
| Portal de Azure | Actualmente no se puede crear equilibrador de carga con acceso Internet mediante Azure Portal. |
| [Azure PowerShell](../articles/load-balancer/load-balancer-get-started-internet-arm-ps.md) | Para proporcionar el identificador de la dirección IP pública que creó anteriormente, use [New-AzureRmLoadBalancerFrontendIpConfig](https://docs.microsoft.com/powershell/resourcemanager/AzureRM.Network/v1.0.13/New-AzureRmLoadBalancerFrontendIpConfig) con el parámetro **-PublicIpAddress**. Use [New-AzureRmLoadBalancerBackendAddressPoolConfig](https://docs.microsoft.com/powershell/resourcemanager/AzureRM.Network/v1.0.13/New-AzureRmLoadBalancerBackendAddressPoolConfig) para crear la configuración del grupo de direcciones de back-end. Use [New-AzureRmLoadBalancerInboundNatRuleConfig](https://docs.microsoft.com/powershell/resourcemanager/AzureRM.Network/v1.0.13/New-AzureRmLoadBalancerInboundNatRuleConfig) para crear reglas NAT de entrada asociadas a la configuración de la IP de front-end que ha creado. Use [New-AzureRmLoadBalancerProbeConfig](https://docs.microsoft.com/powershell/resourcemanager/AzureRM.Network/v1.0.13/New-AzureRmLoadBalancerProbeConfig) para crear los sondeos que necesite. Use [New-AzureRmLoadBalancerRuleConfig](https://docs.microsoft.com/powershell/resourcemanager/AzureRM.Network/v1.0.13/New-AzureRmLoadBalancerRuleConfig) para crear la configuración del equilibrador de carga. Use [New-AzureRmLoadBalancer](/powershell/module/azurerm.network/new-azurermloadbalancer) para crear el equilibrador de carga.|
| [CLI de Azure](../articles/load-balancer/load-balancer-get-started-internet-arm-cli.md) | Use [az network lb create](https://docs.microsoft.com/cli/azure/network/lb#create) para crear la configuración inicial del equilibrador de carga. Use [az network lb frontend-ip create](https://docs.microsoft.com/cli/azure/network/lb/frontend-ip#create) para agregar la dirección IP pública que creó anteriormente. Use [az network lb address-pool create](https://docs.microsoft.com/cli/azure/network/lb/address-pool#create) para agregar la configuración del grupo de direcciones de back-end. Use [az network lb inbound-nat-rule create](https://docs.microsoft.com/cli/azure/network/lb/inbound-nat-rule#create) para agregar reglas NAT. Use [az network lb rule create](https://docs.microsoft.com/cli/azure/network/lb/rule#create) para agregar las reglas del equilibrador de carga. Use [az network lb probe create](https://docs.microsoft.com/cli/azure/network/lb/probe#create) para agregar los sondeos. |
| [Plantilla](../articles/load-balancer/load-balancer-get-started-internet-arm-template.md) | Use [2 VMs in a Load Balancer and configure NAT rules on the LB](https://github.com/Azure/azure-quickstart-templates/tree/master/201-2-vms-loadbalancer-natrules) (Dos máquinas virtuales en un equilibrador de carga y configurar reglas NAT en el equilibrador de carga) como guía para implementar un equilibrador de carga mediante una plantilla. |
    
En esta tabla se enumeran los métodos que se pueden usar para crear un equilibrador de carga interno.

| Método | Descripción |
| ------ | ----------- |
| Portal de Azure | Actualmente no se puede crear equilibrador de carga interno mediante Azure Portal. |
| [Azure PowerShell](../articles/load-balancer/load-balancer-get-started-ilb-arm-ps.md) | Para proporcionar una dirección IP privada en la subred de la red, utilice [New-AzureRmLoadBalancerFrontendIpConfig](https://docs.microsoft.com/powershell/resourcemanager/AzureRM.Network/v1.0.13/New-AzureRmLoadBalancerFrontendIpConfig) con el parámetro **-PrivateIpAddress**. Use [New-AzureRmLoadBalancerBackendAddressPoolConfig](https://docs.microsoft.com/powershell/resourcemanager/AzureRM.Network/v1.0.13/New-AzureRmLoadBalancerBackendAddressPoolConfig) para crear la configuración del grupo de direcciones de back-end. Use [New-AzureRmLoadBalancerInboundNatRuleConfig](https://docs.microsoft.com/powershell/resourcemanager/AzureRM.Network/v1.0.13/New-AzureRmLoadBalancerInboundNatRuleConfig) para crear reglas NAT de entrada asociadas a la configuración de la IP de front-end que ha creado. Use [New-AzureRmLoadBalancerProbeConfig](https://docs.microsoft.com/powershell/resourcemanager/AzureRM.Network/v1.0.13/New-AzureRmLoadBalancerProbeConfig) para crear los sondeos que necesite. Use [New-AzureRmLoadBalancerRuleConfig](https://docs.microsoft.com/powershell/resourcemanager/AzureRM.Network/v1.0.13/New-AzureRmLoadBalancerRuleConfig) para crear la configuración del equilibrador de carga. Use [New-AzureRmLoadBalancer](/powershell/module/azurerm.network/new-azurermloadbalancer) para crear el equilibrador de carga.|
| [CLI de Azure](../articles/load-balancer/load-balancer-get-started-ilb-arm-cli.md) | Use el comando [az network lb create](https://docs.microsoft.com/cli/azure/network/lb#create) para crear la configuración inicial del equilibrador de carga. Para definir la dirección IP privada, utilice [az network lb frontend-ip create](https://docs.microsoft.com/cli/azure/network/lb/frontend-ip#create) con el parámetro **--private-ip-address**. Use [az network lb address-pool create](https://docs.microsoft.com/cli/azure/network/lb/address-pool#create) para agregar la configuración del grupo de direcciones de back-end. Use [az network lb inbound-nat-rule create](https://docs.microsoft.com/cli/azure/network/lb/inbound-nat-rule#create) para agregar reglas NAT. Use [az network lb rule create](https://docs.microsoft.com/cli/azure/network/lb/rule#create) para agregar las reglas del equilibrador de carga. Use [az network lb probe create](https://docs.microsoft.com/cli/azure/network/lb/probe#create) para agregar los sondeos.|
| [Plantilla](../articles/load-balancer/load-balancer-get-started-ilb-arm-template.md) | Use [2 VMs in a Load Balancer and configure NAT rules on the LB](https://github.com/Azure/azure-quickstart-templates/tree/master/201-2-vms-internal-load-balancer) (Dos máquinas virtuales en un equilibrador de carga y configurar relas NAT en el equilibrador de carga) como guía para implementar un equilibrador de carga mediante una plantilla. |

## <a name="vms"></a>Máquinas virtuales

Las máquinas virtuales se pueden crear en la misma red virtual y se pueden conectar entre sí mediante direcciones IP privadas. Pueden conectarse aunque estén en subredes diferentes sin necesidad de configurar una puerta de enlace o utilizar direcciones IP públicas. Para poner máquinas virtuales en una red virtual, cree la red virtual y, después, al crear cada máquina virtual, asígnela a la red virtual y la subred. Las máquinas virtuales adquieren su configuración de red durante la implementación o el inicio.  

A las máquinas virtuales se les asigna una dirección IP cuando se implementan. Si implementa varias máquinas virtuales en una red virtual o una subred, se les asignan direcciones IP cuando arrancan. Una dirección IP dinámica (DIP) es la dirección IP interna asociada a una máquina virtual. Puede asignar una DIP estática a una máquina virtual. Si asigna una DIP estática, debe plantearse la posibilidad de utilizar una subred específica para evitar reutilizar accidentalmente una DIP estática para otra máquina virtual.  

Si crea una máquina virtual y posteriormente desea migrarla a una red virtual, no es un cambio de configuración simple. Debe volver a implementar la máquina virtual en la red virtual. La manera más fácil de volver a implementarla es eliminar la máquina virtual, pero no los discos conectados a ella y, después, volver a crear la máquina virtual con los discos originales en la red virtual. 

En esta tabla se enumeran los métodos que se pueden usar para crear una máquina virtual en una red virtual.

| Método | Descripción |
| ------ | ----------- |
| [Azure Portal](../articles/virtual-machines/windows/quick-create-portal.md) | Utiliza la configuración de red predeterminada que se mencionó anteriormente para crear una máquina virtual con una NIC única. Para crear una máquina virtual con varias NIC, es preciso usar otro método. |
| [Azure PowerShell](../articles/virtual-machines/windows/tutorial-manage-vm.md) | Incluye el uso de [Add-AzureRmVMNetworkInterface](/powershell/module/azurerm.compute/add-azurermvmnetworkinterface) para agregar la NIC que creó anteriormente a la configuración de la máquina virtual. |
| [CLI de Azure](../articles/virtual-machines/linux/create-cli-complete.md) | Cree y conecte una máquina virtual a una red virtual, subred y NIC creadas mediante pasos individuales. |
| [Plantilla](../articles/virtual-machines/windows/ps-template.md) | Use [Very simple deployment of a Windows VM](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-simple-windows) (Implementación muy simple de una máquina virtual Windows) como guía para la implementación de una máquina virtual mediante una plantilla. |

## <a name="next-steps"></a>Pasos siguientes
Para conocer los pasos específicos para máquinas virtuales sobre cómo administrar redes virtuales de Azure en máquinas virtuales, consulte los tutoriales de [Windows](../articles/virtual-machines/windows/tutorial-virtual-network.md) o [Linux](../articles/virtual-machines/linux/tutorial-virtual-network.md).

También hay tutoriales sobre cómo equilibrar la carga de las máquinas virtuales y crear aplicaciones de alta disponibilidad para [Windows](../articles/virtual-machines/windows/tutorial-load-balancer.md) o [Linux](../articles/virtual-machines/linux/tutorial-load-balancer.md).

- Aprenda a configurar [rutas definidas por el usuario y el reenvío IP](../articles/virtual-network/virtual-networks-udr-overview.md). 
- Aprenda a configurar [conexión de red virtual a red virtual](../articles/vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md).
- Aprenda a [solucionar problemas de rutas](../articles/virtual-network/virtual-network-routes-troubleshoot-portal.md).