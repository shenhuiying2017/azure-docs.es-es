---
title: "Resolución de máquinas virtuales e instancias de rol"
description: "Escenarios de resolución de nombres para IaaS de Azure, soluciones híbridas, entre servicios en la nube diferentes, Active Directory y con su propio servidor DNS  "
services: virtual-network
documentationcenter: na
author: GarethBradshawMSFT
manager: carmonm
editor: tysonn
ms.assetid: 5d73edde-979a-470a-b28c-e103fcf07e3e
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/06/2016
ms.author: telmos
ms.openlocfilehash: 479cf8cf358d0b242d8ce030d8639b493e4767d8
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/11/2017
---
# <a name="name-resolution-for-vms-and-role-instances"></a>Resolución de nombres para las máquinas virtuales e instancias de rol
Dependiendo de cómo utilice Azure para hospedar la IaaS, la PaaS y las soluciones híbridas, puede que necesite permitir que las máquinas virtuales y las instancias de rol creadas se comuniquen entre sí. Aunque este tipo de comunicación puede realizarse mediante las direcciones IP, es mucho más sencillo usar nombres ya que podrá recordarlos con más facilidad y no cambiarlos. 

Cuando las instancias de rol y las máquinas virtuales hospedadas en Azure necesitan resolver nombres de dominio en direcciones IP internas, pueden usar uno de dos métodos:

* [Resolución de nombres de Azure](#azure-provided-name-resolution)
* [Resolución de nombres mediante su propio servidor DNS](#name-resolution-using-your-own-dns-server) (que puede reenviar consultas a los servidores DNS proporcionados por Azure) 

El tipo de resolución de nombres que tenga que usar dependerá de cómo se comuniquen las máquinas virtuales y las instancias de rol entre sí.

**La siguiente tabla muestra los escenarios y las soluciones de resolución de nombre correspondientes:**

| **Escenario** | **Solución** | **Sufijo** |
| --- | --- | --- |
| Resolución de nombres entre instancias de rol o máquinas virtuales ubicadas en el mismo servicio en la nube o red virtual. |[Resolución de nombres de Azure](#azure-provided-name-resolution) |Nombre de host o FQDN |
| Resolución de nombres entre instancias de rol o máquinas virtuales ubicadas en diferentes redes virtuales |Servidores DNS administrados por el cliente que reenvían consultas entre redes virtuales para la resolución mediante Azure (proxy DNS).  Consulte [Resolución de nombres mediante su propio servidor DNS](#name-resolution-using-your-own-dns-server) |Solo FQDN |
| Resolución de nombres de servicios y de equipos locales desde instancias de rol o máquinas virtuales en Azure |Servidores DNS administrados por el cliente (por ejemplo, controlador de dominio local, controlador de dominio de solo lectura local o un DNS secundario sincronizado usando transferencias de zona).  See [Resolución de nombres mediante su propio servidor DNS](#name-resolution-using-your-own-dns-server) |Solo FQDN |
| Resolución de nombres de host de Azure desde equipos locales |Reenvío de consultas a un servidor proxy DNS administrado por el cliente en la red virtual correspondiente: el servidor proxy reenvía consultas a Azure para su resolución. See [Resolución de nombres mediante su propio servidor DNS](#name-resolution-using-your-own-dns-server) |Solo FQDN |
| DNS inverso para direcciones IP internas |[Resolución de nombres mediante su propio servidor DNS](#name-resolution-using-your-own-dns-server) |N/D |
| Resolución de nombres entre máquinas virtuales o instancias de rol ubicadas en diferentes servicios en la nube y no en una red virtual |No aplicable. La conectividad entre máquinas virtuales e instancias de rol de servicios en la nube diferentes no es compatible fuera de una red virtual. |N/D |

## <a name="azure-provided-name-resolution"></a>Resolución de nombres de Azure
Junto con la resolución de nombres DNS públicos, Azure proporciona una resolución de nombres interna para máquinas virtuales e instancias de rol que residen dentro de la misma red virtual o servicio en la nube.  Las máquinas virtuales y las instancias de un servicio en la nube comparten el mismo sufijo DNS (por lo que es suficiente con el nombre de host solo) pero, en las redes virtuales clásicas, los distintos servicios en la nube tienen sufijos DNS diferentes, por lo que se necesita el FQDN para resolver nombres entre diferentes servicios en la nube.  En las redes virtuales basadas en el modelo de implementación de Resource Manager, el sufijo DNS es coherente en toda la red virtual (por lo que no es necesario el FQDN), y los nombres DNS pueden asignarse tanto a las NIC como a las máquinas virtuales. Aunque la resolución de nombres que proporciona Azure no necesita ningún tipo de configuración, no es la opción más adecuada para todos los escenarios de implementación, tal y como se mostró en la tabla anterior.

> [!NOTE]
> En el caso de los roles web y de trabajo, puede acceder a las direcciones IP internas de las instancias de rol que estén basadas en el nombre de rol y el número de instancia, mediante la API de REST de la Administración de servicios de Azure. Para obtener más información, consulte la [Referencia de la API de REST de la administración de servicios](https://msdn.microsoft.com/library/azure/ee460799.aspx).
> 
> 

### <a name="features-and-considerations"></a>Características y consideraciones
**Características**

* Facilidad de uso: no es necesario realizar configuración alguna para usar la resolución de nombres proporcionada por Azure.
* El servicio de resolución de nombres de Azure es altamente disponible y evita la creación y administración de clústeres de sus propios servidores DNS.
* Se puede utilizar junto con sus propios servidores DNS para resolver nombres de host de Azure y locales.
* Las instancias de rol o las máquinas virtuales que están dentro del mismo servicio en la nube son las que proporcionan la resolución de nombres sin necesidad de un nombre de dominio completo.
* Se proporciona resolución de nombres entre las máquinas virtuales en redes virtuales que usan el modelo de implementación de Resource Manager, sin necesidad de FQDN. En el modelo de implementación clásico, las redes virtuales requieren el FQDN para resolver nombres en diferentes servicios en la nube. 
* Puede usar los nombres de host que describan mejor las implementaciones, en lugar de trabajar con nombres generados automáticamente.

**Consideraciones:**

* No se puede modificar el sufijo DNS creado por Azure.
* No se pueden registrar manualmente los registros propios.
* No se admiten ni WINS ni NetBIOS. (Las máquinas virtuales no se pueden ver en el Explorador de Windows).
* Los nombres de host deben ser compatibles con el DNS (solamente pueden usar los caracteres 0-9, a-z y “-” y no pueden comenzar ni terminar con un “-”. Consulte la sección 2 de RFC 3696).
* El tráfico de consultas de DNS está limitado por cada máquina virtual. Esto no debería afectar a la mayoría de las aplicaciones.  Si se observa una limitación de solicitudes, asegúrese de que está habilitado el almacenamiento en caché del lado cliente.  Para obtener más información, consulte [Obtención del máximo partido de la resolución de nombres de Azure](#Getting-the-most-from-Azure-provided-name-resolution).
* En un modelo de implementación clásico, solo se registran las máquinas virtuales de los 180 primeros servicios en la nube para cada red virtual clásica. Esto no se aplica a las redes virtuales en los modelos de implementación de Resource Manager.

### <a name="getting-the-most-from-azure-provided-name-resolution"></a>Obtención del máximo partido de la resolución de nombres de Azure
**Almacenamiento en caché del lado cliente:**

No todas las consultas DNS deben enviarse a través de la red.  El almacenamiento en caché del lado cliente ayuda a reducir la latencia y mejorar la resistencia a la señalización visual de la red mediante la resolución de las consultas DNS periódicas desde una caché local.  Los registros DNS contienen un período de vida (TTL) que permite a la memoria caché almacenar el registro tanto como sea posible sin afectar a la actualización de registros, por lo que el almacenamiento en caché de cliente es adecuado para la mayoría de las situaciones.

El cliente DNS de Windows predeterminado tiene una memoria caché DNS integrada.  Algunas distribuciones Linux no incluyen el almacenamiento en caché de forma predeterminada; se recomienda que se agregue una a cada máquina virtual Linux (después de comprobar que aún no hay una memoria caché local).

Hay una serie de distintos paquetes de almacenamiento en caché DNS disponibles, p. ej., dnsmasq; estos son los pasos para instalar dnsmasq en las distribuciones más comunes:

* **Ubuntu (usa resolvconf)**:
  * instalar únicamente el paquete dnsmasq ("sudo apt-get install dnsmasq")
* **SUSE (usa netconf)**:
  * instalar el paquete dnsmasq ("sudo apt-get install dnsmasq") 
  * habilitar el servicio de dnsmasq ("systemctl enable dnsmasq.service") 
  * iniciar el servicio de dnsmasq ("systemctl start dnsmasq.service") 
  * editar “/etc/sysconfig/network/config” y cambie NETCONFIG_DNS_FORWARDER="" por ”dnsmasq”
  * actualizar resolv.conf ("netconfig update") para establecer la memoria caché como el solucionador DNS local
* **OpenLogic (usa NetworkManager)**:
  * instalar el paquete dnsmasq ("sudo yum install dnsmasq")
  * habilitar el servicio de dnsmasq ("systemctl enable dnsmasq.service")
  * iniciar el servicio de dnsmasq ("systemctl start dnsmasq.service")
  * agregar “prepend domain-name-servers 127.0.0.1;” en “/etc/dhclient-eth0.conf”
  * reiniciar el servicio de red ("service network restart") para establecer la memoria caché como el solucionador DNS local

> [!NOTE]
> El paquete 'dnsmasq' es solo una de las muchas cachés DNS disponibles para Linux.  Antes de usarlo, compruebe su idoneidad para sus necesidades concretas y que no se instale ninguna otra memoria caché.
> 
> 

**Reintentos de cliente:**

DNS es principalmente un protocolo UDP.  Como el protocolo UDP no garantiza la entrega de mensajes, la lógica de reintento se controla en el mismo protocolo DNS.  Cada cliente DNS (sistema operativo) puede presentar una lógica de reintento diferente, dependiendo de la preferencia de los creadores:

* Los sistemas operativos Windows realizan un intento tras un segundo y después tras otros 2, 4 y otros 4 segundos. 
* El programa de instalación predeterminado de Linux lo intenta después de 5 segundos.  Se recomienda cambiar esta opción para reintentarlo 5 veces a intervalos de 1 segundo.  

Para comprobar la configuración actual en una máquina virtual Linux, 'cat /etc/resolv.conf' y busque la línea 'options', p. ej.:

    options timeout:1 attempts:5

El archivo resolv.conf suele ser autogenerado y no se debe editar.  Los pasos específicos para agregar la línea 'options' varían según la distribución:

* **Ubuntu** (usa resolvconf):
  * agregar la línea de opciones a '/ etc/resolveconf/resolv.conf.d/head' 
  * ejecutar 'resolvconf -u' para actualizar
* **SUSE** (usa netconf):
  * agregar 'timeout:1 attempts:5' al parámetro NETCONFIG_DNS_RESOLVER_OPTIONS="" en '/etc/sysconfig/network/config' 
  * ejecutar 'netconfig update' para actualizar
* **OpenLogic** (usa NetworkManager):
  * agregar 'echo "options timeout:1 attempts:5"' a '/etc/NetworkManager/dispatcher.d/11-dhclient' 
  * ejecutar 'service network restart' para actualizar

## <a name="name-resolution-using-your-own-dns-server"></a>Resolución de nombres mediante su propio servidor DNS
Hay una serie de situaciones donde sus necesidades de resolución de nombres pueden ir más allá de las características proporcionadas por Azure, por ejemplo, cuando se usan dominios de Active Directory o cuando se requiere la resolución de DNS entre redes virtuales.  Para abarcar estos escenarios, Azure le ofrece la posibilidad de que use sus propios servidores DNS.  

Los servidores DNS de una red virtual pueden reenviar consultas DNS a resoluciones recursivas de Azure para resolver los nombres de host en la red virtual.  Por ejemplo, un controlador de dominio (DC) que se ejecute en Azure puede responder a las consultas DNS referidas a sus dominios y reenviar todas las demás consultas a Azure.  Esto permite que las máquinas virtuales vean sus recursos locales (mediante el controlador de dominio) y los nombres de host proporcionados por Azure (mediante el reenviador).  El acceso a las resoluciones recursivas de Azure se proporciona a través de la IP virtual 168.63.129.16.

El reenvío de DNS también habilita la resolución de DNS entre redes virtuales y permite que los equipos locales resuelvan nombres de host proporcionados por Azure.  Para resolver el nombre de host de una máquina virtual, la máquina virtual del servidor DNS debe residir en la misma red virtual y debe configurarse para reenviar consultas de nombre de host a Azure.  Como el sufijo DNS es diferente en cada red virtual, puede usar las reglas de reenvío condicional para enviar consultas DNS a la red virtual correcta para su resolución.  En la imagen siguiente se muestran dos redes virtuales y una red local que está realizando la resolución DNS entre redes virtuales con este método.  Puede encontrar un reenviador DNS de ejemplo disponible en la [galería de plantillas de inicio rápido de Azure](https://azure.microsoft.com/documentation/templates/301-dns-forwarder/) y en [GitHub](https://github.com/Azure/azure-quickstart-templates/tree/master/301-dns-forwarder).

![DNS entre redes virtuales](./media/virtual-networks-name-resolution-for-vms-and-role-instances/inter-vnet-dns.png)

Cuando se utiliza la resolución de nombres proporcionada por Azure, se proporciona un sufijo DNS interno (*.internal.cloudapp.net) a cada máquina virtual mediante DHCP.  Esto permite la resolución de nombre de host, dado que los registros de nombre de host están en la zona internal.cloudapp.net.  Cuando se utiliza su propia solución de resolución de nombres, no se proporciona este sufijo a las máquinas virtuales puesto que interfiere con otras arquitecturas DNS (como es el caso de los escenarios de unión a un dominio).  En su lugar, proporcionamos un marcador de posición no funcional (reddog.microsoft.com).  

Si es necesario, el sufijo DNS interno se puede determinar con PowerShell o la API:

* En el caso de redes virtuales en modelos de implementación de Resource Manager, el sufijo está disponible con el recurso de [tarjeta de interfaz de red](https://msdn.microsoft.com/library/azure/mt163668.aspx) o del cmdlet [Get-AzureRmNetworkInterface](https://msdn.microsoft.com/library/mt619434.aspx).    
* En los modelos de implementación clásico, el sufijo está disponible mediante la llamada a la [API de Get Deployment](https://msdn.microsoft.com/library/azure/ee460804.aspx) o por medio del cmdlet [Get-AzureVM -Debug](https://msdn.microsoft.com/library/azure/dn495236.aspx).

Si el reenvío de consultas a Azure no satisface sus necesidades, debe proporcionar su propia solución de DNS.  La solución de DNS deberá cumplir estos requisitos:

* Proporcionar la resolución adecuada de nombres de host, por ejemplo, mediante [DDNS](virtual-networks-name-resolution-ddns.md).  Tenga en cuenta que si usa DDNS debe deshabilitar la limpieza de registros DNS, ya que las concesiones DHCP de Azure son muy largas y, al efectuar la limpieza, se pueden eliminar los registros DNS prematuramente. 
* Proporcionar la resolución recursiva adecuada para permitir la resolución de nombres de dominio externos.
* Estar accesible (TCP y UDP en el puerto 53) desde los clientes a los que sirve y poder acceder a Internet.
* Tener protección contra el acceso desde Internet para mitigar las amenazas que suponen los agentes externos.

> [!NOTE]
> Para obtener un mejor rendimiento, cuando se usan máquinas virtuales de Azure como servidores DNS, se debe deshabilitar IPv6 y debe asignarse una [IP pública de nivel de instancia](virtual-networks-instance-level-public-ip.md) a cada máquina virtual del servidor DNS.  Si opta por usar Windows Server como servidor DNS, en [este artículo](http://blogs.technet.com/b/networking/archive/2015/08/19/name-resolution-performance-of-a-recursive-windows-dns-server-2012-r2.aspx) se proporcionan optimizaciones y análisis de rendimiento adicionales.
> 
> 

### <a name="specifying-dns-servers"></a>Especificar los servidores DNS
Si usa sus propios servidores DNS, Azure permite especificar varios servidores DNS por red virtual o por interfaz de red (Resource Manager), o por servicio en la nube (clásico).  Los servidores DNS especificados para una interfaz de red o un servicio en la nube tienen prioridad sobre las especificadas para la red virtual.

> [!NOTE]
> Las propiedades de conexión de red, como las direcciones IP de los servidores DNS, no se deben editar directamente en las máquinas virtuales de Windows, porque podrían borrarse al reemplazar el adaptador de red virtual durante el trabajo de mantenimiento. 
> 
> 

Cuando se usa el modelo de implementación de Resource Manager, se pueden especificar servidores DNS en el Portal, en la API y en las plantillas ([vnet](https://msdn.microsoft.com/library/azure/mt163661.aspx), [nic](https://msdn.microsoft.com/library/azure/mt163668.aspx)), o en PowerShell ([vnet](https://msdn.microsoft.com/library/mt603657.aspx), [nic](https://msdn.microsoft.com/library/mt619370.aspx)).

Cuando se usa el modelo de implementación clásico, los servidores DNS de la red virtual pueden especificarse en el Portal o en el [archivo de *configuración de red*](https://msdn.microsoft.com/library/azure/jj157100).  En el caso de los servicios en la nube, los servidores DNS se especifican mediante [el archivo de *configuración de servicio*](https://msdn.microsoft.com/library/azure/ee758710) o en PowerShell ([New-AzureVM](https://msdn.microsoft.com/library/azure/dn495254.aspx)).

> [!NOTE]
> Si cambia la configuración de DNS para una red virtual o una máquina virtual que ya están implementadas, deberá reiniciar cada máquina virtual afectada para que los cambios surtan efecto.
> 
> 

## <a name="next-steps"></a>Pasos siguientes
Modelo de implementación de Resource Manager:

* [Creación o actualización de una red virtual](https://msdn.microsoft.com/library/azure/mt163661.aspx)
* [Creación o actualización de una tarjeta de interfaz de red](https://msdn.microsoft.com/library/azure/mt163668.aspx)
* [New-AzureRmVirtualNetwork](https://msdn.microsoft.com/library/mt603657.aspx)
* [New-AzureRmNetworkInterface](https://msdn.microsoft.com/library/mt619370.aspx)

Modelo de implementación clásico:

* [Esquema de configuración del servicio de Azure](https://msdn.microsoft.com/library/azure/ee758710)
* [Esquema de configuración de Red virtual](https://msdn.microsoft.com/library/azure/jj157100)
* [Configuración de una red virtual con un archivo de configuración de red](virtual-networks-using-network-configuration-file.md) 

