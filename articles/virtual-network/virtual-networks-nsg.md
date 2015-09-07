<properties 
   pageTitle="Qué es un grupo de seguridad de red"
	description="Información acerca de los grupos de seguridad de red"
	services="virtual-network"
	documentationCenter="na"
	authors="telmosampaio"
	manager="carolz"
	editor="tysonn"/>
<tags 
   ms.service="virtual-network"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="infrastructure-services"
	ms.date="08/13/2015"
	ms.author="telmos"/>

# ¿Qué es un grupo de seguridad de red?

Puede usar un grupo de seguridad de red para controlar el tráfico a una o más instancias de máquina virtual en la red virtual. Un grupo de seguridad de red es un objeto de nivel superior que está asociado a la suscripción. Un grupo de seguridad de red contiene reglas de control de acceso que permiten o deniegan el tráfico a las instancias de máquina virtual. Las reglas de un grupo de seguridad de red pueden cambiarse en cualquier momento; los cambios se aplican a todas las instancias asociadas. Para utilizar un grupo de seguridad de red debe tener una red virtual asociada a una región (ubicación).

>[AZURE.WARNING]Los grupos de seguridad de red no son compatibles con redes virtuales asociadas a un grupo de afinidad. Si no tiene una red virtual regional y desea controlar el tráfico a los extremos, consulte [¿Qué es una lista de control de acceso (ACL) de red?](./virtual-networks-acl.md). También puede [migrar la red virtual a una red virtual regional](./virtual-networks-migrate-to-regional-vnet.md).

Puede asociar un grupo de seguridad de red a una máquina virtual o a una subred dentro de una red virtual. Cuando se asocia a una máquina virtual, el grupo de seguridad de red se aplica a todo el tráfico enviado y recibido por la instancia de máquina virtual. Cuando se aplica a una subred de la red virtual, se aplica a todo el tráfico enviado y recibido por TODAS las instancias de máquina virtual de la subred. Una máquina virtual o una subred solo puede asociarse a un grupo de seguridad de red y cada grupo de seguridad de red puede contener hasta 200 reglas. Puede tener 100 grupos de seguridad de red por suscripción.

>[AZURE.NOTE]No se admiten ACL basadas en el extremo y grupos de seguridad de red en la misma instancia de máquina virtual. Si desea usar un grupo de seguridad de red y ya tiene un extremo del ACL, quite primero el extremo del ACL. Para obtener información acerca de cómo hacerlo, consulte [Administración de listas de control de acceso (ACL) para extremos mediante PowerShell](virtual-networks-acl-powershell.md).

## ¿Cómo funciona un grupo de seguridad de red?

Los grupos de seguridad de red son diferentes de las ACL basadas en extremos. Las ACL de extremo solo funcionan en el puerto público que se expone a través del extremo de entrada. Un grupo de seguridad de red funciona en una o más instancias de máquina virtual y controla todo el tráfico es entrante y saliente de la máquina virtual.

Un grupo de seguridad de red tiene un *Nombre*, está asociado a una *Región* y tiene una etiqueta descriptiva. Contiene dos tipos de reglas, **entrante** y **saliente**. Se aplican las reglas de entrada en los paquetes entrantes a una máquina virtual y las reglas de salida se aplican a los paquetes salientes de la máquina virtual. Las reglas se aplican en el host donde se encuentra la máquina virtual. Un paquete entrante o saliente debe coincidir con una regla **Permitir** para que se permita; de no ser así, se quitará.

Las reglas se procesan en el orden de prioridad. Por ejemplo, una regla con un número de prioridad más bajo (por ejemplo, 100) se procesa antes que las reglas con un número de prioridad más alto (por ejemplo, 200). Una vez que se encuentra una coincidencia, no se procesan más reglas.

Una regla especifica lo siguiente:

- **Nombre:** un identificador único para la regla

- **Tipo:** entrante y saliente

- **Prioridad:** <You can specify an integer between 100 and 4096>

- **Dirección IP de origen:** CIDR de intervalo de IP de origen

- **Intervalo de puertos de origen** <integer or range between 0 and 65536>

- **Intervalo de IP de destino:** CIDR del intervalo de IP de destino

- **Intervalo de puertos de destino:** <integer or range between 0 and 65536>

- **Protocolo:** <se permite TCP, UDP o '*'>

- **Acceso:** permitir o denegar

### Reglas predeterminadas

Un grupo de seguridad de red contiene las reglas predeterminadas. No se pueden eliminar las reglas predeterminadas, pero dado que tienen asignada la mínima prioridad, pueden reemplazarse por las reglas que cree. Las reglas predeterminadas describen la configuración predeterminada recomendada por la plataforma. Como se muestra en las siguientes reglas predeterminadas, se permite el tráfico que se origina y termina en una red virtual en las direcciones tanto de entrada como de salida.

A pesar de que la conectividad a Internet está permitida para la dirección de salida, está bloqueada para la dirección de entrada de manera predeterminada. Hay una regla predeterminada para que el equilibrador de carga de Azure pueda sondear el mantenimiento de la máquina virtual. Puede invalidar esta regla si la máquina virtual o el conjunto de máquinas virtuales en el grupo de seguridad de red no participa en el conjunto de carga equilibrada.

Las reglas predeterminadas son:

**Entrada**

| Nombre | Prioridad | IP de origen | Puerto de origen | IP de destino | Puerto de destino | Protocolo | Access |
|-----------------------------------|----------|--------------------|-------------|-----------------|------------------|----------|--------|
| ALLOW VNET INBOUND (PERMITIR ENTRANTE DE RED VIRTUAL) | 65000 | VIRTUAL\_NETWORK | * | VIRTUAL\_NETWORK | * | * | PERMITIR |
| ALLOW AZURE LOAD BALANCER INBOUND (PERMITIR ENTRANTE DEL EQUILIBRADOR DE CARGA DE AZURE) | 65001 | AZURE\_LOADBALANCER | * | * | * | * | PERMITIR |
| DENY ALL INBOUND (DENEGAR TODO EL TRÁFICO ENTRANTE) | 65500 | * | * | * | * | * | DENEGAR |

**Salida**

| Nombre | Prioridad | IP de origen | Puerto de origen | IP de destino | Puerto de destino | Protocolo | Access |
|-------------------------|----------|-----------------|-------------|-----------------|------------------|----------|--------|
| ALLOW VNET OUTBOUND (PERMITIR SALIENTE DE RED VIRTUAL) | 65000 | VIRTUAL\_NETWORK | * | VIRTUAL\_NETWORK | * | * | PERMITIR |
| ALLOW INTERNET OUTBOUND (PERMITIR SALIENTE DE INTERNET) | 65001 | * | * | INTERNET | * | * | PERMITIR |
| DENY ALL OUTBOUND (DENEGAR TODO EL TRÁFICO SALIENTE) | 65500 | * | * | * | * | * | DENEGAR |

### Reglas especiales de infraestructura

Las reglas de los grupos de seguridad de red son explícitas. No se permite ningún tráfico o se deniega más allá de lo especificado en las reglas de los grupos de seguridad de red. Sin embargo, existen dos tipos de tráfico que siempre se permiten, independientemente de la especificación del grupo de seguridad de red. Estas disposiciones se establecen para admitir la infraestructura.

- **IP virtual del nodo de host:** servicios de infraestructura básica, como DHCP, DNS y seguimiento de estado, se proporcionan a través de la dirección IP 168.63.129.16 del host virtualizado. Esta dirección IP pública pertenece a Microsoft y será la única dirección IP virtualizada que se usará en todas las regiones con este fin. Esta dirección IP se asigna a la dirección IP física del equipo del servidor (nodo de host) que hospeda la máquina virtual. El nodo de host actúa como la retransmisión DHCP, la resolución recursiva de DNS y el origen de sonda del sondeo de mantenimiento del equilibrador de carga y el sondeo de mantenimiento del equipo. La comunicación con esta dirección IP no se debe considerar como un ataque.

- **Licencias (servicio de administración de claves):** las imágenes de Windows que se ejecutan en las máquinas virtuales deben contar con licencia. Para ello, se debe enviar una solicitud de licencia a los servidores host del servicio de administración de claves que administran dichas consultas. Esto siempre se hará en el puerto de salida 1688.

### Etiquetas predeterminadas

Las etiquetas predeterminadas son identificadores proporcionados por el sistema para tratar una categoría de direcciones IP. Las etiquetas predeterminadas se pueden especificar en las reglas definidas por el cliente. Las etiquetas predeterminadas son las siguientes:

- **VIRTUAL\_NETWORK:** esta etiqueta predeterminada denota todo el espacio de dirección de red. Incluye el espacio de direcciones de red virtual (CIDR de IP en Azure), así como todos los espacios de direcciones locales conectados (redes locales). Esto también incluye espacios de direcciones VNet a VNet.

- **AZURE\_LOADBALANCER:** esta etiqueta predeterminada denota el equilibrador de carga de la infraestructura de Azure. Esto se traducirá en una IP de centro de datos de Azure donde se originarán los sondeos de mantenimiento de Azure. Esto solo es necesario si la máquina virtual o un conjunto de máquinas virtuales asociado al grupo de seguridad de red participa en un conjunto de carga equilibrada.

- **INTERNET:** esta etiqueta predeterminada denota el espacio de dirección IP que se encuentra fuera de la red virtual y es accesible mediante Internet pública. Este intervalo incluye además un espacio de IP pública propiedad de Azure.

### Puertos e intervalos de puertos

Las reglas del grupo de seguridad de red se pueden especificar para un solo puerto de origen/destino o para un intervalo de puertos. Esto resulta especialmente útil en casos en los que desea abrir un amplio intervalo de puertos para una aplicación, como FTP. El intervalo solo puede ser secuencial y no se puede combinar con la especificación de puerto individual.

Para especificar un intervalo de puertos, use el signo ‘-’, como se muestra a continuación en el parámetro *DestinationPortRange*:

	Get-AzureNetworkSecurityGroup -Name ApptierSG `
	| Set-AzureNetworkSecurityRule -Name FTP -Type Inbound -Priority 600 -Action Allow `
		-SourceAddressPrefix INTERNET -SourcePortRange * `
		-DestinationAddressPrefix * -DestinationPortRange 100-500 -Protocol *

### Tráfico ICMP

Las reglas de los grupos de seguridad de red actuales solo permiten los protocolos ‘TCP’ o ‘UDP’. No hay una etiqueta específica para 'ICMP'. Sin embargo, el tráfico ICMP se permite dentro de una red virtual de manera predeterminada gracias a las reglas de red virtual de entrada que permiten el tráfico desde/hacia cualquier puerto y protocolo ' *' dentro de la red virtual.

## Asociación de grupos de seguridad de red

Asociación de un grupo de seguridad de red a una máquina virtual: cuando un grupo de seguridad de red está directamente asociado a una máquina virtual, las reglas de acceso de red en el grupo de seguridad de red se aplican directamente a todo el tráfico destinado a la máquina virtual. Cada vez que se actualiza el grupo de seguridad de red debido a cambios en las reglas, los cambios se reflejan en el control del tráfico en unos minutos. Cuando se elimina la asociación del grupo de seguridad de red a la máquina virtual, el estado vuelve a lo que era antes del grupo de seguridad de red; es decir, se usarán los valores predeterminados del sistema antes de entrar en el grupo de seguridad de red.

Asociación de un grupo de seguridad de red a una subred: cuando un grupo de seguridad de red está asociado a una subred, se aplican las reglas de acceso a la red en el grupo de seguridad de red a todas las máquinas virtuales de la subred. Cada vez que se actualizan las reglas de acceso en el grupo de seguridad de red, los cambios se aplican a todas las máquinas virtuales en la subred en unos minutos.

Asociación de un grupo de seguridad de red a una subred y una máquina virtual: es posible asociar un grupo de seguridad de red a una máquina virtual y otro grupo de seguridad de red diferente a la subred donde reside la máquina virtual. Esto está admitido y, en este caso, la máquina virtual obtiene dos capas de protección. En el tráfico entrante el paquete pasa por las reglas de acceso especificadas en la subred y,después, por las reglas de la máquina virtual y en el tráfico saliente pasa primero por las reglas especificadas en la máquina virtual, antes de pasar por las reglas especificadas en la subred, como se ilustra en el diagrama siguiente.

![ACL de grupos de seguridad de red](./media/virtual-networks-nsg/figure1.png)

Cuando un grupo de seguridad de red está asociado a una máquina virtual o una subred, las reglas de control de acceso de la red pasan a ser muy explícitas. La plataforma no insertará ninguna regla implícita para permitir el tráfico a un puerto determinado. En este caso, si crea un extremo en la máquina virtual, también debe crear una regla para permitir el tráfico de Internet. Si no lo hace, la dirección VIP:<Port> no estará accesible desde fuera.

Por ejemplo: cree una nueva máquina virtual y un nuevo grupo de seguridad de red. Asocie el grupo de seguridad de red a la máquina virtual. La máquina virtual puede comunicarse con otras máquinas virtuales de la red virtual a través de la regla ALLOW VNET INBOUND (PERMITIR ENTRANTE DE RED VIRTUAL). La máquina virtual puede realizar conexiones salientes a Internet mediante la regla ALLOW INTERNET OUTBOUND (PERMITIR SALIENTE DE INTERNET). Posteriormente, cree un extremo en el puerto 80 para recibir tráfico en su sitio web que se ejecuta en la máquina virtual. Los paquetes destinados al puerto 80 en la dirección IP virtual pública (VIP) desde Internet no llegarán a la máquina virtual hasta que agregue una regla similar a la siguiente al grupo de seguridad de red.

| Nombre | Prioridad | IP de origen | Puerto de origen | IP de destino | Puerto de destino | Protocolo | Access |
|------|----------|-----------|-------------|----------------|------------------|----------|--------|
| WEB | 100 | INTERNET | * | * | 80 | TCP | PERMITIR |

## Consideraciones de diseño

Es necesario que entienda cómo se comunican las máquinas virtuales con los servicios de infraestructura y el servicio PaaS hospedado por Azure cuando designe sus NSG. La mayoría de los servicios PaaS de Azure, como las bases de datos SQL y el almacenamiento, solo pueden tener acceso a través de una dirección de Internet pública. Esto mismo puede aplicarse a los sondeos de equilibrio de carga.

Un escenario común en Azure es la separación de roles de máquinas virtuales y PaaS en subredes, en función de si estos objetos requieren acceso a Internet o no. En tal caso, es posible que tenga una subred con máquinas virtuales o instancias de rol que requieran acceso a servicios PaaS de Azure, como las bases de datos SQL y el almacenamiento, pero que no necesiten ninguna comunicación entrante o saliente a Internet.

Imagine la siguiente regla NSG para este escenario:

| Nombre | Prioridad | IP de origen | Puerto de origen | IP de destino | Puerto de destino | Protocolo | Access |
|------|----------|-----------|-------------|----------------|------------------|----------|--------|
|SIN INTERNET|100| VIRTUAL\_NETWORK|&#42;|INTERNET|&#42;|TCP|DENEGAR| 

Puesto que la regla deniega todo acceso desde la red virtual a Internet, las máquinas virtuales no podrán tener acceso a cualquier servicio PaaS de Azure que requiera un extremo de Internet público, como por ejemplo, las bases de datos SQL.

En lugar de usar una regla de denegación, pruebe a usar una regla para permitir el acceso desde la red virtual a Internet, pero que deniegue el acceso desde Internet a la red virtual, tal y como se muestra a continuación:

| Nombre | Prioridad | IP de origen | Puerto de origen | IP de destino | Puerto de destino | Protocolo | Access |
|------|----------|-----------|-------------|----------------|------------------|----------|--------|
|A INTERNET|100| VIRTUAL\_NETWORK|&#42;|INTERNET|&#42;|TCP|PERMITIR|
|DESDE INTERNET|110| INTERNET|&#42;|VIRTUAL\_NETWORK|&#42;|TCP|DENEGAR| 

>[AZURE.WARNING]Azure usa una subred especial que se conoce como la subred de la **puerta de enlace** para manejar la puerta de enlace de VPN con otras redes locales y redes virtuales. Asociar un grupo de seguridad de red a esa subred hará que la puerta de enlace de VPN deje de funcionar como se esperaba. No asocie grupos de seguridad de red a subredes de puerta de enlace.

## Planificación: flujo de trabajo de grupos de seguridad de red

Estos son los pasos del flujo de trabajo básicos al utilizar grupos de seguridad de red.

### Flujo de trabajo: creación y asociación de un grupo de seguridad de red

1. Cree un grupo de seguridad de red.

1. Agregue reglas de seguridad de red, a menos que las reglas predeterminadas sean suficientes.

1. Asocie el grupo de seguridad de red a una máquina virtual.

1. Actualice la máquina virtual.

1. Después de la actualización, las reglas de los grupos de seguridad de red surtirán efecto inmediatamente.

### Flujo de trabajo: actualización de un grupo de seguridad de red existente

1. Agregue, elimine o actualice una regla en un grupo de seguridad de red existente.

1. Todas las máquinas virtuales asociadas al grupo de seguridad de red obtendrán las actualizaciones en unos minutos. No es necesaria una actualización de la máquina virtual cuando la regla del grupo de seguridad de red ya está asociada a la máquina virtual.

### Flujo de trabajo: cambio de una asociación grupo de seguridad de red

1. Asocie un nuevo grupo de seguridad de red a una máquina virtual que ya está asociada a otro grupo de seguridad de red.

1. Actualice la máquina virtual.

1. Las reglas del nuevo grupo de seguridad de red surtirán efecto en unos minutos.

## Crear, configuración y administración de los grupos de seguridad de red

De momento, solo puede configurar y modificar los grupos de seguridad de red mediante los cmdlets de PowerShell y las API de REST. No se pueden configurar los grupos de seguridad de red mediante el Portal de administración. Los siguientes cmdlets de PowerShell ayudan a crear, configurar y administrar sus grupos de seguridad de red.

**Creación de un grupo de seguridad de red**

	New-AzureNetworkSecurityGroup -Name "MyVNetSG" -Location uswest `
		-Label "Security group for my Vnet in West US"

**Incorporación o actualización de las reglas**

	Get-AzureNetworkSecurityGroup -Name "MyVNetSG" `
	| Set-AzureNetworkSecurityRule -Name WEB -Type Inbound -Priority 100 `
		-Action Allow -SourceAddressPrefix 'INTERNET'  -SourcePortRange '*' `
		-DestinationAddressPrefix '*' -DestinationPortRange '*' -Protocol TCP


**Eliminación de una regla de un grupo de seguridad de red**

	Get-AzureNetworkSecurityGroup -Name "MyVNetSG" `
	| Remove-AzureNetworkSecurityRule -Name WEB

**Asociación del grupo de seguridad de red a una máquina virtual**

	Get-AzureVM -ServiceName "MyWebsite" -Name "Instance1" `
	| Set-AzureNetworkSecurityGroupConfig -NetworkSecurityGroupName "MyVNetSG" `
	| Update-AzureVM

**Ver las reglas NSG asociadas a una máquina virtual**

	Get-AzureVM -ServiceName "MyWebsite" -Name "Instance1" `
	| Get-AzureNetworkSecurityGroupAssociation

**Eliminación de un grupo de seguridad de red de una máquina virtual**

	Get-AzureVM -ServiceName "MyWebsite" -Name "Instance1" `
	| Remove-AzureNetworkSecurityGroupConfig -NetworkSecurityGroupName "MyVNetSG" `
	| Update-AzureVM

**Asociación de un grupo de seguridad de red a una máquina virtual**

	Get-AzureNetworkSecurityGroup -Name "MyVNetSG" `
	| Set-AzureNetworkSecurityGroupToSubnet -VirtualNetworkName 'VNetUSWest' `
		-SubnetName 'FrontEndSubnet'

**Ver las reglas NSG asociadas a una subred**

	Get-AzureNetworkSecurityGroupForSubnet -SubnetName 'FrontEndSubnet' `
		-VirtualNetworkName 'VNetUSWest' 

**Eliminación de un grupo de seguridad de red de una subred**

	Get-AzureNetworkSecurityGroup -Name "MyVNetSG" `
	| Remove-AzureNetworkSecurityGroupFromSubnet -VirtualNetworkName 'VNetUSWest' `
		-SubnetName 'FrontEndSubnet'

**Eliminación de un grupo de seguridad de red**

	Remove-AzureNetworkSecurityGroup -Name "MyVNetSG"

**Obtención de los detalles de un grupo de seguridad de red junto con las reglas**

	Get-AzureNetworkSecurityGroup -Name "MyVNetSG" -Detailed
 
**Ver todos cmdlets de Azure PowerShell relacionados con las reglas NSG**

	Get-Command *azurenetworksecuritygroup*

<!---HONumber=August15_HO9-->