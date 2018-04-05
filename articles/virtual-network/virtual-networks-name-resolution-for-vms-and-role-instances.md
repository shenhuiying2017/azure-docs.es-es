---
title: Resolución de nombres para máquinas virtuales e instancias de rol
description: Escenarios de resolución de nombres para IaaS de Azure, soluciones híbridas, entre servicios en la nube diferentes, Active Directory y con su propio servidor DNS.
services: virtual-network
documentationcenter: na
author: jimdial
manager: jeconnoc
editor: tysonn
ms.assetid: 5d73edde-979a-470a-b28c-e103fcf07e3e
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/14/2018
ms.author: jdial
ms.openlocfilehash: e46f6617b1a6d73ace00d4eafa1410785315a8c8
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 03/28/2018
---
# <a name="name-resolution-for-virtual-machines-and-role-instances"></a>Resolución de nombres para máquinas virtuales e instancias de rol

Según cómo utilice Azure para hospedar soluciones híbridas, IaaS y PaaS, puede que tenga que permitir que las máquinas virtuales y las instancias de rol creadas se comuniquen entre sí. Aunque este tipo de comunicación puede realizarse mediante las direcciones IP, es mucho más sencillo usar nombres, ya que podrá recordarlos con más facilidad y no cambiarlos. 

Cuando las instancias de rol y las máquinas virtuales hospedadas en Azure necesitan resolver nombres de dominio en direcciones IP internas, pueden usar uno de dos métodos:

* [Resolución de nombres de Azure](#azure-provided-name-resolution)
* [Resolución de nombres con su propio servidor DNS](#name-resolution-that-uses-your-own-dns-server) (que puede reenviar consultas a los servidores DNS proporcionados por Azure) 

El tipo de resolución de nombres que tenga que usar dependerá de cómo se comuniquen las máquinas virtuales y las instancias de rol entre sí. En la siguiente tabla se muestran los escenarios y las soluciones de resolución de nombre correspondientes:

> [!NOTE]
> Según el escenario, puede ser conveniente usar la característica Azure DNS Private Zones, que se encuentra actualmente en versión preliminar pública. Para obtener más detalles, consulte [Uso de Azure DNS para dominios privados](../dns/private-dns-overview.md).
>

| **Escenario** | **Solución** | **Sufijo** |
| --- | --- | --- |
| Resolución de nombres entre instancias de rol o máquinas virtuales ubicadas en el mismo servicio en la nube o red virtual. | [Azure DNS Private Zones](../dns/private-dns-overview.md) o [resolución de nombres de Azure](#azure-provided-name-resolution) |Nombre de host o FQDN |
| Resolución de nombres entre instancias de rol o máquinas virtuales ubicadas en diferentes redes virtuales. |[Azure DNS Private Zones](../dns/private-dns-overview.md) o Servidores DNS administrados por el cliente que reenvían consultas entre redes virtuales para la resolución mediante Azure (proxy DNS). Consulte [Resolución de nombres mediante su propio servidor DNS](#name-resolution-that-uses-your-own-dns-server). |Solo FQDN |
| Resolución de nombres de Azure App Service (aplicación web, función o bot) mediante la integración de red virtual con instancias de rol o máquinas virtuales ubicadas en la misma red virtual. |Servidores DNS administrados por el cliente que reenvían consultas entre redes virtuales para la resolución mediante Azure (proxy DNS). Consulte [Resolución de nombres mediante su propio servidor DNS](#name-resolution-that-uses-your-own-dns-server). |Solo FQDN |
| Resolución de nombres entre instancias de App Service Web Apps en máquinas virtuales ubicadas en la misma red virtual. |Servidores DNS administrados por el cliente que reenvían consultas entre redes virtuales para la resolución mediante Azure (proxy DNS). Consulte [Resolución de nombres mediante su propio servidor DNS](#name-resolution-that-uses-your-own-dns-server). |Solo FQDN |
| Resolución de nombres entre instancias de App Service Web Apps en máquinas virtuales ubicadas en una red virtual diferente. |Servidores DNS administrados por el cliente que reenvían consultas entre redes virtuales para la resolución mediante Azure (proxy DNS). Consulte [Resolución de nombres mediante su propio servidor DNS](#name-resolution-that-uses-your-own-dns-server-for-web-apps). |Solo FQDN |
| Resolución de nombres de servicios y de equipos locales desde instancias de rol o máquinas virtuales en Azure. |Servidores DNS administrados por el cliente (controlador de dominio local, controlador de dominio de solo lectura local o un DNS secundario sincronizado mediante transferencias de zona, por ejemplo). Consulte [Resolución de nombres mediante su propio servidor DNS](#name-resolution-that-uses-your-own-dns-server). |Solo FQDN |
| Resolución de nombres de host de Azure desde equipos locales. |Reenvío de consultas a un servidor proxy DNS administrado por el cliente en la red virtual correspondiente: el servidor proxy reenvía consultas a Azure para su resolución. Consulte [Resolución de nombres mediante su propio servidor DNS](#name-resolution-that-uses-your-own-dns-server). |Solo FQDN |
| DNS inverso para direcciones IP internas. |[Resolución de nombres mediante su propio servidor DNS](#name-resolution-that-uses-your-own-dns-server). |No aplicable |
| Resolución de nombres entre máquinas virtuales o instancias de rol ubicadas en diferentes servicios en la nube y no en una red virtual. |No aplicable. La conectividad entre máquinas virtuales e instancias de rol de servicios en la nube diferentes no es compatible fuera de una red virtual. |No aplicable|

## <a name="azure-provided-name-resolution"></a>Resolución de nombres de Azure

Junto con la resolución de nombres DNS públicos, Azure proporciona una resolución de nombres interna para máquinas virtuales e instancias de rol que residen dentro de la misma red virtual o servicio en la nube. Las máquinas virtuales y las instancias en un servicio en la nube comparten el mismo sufijo DNS (por lo que el nombre de host por sí solo es suficiente). Pero en las redes virtuales que utilizan el modelo de implementación clásica, diferentes servicios en la nube tienen distintos sufijos DNS. En esta situación, es necesario el FQDN para resolver los nombres entre diferentes servicios en la nube. En las redes virtuales basadas en el modelo de implementación de Azure Resource Manager, el sufijo DNS es coherente en toda la red virtual, de modo que el FQDN no es necesario. Los nombres DNS pueden asignarse tanto a las NIC como a las máquinas virtuales. Aunque la resolución de nombres que proporciona Azure no necesita ningún tipo de configuración, no es la opción más adecuada para todos los escenarios de implementación, tal y como se mostró en la tabla anterior.

> [!NOTE]
> Al utilizar roles web y de trabajo, también puede tener acceso a las direcciones IP internas de las instancias de rol. Esto se fundamenta en el número de instancia y en el nombre de rol, mediante el uso de la API REST de Azure Service Management. Para obtener más información, consulte la [Referencia de la API REST de administración de servicios](https://msdn.microsoft.com/library/azure/ee460799.aspx).
> 
> 

### <a name="features"></a>Características

La resolución de nombres proporcionada por Azure incluye las siguientes características:
* Facilidad de uso. No se requiere ninguna configuración.
* Alta disponibilidad. No es necesario crear clústeres de sus propios servidores DNS ni administrarlos.
* Puede utilizar el servicio junto con sus propios servidores DNS para resolver nombres de host de Azure y locales.
* Puede usar la resolución de nombres entre las instancias de rol y las máquinas virtuales que están dentro del mismo servicio en la nube, sin necesidad de un nombre de dominio completo.
* Puede usar la resolución de nombres entre las máquinas virtuales en redes virtuales que usan el modelo de implementación de Azure Resource Manager, sin necesidad de un nombre de dominio completo. En el modelo de implementación clásica, las redes virtuales requieren un FQDN cuando se resuelven nombres en diferentes servicios en la nube. 
* Puede usar los nombres de host que describan mejor las implementaciones, en lugar de trabajar con nombres generados automáticamente.

### <a name="considerations"></a>Consideraciones

Aquí se presentan puntos que deben considerarse cuando se utiliza la resolución de nombres de Azure:
* No se puede modificar el sufijo DNS creado por Azure.
* No se pueden registrar manualmente los registros propios.
* No se admiten WINS ni NetBIOS (no se pueden ver las máquinas virtuales en Windows Explorer).
* Los nombres de host deben ser compatibles con DNS. Los nombres solamente pueden contener los caracteres 0-9, a-z y "-", y no pueden comenzar ni terminar por "-".
* El tráfico de consultas de DNS está limitado por cada máquina virtual. La limitación no debería afectar a la mayoría de las aplicaciones. Si se observa una limitación de solicitudes, asegúrese de que está habilitado el almacenamiento en caché del lado cliente. Para más información, consulte [Configuración de cliente DNS](#dns-client-configuration).
* En un modelo de implementación clásico, solo se registran las máquinas virtuales de los 180 primeros servicios en la nube para cada red virtual clásica. Este límite no se aplica a las redes virtuales en Azure Resource Manager.

## <a name="dns-client-configuration"></a>Configuración de cliente DNS

Esta sección trata los intentos del lado cliente y el almacenamiento en caché del cliente.

### <a name="client-side-caching"></a>Almacenamiento en caché del lado cliente

No todas las consultas DNS deben enviarse a través de la red. El almacenamiento en caché del cliente ayuda a reducir la latencia y a mejorar la resistencia a la señalización visual de la red mediante la resolución de las consultas DNS periódicas desde una memoria caché local. Los registros DNS contienen un mecanismo para contabilizar el período de vida (TTL), lo que permite a la memoria caché almacenar el registro el máximo tiempo posible sin afectar a la actualización de registros. Por ello, el almacenamiento en caché del cliente es adecuado para la mayoría de las situaciones.

El cliente DNS de Windows predeterminado tiene una memoria caché DNS integrada. Algunas distribuciones de Linux no incluyen el almacenamiento en caché de forma predeterminada. Si comprueba que aún no hay una memoria caché local, agregue una memoria caché DNS a cada máquina virtual Linux.

Hay una serie de diferentes paquetes de caché DNS disponibles, como dnsmasq. Aquí se indica cómo instalar dnsmasq en las distribuciones más habituales:

* **Ubuntu (usa resolvconf)**:
  * Instale el paquete dnsmasq con `sudo apt-get install dnsmasq`.
* **SUSE (usa netconf)**:
  * Instale el paquete dnsmasq con `sudo zypper install dnsmasq`.
  * Habilite el servicio dnsmasq con `systemctl enable dnsmasq.service`. 
  * Inicie el servicio dnsmasq con `systemctl start dnsmasq.service`. 
  * Edite **/etc/sysconfig/network/config** y cambie *NETCONFIG_DNS_FORWARDER=""* por *dnsmasq*.
  * Actualice resolv.conf con `netconfig update` para establecer la memoria caché como la resolución DNS local.
* **OpenLogic (usa NetworkManager)**:
  * Instale el paquete dnsmasq con `sudo yum install dnsmasq`.
  * Habilite el servicio dnsmasq con `systemctl enable dnsmasq.service`.
  * Inicie el servicio dnsmasq con `systemctl start dnsmasq.service`.
  * Agregue *prepend domain-name-servers 127.0.0.1;* a **/etc/dhclient-eth0.conf**.
  * Reinicie el servicio de red con `service network restart` para establecer la memoria caché como la resolución DNS local.

> [!NOTE]
> El paquete dnsmasq constituye solo una de las muchas memorias cachés DNS disponibles para Linux. Antes de usarlo, compruebe su idoneidad para sus necesidades concretas y que no esté instalada ninguna otra memoria caché.
> 
> 
    
### <a name="client-side-retries"></a>Reintentos del cliente

DNS es principalmente un protocolo UDP. Como el protocolo UDP no garantiza la entrega de mensajes, la lógica de reintento se controla en el mismo protocolo DNS. Cada cliente DNS (sistema operativo) puede presentar una lógica de reintento diferente en función de la preferencia del creador:

* Los sistemas operativos Windows realizan un reintento tras un segundo y después tras otros dos, cuatro y otros cuatro segundos. 
* El programa de instalación predeterminado de Linux lo intenta después de cinco segundos. Se recomienda cambiar las especificaciones de reintento a cinco veces, en intervalos de un segundo.

Compruebe la configuración actual en una VM de Linux con `cat /etc/resolv.conf`. Mire la línea *options*, por ejemplo:

```bash
options timeout:1 attempts:5
```

El archivo resolv.conf suele generarse de forma automática y no se debe editar. Los pasos específicos para agregar la línea *options* varían según la distribución:

* **Ubuntu** (usa resolvconf):
  1. Agregue la línea *options* a **/etc/resolveconf/resolv.conf.d/head**.
  2. Ejecute `resolvconf -u` para actualizar.
* **SUSE** (usa netconf):
  1. Agregue *timeout:1 attempts:5* al parámetro **NETCONFIG_DNS_RESOLVER_OPTIONS=""** en **/etc/sysconfig/network/config**. 
  2. Ejecute `netconfig update` para actualizar.
* **OpenLogic** (usa NetworkManager):
  1. Agregue *echo "options timeout:1 attempts:5"* a **/etc/NetworkManager/dispatcher.d/11-dhclient**. 
  2. Actualice con `service network restart`.

## <a name="name-resolution-that-uses-your-own-dns-server"></a>Resolución de nombres con su propio servidor DNS

Esta sección trata las instancias de rol y máquinas virtuales, y las aplicaciones web.

### <a name="vms-and-role-instances"></a>Máquinas virtuales e instancias de rol

Puede que sus necesidades de resolución de nombres superen las posibilidades de las características de Azure. Por ejemplo, esto puede suceder cuando se utilizan dominios de Active Directory o cuando se requiere la resolución DNS entre redes virtuales. Para abarcar estos escenarios, Azure le ofrece la posibilidad de que use sus propios servidores DNS.

Los servidores DNS dentro de una red virtual pueden reenviar consultas DNS a las resoluciones recursivas en Azure. Esto le permite resolver los nombres de host dentro de esa red virtual. Por ejemplo, un controlador de dominio (DC) que se ejecute en Azure puede responder a las consultas DNS referidas a sus dominios y reenviar todas las demás consultas a Azure. El reenvío de consultas permite que las máquinas virtuales vean sus recursos locales (mediante el controlador de dominio) y los nombres de host proporcionados por Azure (mediante el reenviador). El acceso a las resoluciones recursivas de Azure se proporciona mediante la IP virtual 168.63.129.16.

El reenvío de DNS también habilita la resolución de DNS entre redes virtuales y permite que los equipos locales resuelvan nombres de host proporcionados por Azure. Para resolver el nombre de host de una máquina virtual, la máquina virtual del servidor DNS debe residir en la misma red virtual y debe configurarse para reenviar consultas de nombre de host a Azure. Dado que el sufijo DNS es diferente en cada red virtual, puede usar las reglas de desvío condicional para enviar consultas de DNS a la red virtual correcta para su resolución. En la imagen siguiente se muestran dos redes virtuales y una red local que está realizando resolución DNS entre redes virtuales con este método. Puede encontrar un reenviador DNS de ejemplo disponible en la [galería de plantillas de inicio rápido de Azure](https://azure.microsoft.com/documentation/templates/301-dns-forwarder/) y en [GitHub](https://github.com/Azure/azure-quickstart-templates/tree/master/301-dns-forwarder).

> [!NOTE]
> Una instancia de rol puede realizar la resolución de nombres de máquinas virtuales dentro de la misma red virtual. Para ello, use el FQDN, que consta del nombre de host de la máquina virtual y el sufijo DNS **internal.cloudapp.net**. Sin embargo, en este caso, la resolución de nombres solo es correcta si la instancia de rol tiene el nombre de máquina virtual definido en el [esquema Role (archivo .cscfg)](https://msdn.microsoft.com/library/azure/jj156212.aspx). 
>    <Role name="<role-name>" vmName="<vm-name>">
> 
> Las instancias de rol que tienen que realizar la resolución de nombres de máquinas virtuales en otra red virtual (FQDN utilizando el sufijo **internal.cloudapp.net**) tienen que hacerlo con el método descrito en esta sección (reenvío de servidores DNS personalizados entre las dos redes virtuales).
>

![Diagrama de DNS entre redes virtuales](./media/virtual-networks-name-resolution-for-vms-and-role-instances/inter-vnet-dns.png)

Cuando se utiliza la resolución de nombres proporcionada con Azure, el Protocolo dinámico de configuración de host (DHCP) de Azure proporciona un sufijo DNS interno (**. internal.cloudapp.net**) a cada máquina virtual. Este sufijo permite la resolución de nombres de host, dado que los registros de nombre de host están en la zona **internal.cloudapp.net**. Si usa su propia solución de resolución de nombres, no se proporciona este sufijo a las máquinas virtuales puesto que interfiere con otras arquitecturas DNS (como es el caso de los escenarios con unión a un dominio). En su lugar, Azure proporciona un marcador de posición no funcional (*reddog.microsoft.com*).

Si es necesario, el sufijo DNS interno se puede determinar con PowerShell o la API:

* En el caso de redes virtuales en modelos de implementación de Azure Resource Manager, el sufijo está disponible con el recurso de la [API REST de interfaz de red](/rest/api/virtualnetwork/networkinterfaces/get) o del cmdlet [Get-AzureRmNetworkInterface](/powershell/module/azurerm.network/get-azurermnetworkinterface) de PowerShell y del comando de la CLI de Azure [az network nic show](/cli/azure/network/nic#az-network-nic-show).
* En los modelos de implementación clásica, el sufijo está disponible mediante la llamada a la [API de Get Deployment](https://msdn.microsoft.com/library/azure/ee460804.aspx) o por medio del cmdlet [Get-AzureVM -Debug](/powershell/module/azure/get-azurevm).

Si el reenvío de consultas a Azure no satisface sus necesidades, debería proporcionar su propia solución DNS. La solución DNS debe:

* Proporcionar la resolución adecuada de nombres de host, por ejemplo, mediante [DDNS](virtual-networks-name-resolution-ddns.md). Observe que, si usa DDNS, puede que tenga que deshabilitar la limpieza de registros DNS. Las concesiones DHCP de Azure son muy largas y la limpieza puede eliminar registros DNS de forma prematura. 
* Proporcionar la resolución recursiva adecuada para permitir la resolución de nombres de dominio externos.
* Estar accesible (TCP y UDP en el puerto 53) desde los clientes a los que sirve y poder acceder a Internet.
* Tener protección contra el acceso desde Internet para mitigar las amenazas que suponen los agentes externos.

> [!NOTE]
> Para obtener el mejor rendimiento, cuando se usan máquinas virtuales de Azure como servidores DNS, debe deshabilitarse IPv6. Debe asignarse una [dirección IP pública de nivel de instancia](virtual-networks-instance-level-public-ip.md) a cada máquina virtual del servidor DNS. Si desea realizar optimizaciones y análisis de rendimiento adicionales al utilizar Windows Server como servidor DNS, consulte [Name resolution performance of a recursive Windows DNS Server 2012 R2](http://blogs.technet.com/b/networking/archive/2015/08/19/name-resolution-performance-of-a-recursive-windows-dns-server-2012-r2.aspx) (Rendimiento de resolución de nombres de un servidor Windows DNS Server 2012 R2 recursivo).
> 
> 

### <a name="web-apps"></a>Aplicaciones web
Supongamos que necesita realizar la resolución de nombres de una aplicación web integrada con App Service y vinculada a una red virtual, a las máquinas virtuales en la misma red virtual. Además de configurar un servidor DNS personalizado que tenga un reenviador DNS que reenvíe las consultas a Azure (dirección IP virtual 168.63.129.16), realice los pasos siguientes:
1. Habilite la integración de red virtual de la aplicación web, si aún no lo ha hecho, tal y como se describe en [Integración de su aplicación con una instancia de Azure Virtual Network](../app-service/web-sites-integrate-with-vnet.md?toc=%2fazure%2fvirtual-network%2ftoc.json).
2. En Azure Portal, para el plan de App Service que hospeda la aplicación web, seleccione **Sincronizar red** en **Redes**, **Integración de Virtual Network**.

    ![Captura de pantalla de la resolución de nombres de red virtual](./media/virtual-networks-name-resolution-for-vms-and-role-instances/webapps-dns.png)

Supongamos que necesita realizar la resolución de nombres de una aplicación web compilada con App Service y vinculada a una red virtual, a las máquinas virtuales de una red virtual diferente. Esto requiere el uso de servidores DNS personalizados en ambas redes virtuales, como se indica a continuación: 
* Configure un servidor DNS en la red virtual de destino en una máquina virtual que también pueda reenviar consultas a la resolución recursiva de Azure (dirección IP virtual 168.63.129.16). Puede encontrar un reenviador DNS de ejemplo disponible en la [galería de plantillas de inicio rápido de Azure](https://azure.microsoft.com/documentation/templates/301-dns-forwarder) y en [GitHub](https://github.com/Azure/azure-quickstart-templates/tree/master/301-dns-forwarder). 
* Configure un reenviador DNS en la red virtual de origen en una máquina virtual. Configure este reenviador DNS para que reenvíe consultas al servidor DNS de la red virtual de destino.
* Configure el servidor DNS de origen en la configuración de su red virtual de origen.
* Habilite la integración de red virtual para la aplicación web para vincular a la red virtual de origen, siguiendo las instrucciones de [Integración de su aplicación con una instancia de Azure Virtual Network](../app-service/web-sites-integrate-with-vnet.md?toc=%2fazure%2fvirtual-network%2ftoc.json).
* En Azure Portal, para el plan de App Service que hospeda la aplicación web, seleccione **Sincronizar red** en **Redes**, **Integración de Virtual Network**. 

## <a name="specify-dns-servers"></a>Especificación de los servidores DNS
Si usa sus propios servidores DNS, Azure permite especificar varios servidores DNS por cada red virtual. También puede hacer esto para cada interfaz de red (para Azure Resource Manager) o servicio en la nube (para el modelo de implementación clásica). Los servidores DNS especificados para una interfaz de red o un servicio en la nube tienen prioridad sobre los servidores DNS especificados para la red virtual.

> [!NOTE]
> Las propiedades de conexión de red, como las direcciones IP del servidor DNS, no deben editarse directamente dentro de máquinas virtuales Windows. Esto se debe a que podrían borrarse durante el servicio de reparación cuando se reemplaza el adaptador de la red virtual. 
> 
> 

Cuando se usa el modelo de implementación de Azure Resource Manager, puede especificar servidores DNS en Azure Portal. Consulte [Redes virtuales](https://msdn.microsoft.com/library/azure/mt163661.aspx) e [Interfaces de red](https://msdn.microsoft.com/library/azure/mt163668.aspx). También puede hacer esto en PowerShell. Consulte [Redes virtuales](/powershell/module/AzureRM.Network/New-AzureRmVirtualNetwork) e [interfaz de red](/powershell/module/azurerm.network/new-azurermnetworkinterface).

Cuando se usa el modelo de implementación clásica, puede especificar servidores DNS para la red virtual en Azure Portal o en el [archivo de configuración de red](https://msdn.microsoft.com/library/azure/jj157100). En el caso de los servicios en la nube, puede especificar los servidores DNS mediante el [archivo de configuración del servicio](https://msdn.microsoft.com/library/azure/ee758710) o mediante PowerShell, con [New-AzureVM](/powershell/module/azure/new-azurevm).

> [!NOTE]
> Si cambia la configuración de DNS para una red virtual o una máquina virtual que ya estén implementadas, deberá reiniciar cada máquina virtual afectada para que los cambios surtan efecto.
> 
> 

## <a name="next-steps"></a>Pasos siguientes

Modelo de implementación de Azure Resource Manager:

* [Creación o actualización de una red virtual](https://msdn.microsoft.com/library/azure/mt163661.aspx)
* [Creación o actualización de una tarjeta de interfaz de red](https://msdn.microsoft.com/library/azure/mt163668.aspx)
* [New-AzureRmVirtualNetwork](/powershell/module/azurerm.network/new-azurermvirtualnetwork)
* [New-AzureRmNetworkInterface](/powershell/module/azurerm.network/new-azurermnetworkinterface)

Modelo de implementación clásico:

* [Esquema de configuración del servicio de Azure](https://msdn.microsoft.com/library/azure/ee758710)
* [Esquema de configuración de Virtual Network](https://msdn.microsoft.com/library/azure/jj157100)
* [Configuración de Virtual Network con un archivo de configuración de red](virtual-networks-using-network-configuration-file.md)
