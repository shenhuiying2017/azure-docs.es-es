<properties 
   pageTitle="Resolución de máquinas virtuales e instancias de rol"
	description="Escenarios de resolución de nombres para IaaS de Azure, soluciones híbridas, entre servicios en la nube diferentes, Active Directory y con su propio servidor DNS"
	services="virtual-network"
	documentationCenter="na"
	authors="joaoma"
	manager="jdial"
	editor="tysonn"/>
<tags 
   ms.service="virtual-network"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="infrastructure-services"
	ms.date="08/10/2015"
	ms.author="joaoma"/>

# Resolución de nombres para las máquinas virtuales e instancias de rol

Dependiendo de cómo utilice Azure para hospedar la IaaS, la PaaS y las soluciones híbridas, puede que necesite permitir que las máquinas virtuales y las instancias de rol creadas se comuniquen entre sí. Aunque este tipo de comunicación puede realizarse mediante las direcciones IP, es mucho más sencillo usar nombres ya que podrá recordarlos con más facilidad y no cambiarlos.

Cuando las instancias de rol y las máquinas virtuales hospedadas en Azure necesitan resolver nombres de dominio en direcciones IP internas, pueden usar uno de dos métodos:

- [Resolución de nombres de Azure](#azure-provided-name-resolution)

- [Resolución de nombres mediante su propio servidor DNS](#name-resolution-using-your-own-dns-server)

El tipo de resolución de nombres que tenga que usar dependerá de cómo se comuniquen las máquinas virtuales y las instancias de rol entre sí.

**La siguiente tabla muestra los escenarios y las soluciones de resolución de nombre correspondientes:**

| **Escenario** | **Resolución de nombres proporcionado por:** | **Para obtener más información, consulte:** |
|--------------|----------------------------------|-------------------------------|
| Resolución de nombres entre instancias de rol o máquinas virtuales ubicadas en el mismo servicio en la nube | Resolución de nombres de Azure | [Resolución de nombres de Azure](#azure-provided-name-resolution)|
| Resolución de nombres entre instancias de rol y máquinas virtuales ubicadas en la misma red virtual | Resolución de nombres proporcionados por Azure (solo para implementaciones basadas en ARM) o resolución de nombres con su propio servidor DNS | [Resolución de nombres de Azure](#azure-provided-name-resolution), [Resolución de nombres mediante su propio servidor DNS](#name-resolution-using-your-own-dns-server), [Requisitos del servidor DNS](#dns-server-requirements) |
| Resolución de nombres entre instancias de rol y máquinas virtuales ubicadas en diferentes redes virtuales | Resolución de nombres mediante su propio servidor DNS| [Resolución de nombres mediante su propio servidor DNS](#name-resolution-using-your-own-dns-server), [Requisitos del servidor DNS](#dns-server-requirements)|
| Entre locales: resolución de nombres entre instancias de rol o máquinas virtuales en Azure u en equipos locales| Resolución de nombres mediante su propio servidor DNS| [Resolución de nombres mediante su propio servidor DNS](#name-resolution-using-your-own-dns-server), [Requisitos del servidor DNS](#dns-server-requirements)|
| Búsqueda inversa de direcciones IP internas| Resolución de nombres mediante su propio servidor DNS| [Resolución de nombres mediante su propio servidor DNS](#name-resolution-using-your-own-dns-server), [Requisitos del servidor DNS](#dns-server-requirements)|
| Resolución de nombres para dominios no públicos (por ejemplo, dominios de Active Directory)| Resolución de nombres mediante su propio servidor DNS| [Resolución de nombres mediante su propio servidor DNS](#name-resolution-using-your-own-dns-server), [Requisitos del servidor DNS](#dns-server-requirements)|
| Resolución de nombres entre instancias de rol ubicadas en diferentes servicios en la nube y no en una red virtual| No aplicable. La conectividad entre máquinas virtuales e instancias de rol de servicios en la nube diferentes no es compatible fuera de una red virtual.| No aplicable.|


## Resolución de nombres de Azure

Junto con la resolución de nombres DNS públicos, Azure proporciona una resolución de nombres interna para máquinas virtuales e instancias de rol que residen dentro de la misma red virtual o servicio en la nube. Las máquinas virtuales o instancias de un servicio en la nube comparten el mismo sufijo DNS, por lo que el nombre de host solo es suficiente. En las redes virtuales clásicas, los distintos servicios en la nube tienen diferentes sufijos DNS, por lo que es necesario un nombre de dominio completo. En las redes virtuales basadas en ARM, el sufijo DNS es común en toda la red virtual, por lo que no es necesario el nombre de dominio completo, y el nombre DNS puede asignarse a la NIC o a la máquina virtual. Aunque la resolución de nombres que proporciona Azure no necesita ningún tipo de configuración, no es la opción más adecuada para todos los escenarios de implementación, tal y como se mostró en la tabla anterior.

> [AZURE.NOTE]En el caso de los roles web y de trabajo, puede acceder a las direcciones IP internas de las instancias de rol que estén basadas en el nombre de rol y el número de instancia, mediante la API de REST de la Administración de servicios de Azure. Para obtener más información, consulte la [Referencia de la API de REST de la administración de servicios](https://msdn.microsoft.com/library/azure/ee460799.aspx).

### Características y consideraciones

**Características**

- Facilidad de uso: no es necesario realizar configuración alguna para usar la resolución de nombres proporcionada por Azure.

- Las instancias de rol o las máquinas virtuales que están dentro del mismo servicio en la nube son las que proporcionan la resolución de nombres sin necesidad de un nombre de dominio completo.

- La resolución de nombres se proporciona siempre entre máquinas virtuales en redes virtuales basadas en ARM sin necesidad del nombre de dominio completo, las redes clásicas requieren el nombre de dominio completo al resolver los nombres de los distintos servicios en la nube.

- Puede crear los nombres de host que describan mejor las implementaciones, en lugar de trabajar con nombres generados automáticamente.

**Consideraciones:**

- La resolución de nombres entre redes virtuales no está disponible.

- Solo se pueden registrar los nombres de host para máquinas virtuales e instancias de rol que residan en los primeros 180 servicios en la nube agregados a una red virtual de Azure. Si tiene más de 180 servicios en la nube, independientemente del número de máquinas virtuales e instancias de rol de cada servicio, deberá proporcionar su propio servidor DNS para la resolución de nombres.

- La resolución de nombres entre locales no está disponible.

- No se puede modificar el sufijo DNS creado por Azure.

- No se pueden registrar manualmente los registros propios.

- No se admiten ni WINS ni NetBIOS. (No puede enumerar las máquinas virtuales en el explorador de red del Explorador de Windows).

- Los nombres de host deben ser compatibles con el DNS (solamente pueden usar los caracteres 0-9, a-z y “-” y no pueden comenzar ni terminar con un “-”. Consulte la sección 2 de RFC 3696).

- El tráfico de consultas de DNS está limitado por la máquina virtual. Si la aplicación realiza frecuentemente consultas DNS en varios nombres de destino, es posible que algunas consultas agoten el tiempo de espera. Para evitar esto, se recomienda habilitar el almacenamiento en caché del cliente. Está habilitado de forma predeterminada en Windows, pero algunas distribuciones de Linux no tienen habilitada el almacenamiento en caché.

## Resolución de nombres mediante su propio servidor DNS

Si los requisitos de resolución de nombres van más allá de las características proporcionadas por Azure, tiene la opción de usar su propio servidor DNS. Cuando use su propio servidor DNS, solo usted será el responsable de administrar los registros DNS.

> [AZURE.NOTE]Le recomendamos que evite el uso de un servidor DNS externo, a menos que lo requiera su escenario de implementación.

## Requisitos del servidor DNS

Si piensa usar la resolución de nombres que no se proporciona en Azure, el servidor DNS que especifique debe admitir lo siguiente:

- el servidor DNS debe aceptar el registro DNS dinámico a través del protocolo DNS dinámico (DDNS), o bien debe crear los registros necesarios.

- Si se basa en DNS dinámico, el servidor DNS debe tener desactivada la limpieza de registros. En Azure, las direcciones IP tienen concesiones DHCP largas, por lo que pueden ocasionar la eliminación de registros en el servidor DNS durante la limpieza.

- El servidor DNS debe tener la recursividad habilitada para permitir la resolución de nombres de dominio externos.

- Los clientes que soliciten la resolución de nombres, y los servicios y las máquinas virtuales que vayan a registrar sus nombres, han de poder acceder al servidor DNS debe (en el puerto TCP/UDP 53).

- Asimismo, le recomendamos que proteja el servidor DNS para evitar accesos desde Internet, ya que muchos bots se dedican a buscar resoluciones DNS recursivas abiertas.

- Para obtener un mejor rendimiento, cuando se usan máquinas virtuales de Azure como servidores DNS, se debe deshabilitar IPv6 y debe asignarse una [IP pública de nivel de instancia](virtual-networks-instance-level-public-ip.mp) a cada máquina virtual del servidor DNS.

## Especificar los servidores DNS

Puede especificar varios servidores DNS para que los usen las máquinas virtuales y las instancias de rol. Por cada consulta de DNS el cliente intentará probar primero su servidor DNS preferido y solo probará otros servidores alternativos si su servidor preferido no responde, es decir, las consultas de DNS no tendrán la carga equilibrada en los diferentes servidores DNS. Por este motivo, compruebe que tiene los servidores DNS enumerados en el orden correcto para su entorno.

> [AZURE.NOTE]Si cambia la configuración DNS en un archivo de configuración de red de una red virtual que ya esté implementada, deberá reiniciar cada máquina virtual para que los cambios surtan efecto.

### Especificación de un servidor DNS en el Portal de administración

Al crear la red virtual mediante el Portal de administración, puede especificar la dirección IP y el nombre del servidor DNS (o servidores) que desea usar. Una vez creada la red virtual, las máquinas virtuales y las instancias de rol que implemente en la red virtual se configurarán automáticamente mediante la configuración DNS especificada. Los servidores DNS especificados para un servicio específico en la nube (Azure clásico) o para una tarjeta de interfaz de red (implementaciones basadas en ARM) tienen prioridad sobre los especificados para la red virtual. Consulte [Acerca de la configuración de una red virtual en el Portal de administración](virtual-networks-settings.md).

### Especificación de un servidor DNS mediante el uso de archivos de configuración (Azure clásico)

En las redes virtuales clásicas, puede especificar la configuración DNS mediante el uso de dos archivos de configuración diferentes: el archivo de *configuración de red* y el archivo de *configuración de servicio*.

> [AZURE.NOTE]Los servidores DNS en el archivo de configuración de servicio sobrescribirán la configuración del archivo de configuración de red.
 
El archivo de configuración de red describe las redes virtuales de su suscripción. Al agregar instancias de rol o máquinas virtuales a cualquier servicio en la nube en una red virtual, la configuración DNS de su archivo de configuración de red se aplica a la instancia de rol o a la máquina virtual, a menos que se especifiquen servidores DNS específicos del servicio en la nube.

El archivo de configuración de servicio se crea por cada servicio en la nube que agregue a Azure. Al agregar instancias de rol o máquinas virtuales en el servicio en la nube, la configuración DNS de su archivo de configuración de servicio se aplica a la instancia de rol o a la máquina virtual.



## Pasos siguientes

[Esquema de configuración del servicio de Azure](https://msdn.microsoft.com/library/azure/ee758710)

[Esquema de configuración de Red virtual](https://msdn.microsoft.com/library/azure/jj157100)

[Configuración de una red virtual en el Portal de administración](virtual-networks-settings.md)

[Configuración de una red virtual con un archivo de configuración de red](virtual-networks-using-network-configuration-file.md)

<!---HONumber=September15_HO1-->