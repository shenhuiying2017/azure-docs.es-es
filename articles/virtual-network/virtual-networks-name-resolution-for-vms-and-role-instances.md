<properties 
   pageTitle="Resolución de máquinas virtuales e instancias de rol"
   description="Escenarios de resolución de nombres para IaaS de Azure, soluciones híbridas, entre servicios en la nube diferentes, Active Directory y con su propio servidor DNS"
   services="virtual-network"
   documentationCenter="na"
   authors="joaoma"
   manager="jdial"
   editor="tysonn" />
<tags 
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="06/30/2015"
   ms.author="joaoma" />

# Resolución de nombres para las máquinas virtuales e instancias de rol

Dependiendo de cómo utilice Azure para hospedar la IaaS, la PaaS y las soluciones híbridas, puede que necesite permitir que las máquinas virtuales y las instancias de rol que haya creado se comuniquen con otras máquinas virtuales e instancias de rol. Aunque este tipo de comunicación puede realizarse mediante las direcciones IP, es mejor usar nombres de host ya que podrá recordarlos con más facilidad. Sin embargo, esos nombres de host deben resolverse en direcciones IP para poder establecer la comunicación.

Cuando las instancias de rol y las máquinas virtuales hospedadas en Azure necesitan resolver nombres de host y los nombres de dominio en direcciones IP internas, pueden usar uno de dos métodos:

- [Resolución de nombres de Azure](azure-provided-name-resolution)

- [Resolución de nombres mediante su propio servidor DNS](name-resolution-using-your-own-DNS-server)

El tipo de resolución de nombres que tenga que usar dependerá de cómo se comuniquen las máquinas virtuales y las instancias de rol dentro del servicio en la nube y con otros servicios en la nube.

**La siguiente tabla muestra los escenarios y las soluciones de resolución de nombre correspondientes:**

| **Escenario** | **Resolución de nombres proporcionado por:** | **Para obtener más información, consulte:** |
|------------------------------------------------------------------------------------------------------|-------------------------------------------------------------------------------------------------------------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Resolución de nombres entre instancias de rol o máquinas virtuales ubicadas en el mismo servicio en la nube | Resolución de nombres de Azure | [Resolución de nombres de Azure](#azure-provided-name-resolution) |
| Resolución de nombres entre instancias de rol y máquinas virtuales ubicadas en la misma red virtual | Resolución de nombres proporcionado por Azure: mediante la resolución FQDNorName con su propio servidor DNS | - [Resolución de nombres de Azure](#azure-provided-name-resolution) - [Resolución de nombres mediante su propio servidor DNS](#name-resolution-using-your-own-dns-server) - [Requisitos del servidor DNS](#dns-server-requirements) |
| Resolución de nombres entre instancias de rol y máquinas virtuales ubicadas en diferentes redes virtuales | Resolución de nombres mediante su propio servidor DNS | - [Resolución de nombres mediante su propio servidor DNS](#name-resolution-using-your-own-dns-server) - [Requisitos del servidor DNS](#dns-server-requirements) |
| Entre locales: resolución de nombres entre instancias de rol o máquinas virtuales en Azure u en equipos locales | Resolución de nombres mediante su propio servidor DNS | - [Resolución de nombres mediante su propio servidor DNS](#name-resolution-using-your-own-dns-server) - [Requisitos del servidor DNS](#dns-server-requirements) |
| Búsqueda inversa de direcciones IP internas | Resolución de nombres mediante su propio servidor DNS | - [Resolución de nombres mediante su propio servidor DNS](#name-resolution-using-your-own-dns-server) - [Requisitos del servidor DNS](#dns-server-requirements) |
| Resolución de nombres para dominios personalizados (por ejemplo, dominios de Active Directory, dominios que usted mismo registra, etc.) | Resolución de nombres mediante su propio servidor DNS | - [Resolución de nombres mediante su propio servidor DNS](#name-resolution-using-your-own-dns-server) - [Requisitos del servidor DNS](#dns-server-requirements) |
| Resolución de nombres entre instancias de rol ubicadas en diferentes servicios en la nube y no en una red virtual | No aplicable. La conectividad entre máquinas virtuales e instancias de rol de servicios en la nube diferentes no es compatible fuera de una red virtual. | No aplicable. |

> [AZURE.NOTE]Azure proporciona automáticamente la resolución de nombres entre equipos en Internet y los extremos públicos y no requiere configuración.

## Resolución de nombres de Azure

Junto con la resolución para los dominios de Internet públicos, Azure proporciona una resolución de nombres de nombre de host para máquinas virtuales e instancias de rol que residen en el mismo servicio en la nube gracias al uso de nombres de host, y para las máquinas virtuales e instancias de rol de servicios en la nube diferentes que residen en la misma red virtual gracias al uso de FQDN. Aunque la resolución de nombres que proporciona Azure no necesita ningún tipo de configuración, no es la opción más adecuada para todos los escenarios de implementación, tal y como se mostró en la tabla anterior.

> [AZURE.NOTE]En el caso de los roles web y de trabajo, puede acceder a las direcciones IP internas de las instancias de rol que estén basadas en el nombre de rol y el número de instancia, mediante la API de REST de la Administración de servicios de Azure. Para obtener más información, consulte la [Referencia de la API de REST de la Administración de servicios](https://msdn.microsoft.com/library/azure/ee460799.aspx).

### Características y consideraciones

**Características**

- Facilidad de uso: no es necesario realizar configuración alguna para usar el servicio DNS proporcionado por Azure.

- Las instancias de rol o las máquinas virtuales que están dentro del mismo servicio en la nube son las que proporcionan la resolución de nombres de host.

- Las instancias de rol y las máquinas virtuales que se encuentran en la misma red virtual son las que proporcionan la resolución de nombres, pero en servicios en la nube diferentes, mediante el FQDN de la instancia de rol o la máquina virtual de destino.

- Puede crear los nombres de host que describan mejor las implementaciones, en lugar de trabajar con nombres generados automáticamente.

- Puede usar las búsquedas DNS estándar para resolver nombres de dominio público.

**Consideraciones:**

- La resolución de nombres entre redes virtuales no está disponible.

- Sólo se pueden registrar los nombres de host y FQDN para máquinas virtuales e instancias de rol que residan en los primeros 180 servicios en la nube agregados a una red virtual de Azure. Si tiene más de 180 servicios en la nube, independientemente del número de máquinas virtuales e instancias de rol de cada servicio, deberá proporcionar su propio servidor DNS para la resolución de nombres.

- No puede usar varios nombres de host para la misma máquina virtual o instancia de rol.

- La resolución de nombres entre locales no está disponible.

- No se puede modificar el sufijo DNS creado por Azure.

- No puede registrar manualmente sus propios registros en un DNS proporcionado por Azure.

- No se admiten ni WINS ni NetBIOS. (No puede enumerar las máquinas virtuales en el explorador de red del Explorador de Windows).

- Los nombres de host deben ser compatibles con el DNS (solamente pueden usar los caracteres 0-9, a-z y “-” y no pueden comenzar ni terminar con un “-”. Consulte la sección 2 de RFC 3696).

- El tráfico de consultas de DNS está limitado por la máquina virtual. Si la aplicación realiza frecuentemente consultas DNS en varios nombres de destino, es posible que algunas consultas agoten el tiempo de espera. Para evitar esto, se recomienda utilizar el almacenamiento en caché del cliente.

## Resolución de nombres mediante su propio servidor DNS

Si los requisitos de resolución de nombres van más allá de las características proporcionadas por Azure, tiene la opción de usar su propio servidor DNS. Cuando use su propio servidor DNS, solo usted será el responsable de administrar los registros necesarios para sus servicios en la nube.

> [AZURE.NOTE]Le recomendamos que evite el uso de un servidor DNS externo, a menos que lo requiera su escenario de implementación.

## Requisitos del servidor DNS

Si piensa usar la resolución de nombres que no se proporciona en Azure, el servidor DNS que especifique debe admitir lo siguiente: el servidor DNS debe aceptar el registro DNS dinámico a través del DNS dinámico (DDNS).

- El servidor DNS debe tener desactivada la limpieza de registros. Direcciones IP de Azure tienen concesiones largas, por lo que pueden ocasionar la eliminación de registros en el servidor DNS durante la limpieza.

- El servidor DNS debe tener la recursividad habilitada para permitir la resolución de nombres de dominio externos.

- Los clientes que soliciten la resolución de nombres, y los servicios y las máquinas virtuales que vayan a registrar sus nombres, han de poder acceder al servidor DNS debe (en el puerto TCP/UDP 53).

- Asimismo, le recomendamos que proteja el servidor DNS para evitar accesos desde Internet, ya que muchos bots se dedican a buscar resoluciones DNS recursivas abiertas.


## Especificar los servidores DNS

Puede especificar varios servidores DNS para que los usen las máquinas virtuales y las instancias de rol. Sin embargo, cuando haga esto, los servidores DNS se usarán en el orden que haya especificado en la conmutación por error (como oposición a round robin). Por cada consulta de DNS el cliente intentará probar primero su servidor DNS preferido y solo probará otros servidores alternativos si su servidor preferido no responde. Por este motivo, compruebe que tiene los servidores DNS enumerados en el orden correcto para su entorno.

> [AZURE.NOTE]Si cambia la configuración DNS en un archivo de configuración de red de una red virtual que ya esté implementada, deberá reiniciar cada máquina virtual para que los cambios surtan efecto.

### Especificar un servidor DNS mediante el Portal de administración

Al crear la red virtual mediante el Portal de administración, puede especificar la dirección IP y el nombre del servidor DNS (o servidores) que desea utilizar. Una vez creada la red virtual, las máquinas virtuales y las instancias de rol que implemente en la red virtual se configurarán automáticamente mediante la configuración DNS especificada, a menos que especifique qué servidores DNS se usarán para la implementación. Para obtener más información acerca de cómo configurar las opciones de la red virtual de Azure, consulte [Cómo configurar una red virtual del Portal de administración](https://msdn.microsoft.com/library/azure/jj156074.aspx).

> [AZURE.NOTE]Sólo puede utilizar hasta 9 servidores DNS.

### Especificar un servidor DNS mediante el uso de archivos de configuración

Puede especificar la configuración DNS mediante el uso de dos archivos de configuración diferentes: el archivo de *Configuración de red* y el archivo de *Configuración de servicio*.

Se crea un archivo de configuración de red por cada red virtual que agrega a Azure. Al agregar instancias de rol o máquinas virtuales a cualquier servicio en la nube en una red virtual, la configuración DNS de su archivo de configuración de red se aplica a la instancia de rol o a la máquina virtual, a menos que el archivo de configuración de servicio tenga su propia configuración DNS.

El archivo de configuración de servicio se crea por cada servicio en la nube que agregue a Azure. Al agregar instancias de rol o máquinas virtuales en el servicio en la nube, la configuración DNS de su archivo de configuración de servicio se aplica a la instancia de rol o a la máquina virtual.

> [AZURE.NOTE]La configuración en el archivo de configuración de servicio sobrescribirá el archivo de configuración de red. Por ejemplo, si se agrega una máquina virtual a un servicio en la nube que forma parte de una red virtual y el archivo de configuración de red y el archivo de configuración de servicio tienen una configuración DNS, la configuración DNS en el archivo de configuración de servicio es la que se aplica a la máquina virtual.


## Otras referencias

[Esquema de configuración del servicio de Azure](https://msdn.microsoft.com/library/azure/ee758710) [Esquema de configuración de red virtual](https://msdn.microsoft.com/library/azure/jj157100) [Cómo configurar la red virtual en el Portal de administración](https://msdn.microsoft.com/library/azure/jj156074.aspx) [Configurar una red virtual mediante un archivo de configuración de red](https://msdn.microsoft.com/library/azure/jj156097.aspx) [Tareas de configuración de la red virtual de Azure](https://msdn.microsoft.com/library/azure/jj156206.aspx)

<!---HONumber=July15_HO2-->