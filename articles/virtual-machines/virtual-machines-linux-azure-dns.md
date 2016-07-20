<properties 
   pageTitle="Opciones de resolución de nombres DNS para máquinas virtuales Linux en Azure"
   description="Escenarios de resolución de nombres DNS para máquinas virtuales Linux en IaaS de Azure, incluidos los servicios DNS proporcionados, y los servidores DNS externos híbridos y Bring Your Own DNS."
   services="virtual-machines"
   documentationCenter="na"
   authors="RicksterCDN"
   manager="timlt"
   editor="tysonn" />
<tags 
   ms.service="virtual-machines"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="06/21/2016"
   ms.author="rclaus" />

# Opciones de resolución de nombres DNS para máquinas virtuales Linux en Azure

Azure proporciona una funcionalidad de resolución de nombres DNS de forma predeterminada para todas las máquinas virtuales de una única red virtual. Puede implementar su propia solución de resolución de nombre DNS configurando sus propios servicios DNS en las máquinas virtuales hospedadas de Azure. Los siguientes escenarios le ayudarán a elegir cuál encaja mejor en su situación concreta.

- [Resolución de nombres de Azure](#azure-provided-name-resolution)

- [Resolución de nombres mediante su propio servidor DNS](#name-resolution-using-your-own-dns-server)

El tipo de resolución de nombres que tenga que usar dependerá de cómo se comuniquen las máquinas virtuales y las instancias de rol entre sí.

**La siguiente tabla muestra los escenarios y las soluciones de resolución de nombre correspondientes:**

| **Escenario** | **Solución** | **Sufijo** |
|--------------|--------------|----------|
| Resolución de nombres entre instancias de rol o máquinas virtuales ubicadas en la misma red virtual | [Resolución de nombres de Azure](#azure-provided-name-resolution)| Nombre de host o FQDN |
| Resolución de nombres entre instancias de rol o máquinas virtuales ubicadas en diferentes redes virtuales | Servidores DNS administrados por el cliente que reenvían consultas entre redes virtuales para la resolución mediante Azure (proxy DNS). Consulte [Resolución de nombres mediante su propio servidor DNS](#name-resolution-using-your-own-dns-server).| Solo FQDN |
| Resolución de nombres de servicios y de equipos locales desde instancias de rol o máquinas virtuales en Azure | Servidores DNS administrados por el cliente (por ejemplo, controlador de dominio local, controlador de dominio de solo lectura local o un DNS secundario sincronizado usando transferencias de zona). Consulte [Resolución de nombres mediante su propio servidor DNS](#name-resolution-using-your-own-dns-server).|Solo FQDN |
| Resolución de nombres de host de Azure desde equipos locales | Reenvío de consultas a un servidor proxy DNS administrado por el cliente en la red virtual correspondiente: el servidor proxy reenvía consultas a Azure para su resolución. Consulte [Resolución de nombres mediante su propio servidor DNS](#name-resolution-using-your-own-dns-server).| Solo FQDN |
| DNS inverso para direcciones IP internas | [Resolución de nombres mediante su propio servidor DNS](#name-resolution-using-your-own-dns-server) | N/D |

## Resolución de nombres de Azure

Junto con la resolución de nombres DNS públicos, Azure proporciona una resolución de nombres interna para máquinas virtuales e instancias de rol que residen dentro de la misma red virtual. En las redes virtuales basadas en ARM, el sufijo DNS es coherente en toda la red virtual (por lo que no es necesario el FQDN), y los nombres DNS pueden asignarse a la NIC y a la máquina virtual. Aunque la resolución de nombres que proporciona Azure no necesita ningún tipo de configuración, no es la opción más adecuada para todos los escenarios de implementación, tal y como se mostró en la tabla anterior.

### Características y consideraciones

**Características**

- Facilidad de uso: no es necesario realizar configuración alguna para usar la resolución de nombres proporcionada por Azure.

- El servicio de resolución de nombres de Azure es altamente disponible y evita la creación y administración de clústeres de sus propios servidores DNS.

- Se puede utilizar junto con sus propios servidores DNS para resolver nombres de host de Azure y locales.

- Se proporciona resolución de nombres entre las máquinas virtuales en redes virtuales sin necesidad de usar FQDN.

- Puede usar los nombres de host que describan mejor las implementaciones, en lugar de trabajar con nombres generados automáticamente.

**Consideraciones:**

- No se puede modificar el sufijo DNS creado por Azure.

- No se pueden registrar manualmente los registros propios.

- No se admiten ni WINS ni NetBIOS.

- Los nombres de host deben ser compatibles con el DNS (solamente pueden usar los caracteres 0-9, a-z y “-” y no pueden comenzar ni terminar con un “-”. Consulte la sección 2 de RFC 3696).

- El tráfico de consultas de DNS está limitado por cada máquina virtual. Esto no debería afectar a la mayoría de las aplicaciones. Si se observa una limitación de solicitudes, asegúrese de que está habilitado el almacenamiento en caché del lado cliente. Para obtener más información, consulte [Obtención del máximo partido de la resolución de nombres de Azure](#Getting-the-most-from-Azure-provided-name-resolution).


### Obtención del máximo partido de la resolución de nombres de Azure
**Almacenamiento en caché del lado cliente:**

No todas las consultas DNS deben enviarse a través de la red. El almacenamiento en caché del lado cliente ayuda a reducir la latencia y mejorar la resistencia a la señalización visual de la red mediante la resolución de las consultas DNS periódicas desde una caché local. Los registros DNS contienen un período de vida (TTL) que permite a la memoria caché almacenar el registro tanto como sea posible sin afectar a la actualización de registros, por lo que el almacenamiento en caché de cliente es adecuado para la mayoría de las situaciones.

Algunas distribuciones Linux no incluyen el almacenamiento en caché de forma predeterminada; se recomienda que se agregue una a cada máquina virtual Linux (después de comprobar que aún no hay una memoria caché local).

Hay una serie de distintos paquetes de almacenamiento en caché DNS disponibles, p. ej., dnsmasq; estos son los pasos para instalar dnsmasq en las distribuciones más comunes:

- **Ubuntu (usa resolvconf)**:
	- instalar únicamente el paquete dnsmasq ("sudo apt-get install dnsmasq")
- **SUSE (usa netconf)**:
	- instalar el paquete dnsmasq ("sudo apt-get install dnsmasq")
	- habilitar el servicio de dnsmasq ("systemctl enable dnsmasq.service")
	- iniciar el servicio de dnsmasq ("systemctl start dnsmasq.service")
	- editar “/etc/sysconfig/network/config” y cambie NETCONFIG\_DNS\_FORWARDER="" por ”dnsmasq”
	- actualizar resolv.conf ("netconfig update") para establecer la memoria caché como el solucionador DNS local
- **OpenLogic (usa NetworkManager)**:
	- instalar el paquete dnsmasq ("sudo yum install dnsmasq")
	- habilitar el servicio de dnsmasq ("systemctl enable dnsmasq.service")
	- iniciar el servicio de dnsmasq ("systemctl start dnsmasq.service")
	- agregar “prepend domain-name-servers 127.0.0.1;” en “/etc/dhclient-eth0.conf”
	- reiniciar el servicio de red ("service network restart") para establecer la memoria caché como el solucionador DNS local

> [AZURE.NOTE]\: El paquete 'dnsmasq' es solo una de las muchas cachés DNS disponibles para Linux. Antes de usarlo, compruebe su idoneidad para sus necesidades concretas y que no se instale ninguna otra memoria caché.

**Reintentos de cliente:**

DNS es principalmente un protocolo UDP. Como el protocolo UDP no garantiza la entrega de mensajes, la lógica de reintento se controla en el mismo protocolo DNS. Cada cliente DNS (sistema operativo) puede presentar una lógica de reintento diferente, dependiendo de la preferencia de los creadores:

 - Los sistemas operativos Windows realizan un intento tras un segundo y después tras otros 2, 4 y otros 4 segundos.
 - El programa de instalación predeterminado de Linux lo intenta después de 5 segundos. Se recomienda cambiar esta opción para reintentarlo 5 veces a intervalos de 1 segundo.

Para comprobar la configuración actual en una máquina virtual Linux, 'cat /etc/resolv.conf' y busque la línea 'options', p. ej.:

	options timeout:1 attempts:5

El archivo resolv.conf suele ser autogenerado y no se debe editar. Los pasos específicos para agregar la línea 'options' varían según la distribución:

- **Ubuntu** (usa resolvconf):
	- agregar la línea de opciones a '/ etc/resolveconf/resolv.conf.d/head'
	- ejecutar 'resolvconf -u' para actualizar
- **SUSE** (usa netconf):
	- agregar 'timeout:1 attempts:5' al parámetro NETCONFIG\_DNS\_RESOLVER\_OPTIONS="" en '/etc/sysconfig/network/config'
	- ejecutar 'netconfig update' para actualizar
- **OpenLogic** (usa NetworkManager):
	- agregar 'echo "options timeout:1 attempts:5"' a '/etc/NetworkManager/dispatcher.d/11-dhclient'
	- ejecutar 'service network restart' para actualizar

## Resolución de nombres mediante su propio servidor DNS
Hay una serie de situaciones donde sus necesidades de resolución de nombres pueden ir más allá de las características proporcionadas por Azure, por ejemplo, cuando se requiere la resolución de DNS entre redes virtuales. Para abarcar este escenario, Azure le ofrece la posibilidad de que use sus propios servidores DNS.

Los servidores DNS de una red virtual pueden reenviar consultas DNS a resoluciones recursivas de Azure para resolver los nombres de host en la red virtual. Por ejemplo, un servidor DNS que se ejecute en Azure puede responder a sus propios archivos de zonas DNS referidas y reenviar todas las demás consultas a Azure. Esto permite que las máquinas virtuales vean sus propios archivos de zonas y los nombres de host proporcionados por Azure (mediante el reenviador). El acceso a las resoluciones recursivas de Azure se proporciona a través de la IP virtual 168.63.129.16.

El reenvío de DNS también habilita la resolución de DNS entre redes virtuales y permite a los equipos locales resolver nombres de host proporcionados por Azure. Para resolver el nombre de host de una máquina virtual, la máquina virtual del servidor DNS debe residir en la misma red virtual y debe configurarse para reenviar consultas de nombre de host a Azure. Como el sufijo DNS es diferente en cada red virtual, puede usar las reglas de reenvío condicional para enviar consultas DNS a la red virtual correcta para su resolución. La imagen siguiente muestra dos redes virtuales y una red local realizando la resolución DNS entre redes virtuales con este método:

![DNS entre redes virtuales](./media/virtual-machines-linux-azure-dns/inter-vnet-dns.png)

Cuando se utiliza la resolución de nombres proporcionada por Azure, el sufijo DNS interno se proporciona a cada máquina virtual mediante DHCP. Cuando se utiliza su propia solución de resolución de nombres, no se proporciona este sufijo a las máquinas virtuales puesto que interfiere con otras arquitecturas DNS. Para hacer referencia a equipos mediante el FQDN (o para configurar el sufijo en las máquinas virtuales), el sufijo puede determinarse mediante PowerShell o la API:

-  Para las redes virtuales administradas de la administración de recursos de Azure, el sufijo está disponible a través del recurso de [tarjeta de interfaz de red](https://msdn.microsoft.com/library/azure/mt163668.aspx); también puede ejecutar el comando `azure network public-ip show <resource group> <pip name>` para mostrar los detalles de la dirección IP pública, incluido el FQDN de la NIC.


Si el reenvío de consultas a Azure no satisface sus necesidades, debe proporcionar su propia solución de DNS. La solución de DNS deberá cumplir estos requisitos:

-  Proporcione la resolución apropiada para el nombre de host, por ejemplo, a través de [DDNS](../virtual-network/virtual-networks-name-resolution-ddns.md). Tenga en cuenta que si usa DDNS debe deshabilitar la limpieza de registros DNS, ya que las concesiones DHCP de Azure son muy largas y, al efectuar la limpieza, se pueden eliminar los registros DNS prematuramente.
-  Proporcionar la resolución recursiva adecuada para permitir la resolución de nombres de dominio externos.
-  Estar accesible (TCP y UDP en el puerto 53) desde los clientes a los que sirve y poder acceder a Internet.
-  Tener protección contra el acceso desde Internet para mitigar las amenazas que suponen los agentes externos.

> [AZURE.NOTE] Para obtener un mejor rendimiento, cuando se usan máquinas virtuales de Azure como servidores DNS, se debe deshabilitar IPv6 y debe asignarse una [IP pública de nivel de instancia](../virtual-network/virtual-networks-instance-level-public-ip.md) a cada máquina virtual del servidor DNS.

<!---HONumber=AcomDC_0706_2016-->