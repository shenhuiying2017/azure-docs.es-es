<properties
	pageTitle="Fase 1 de la granja de SharePoint Server 2013 | Microsoft Azure"
	description="Cree la red virtual y otros elementos de la infraestructura de Azure en la fase 1 de la granja de SharePoint Server 2013 en Azure."
	documentationCenter=""
	services="virtual-machines"
	authors="JoeDavies-MSFT"
	manager="timlt"
	editor=""
	tags="azure-service-management"/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="Windows"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/21/2015"
	ms.author="josephd"/>

# Fase 1 de la carga de trabajo de la granja de servidores de intranet de SharePoint: Configurar Azure

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-include.md)]En este artículo se trata la creación de recursos con el modelo de implementación clásica.

En esta fase de la implementación de una granja de servidores solo de intranet de SharePoint 2013 con grupos de disponibilidad AlwaysOn de SQL Server en los servicios de infraestructura de Azure, creará la infraestructura de red y almacenamiento de Azure en Administración de servicios de Azure. Debe completar esta fase antes de pasar a la [fase 2](virtual-machines-workload-intranet-sharepoint-phase2.md). Consulte [Implementación de SharePoint con grupos de disponibilidad AlwaysOn de SQL Server en Azure](virtual-machines-workload-intranet-sharepoint-overview.md) para ver todas las fases.

Azure debe contar con los siguientes componentes de red básicos:

- Una red virtual entre locales con una subred
- Tres servicios en la nube de Azure
- Una cuenta de almacenamiento de Azure para almacenar imágenes de disco de VHD y discos de datos adicionales

## Antes de empezar

Antes de empezar a configurar los componentes de Azure, rellene las tablas siguientes. Para que le resulte más fácil seguir los procedimientos para configurar Azure, imprima esta sección y anote la información necesaria, o copie esta sección en un documento y rellene los campos.

Para la configuración de la red virtual (VNet), rellene la tabla V.

Elemento | Elemento configuración | Descripción | Valor
--- | --- | --- | ---
1\. | Nombre de red virtual | Nombre que se va a asignar a la red virtual de Azure (por ejemplo, SPFarmNet). | \_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_
2\. | Ubicación de la red virtual | Centro de datos Azure que contendrá la red virtual. | \_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_
3\. | Nombre de la red local | Nombre que se va a asignar a la red de su organización. | \_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_
4\. | Dirección IP del dispositivo VPN | Dirección IPv4 pública de la interfaz del dispositivo VPN en Internet. Trabaje con su departamento de TI para determinar esta dirección. | \_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_
5\. | Espacio de direcciones de red virtual | Espacio de direcciones (definido en un prefijo de dirección privada único) para la red virtual. Trabaje con su departamento de TI para determinar este espacio de direcciones. | \_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_
6\. | Primer servidor DNS final | Cuarta dirección IP posible para el espacio de direcciones de la subred de la red virtual (vea la tabla S). Trabaje con su departamento de TI para determinar estas direcciones. | \_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_
7\. | Segundo servidor DNS final | Quinta dirección IP posible para el espacio de direcciones de la subred de la red virtual (vea la tabla S). Trabaje con su departamento de TI para determinar estas direcciones. | \_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_

**Tabla V: Configuración de red virtual entre locales**

Rellene la tabla S para la subred de esta solución. Asigne a la subred un nombre descriptivo, un espacio de direcciones IP único basado en el espacio de direcciones de la red virtual y una finalidad descriptiva. El espacio de direcciones debe estar en formato de enrutamiento de interdominios sin clases (CIDR), también conocido como formato de prefijo de red. Un ejemplo es 10.24.64.0/20. Trabaje con su departamento de TI para determinar este espacio de direcciones a partir del espacio de direcciones de la red virtual.

Elemento | Nombre de subred | Espacio de direcciones de subred | Propósito
--- | --- | --- | ---
1\. | \_\_\_\_\_\_\_\_\_\_\_\_\_\_\_ | \_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_ | \_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_

**Tabla S: Subredes de la red virtual**

> [AZURE.NOTE]Esta arquitectura predefinida usa una sola subred por motivos de simplicidad. Si desea superponer un conjunto de filtros de tráfico para emular el aislamiento de subred, puede usar los [grupos de seguridad de red](virtual-networks-nsg.md) de Azure.

Para los dos servidores DNS locales que desea usar al configurar inicialmente los controladores de dominio de la red virtual, rellene tabla D. Asigne a cada servidor DNS un nombre descriptivo y una dirección IP única. No es necesario que este nombre descriptivo coincida con el nombre de host o el nombre de equipo del servidor DNS. Tenga en cuenta que se muestran dos entradas en blanco, pero puede agregar más. Trabaje con su departamento de TI para determinar esta lista.

Elemento | Nombre descriptivo del servidor DNS | Dirección IP del servidor DNS
--- | --- | ---
1\. | \_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_ | \_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_
2\. | \_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_ | \_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_

**Tabla D: Servidores DNS locales**

Para enrutar paquetes de la red entre locales a la red de su organización a través de la conexión VPN de sitio a sitio, debe configurar la red virtual con una red local que contenga una lista con los espacios de direcciones (en notación CIDR) de todas las ubicaciones accesibles en la red local de su organización. La lista de espacios de direcciones que definen la red local no debe incluir ni solaparse con el espacio de direcciones usado para otras redes virtuales u otras redes locales. En otras palabras, los espacios de direcciones de las redes virtuales y locales configuradas deben ser únicos.

Para el conjunto de espacios de direcciones de la red local, rellene la tabla L. Observe que aparecen tres entradas en blanco, pero normalmente se necesitan más. Trabaje con su departamento de TI para determinar esta lista de espacios de direcciones.

Elemento | Espacio de direcciones de la red local
--- | ---
1\. | \_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_
2\. | \_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_
3\. | \_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_

**Tabla L: Prefijos de dirección de la red local**

Para crear la red virtual con la configuración de las tablas V, S, D y L, use las instrucciones indicadas en [Crear una red virtual entre locales usando las tablas de configuración](virtual-machines-workload-deploy-vnet-config-tables.md).

> [AZURE.NOTE]Este procedimiento le guiará en el proceso de creación de una red virtual que usa una conexión VPN de sitio a sitio. Para obtener información sobre cómo usar ExpressRoute para la conexión de sitio a sitio, vea [Introducción técnica a ExpressRoute](../expressroute/expressroute-introduction.md).

Después de crear la red virtual de Azure, el Portal de administración de Azure determinará lo siguiente:

- La dirección IPv4 pública de la puerta de enlace VPN de Azure para la red virtual.
- La clave precompartida del protocolo de seguridad de Internet (IPsec) para la conexión VPN de sitio a sitio.

Para ver estos datos en el Portal de administración de Azure después de crear la red virtual, haga clic en **Redes**, en el nombre de la red virtual y, a continuación, en la opción de menú **Panel**.

A continuación, configurará la puerta de enlace de red virtual para crear una conexión VPN de sitio a sitio segura. Vea [Configurar una puerta de enlace de red virtual en el Portal de administración](../vpn-gateway/vpn-gateway-configure-vpn-gateway-mp.md) para consultar las instrucciones.

A continuación, cree la conexión VPN de sitio a sitio entre la nueva red virtual y un dispositivo VPN local. Para obtener más detalles, vea [Configurar una puerta de enlace de red virtual en el Portal de administración](../vpn-gateway/vpn-gateway-configure-vpn-gateway-mp.md).

A continuación, asegúrese de que el espacio de direcciones de la red virtual sea accesible desde la red local. Esto se hace normalmente agregando una ruta correspondiente al espacio de direcciones de la red virtual a su dispositivo VPN y, a continuación, anunciando esa ruta al resto de la infraestructura de enrutamiento de la red de su organización. Trabaje con su departamento de TI para determinar cómo hacerlo.

A continuación, siga las instrucciones en [Cómo instalar y configurar Azure PowerShell](../install-configure-powershell.md) para instalar Azure PowerShell en el equipo local. Abra un símbolo del sistema de Azure PowerShell.

En primer lugar, seleccione la suscripción de Azure correcta con estos comandos. Reemplace todo el contenido dentro de las comillas, incluidos los caracteres < and >, por los nombres correctos.

	$subscr="<Subscription name>"
	Select-AzureSubscription -SubscriptionName $subscr –Current

Puede obtener el nombre de suscripción con la propiedad **SubscriptionName** de la salida del comando **Get-AzureSubscription**.

A continuación, cree los tres servicios en la nube necesarios para esta granja de servidores de SharePoint. Rellene la tabla C.

Elemento | Propósito | Nombre del servicio en la nube
--- | --- | ---
1\. | Controladores de dominio | \_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_
2\. | Servidores SQL Server | \_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_
3\. | Servidores de SharePoint | \_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_

**Tabla C: Nombres del servicio en la nube**

Debe elegir un nombre único para cada servicio en la nube. *El nombre del servicio en la nube solo puede contener letras, números y guiones. El primer y el último carácter del campo deben ser una letra o un número.*

Por ejemplo, podría asignar el nombre DCs-*UniqueSequence* al primer servicio en la nube, en el que *UniqueSequence* es una abreviatura de su organización. Por ejemplo, si el nombre de su organización es Tailspin Toys, podría asignar el nombre DCs-Tailspin al servicio en la nube.

Puede comprobar que el nombre sea único con el siguiente comando de Azure PowerShell en el equipo local.

	Test-AzureName -Service <Proposed cloud service name>

Si este comando devuelve "False", el nombre propuesto es único. A continuación, cree el servicio en la nube con lo que se indica a continuación.

	New-AzureService -Service <Unique cloud service name> -Location "<Table V – Item 2 – Value column>"

Registre el nombre real de cada servicio en la nube recién creado en la tabla C.

A continuación, cree una cuenta de almacenamiento para la granja de servidores de SharePoint. *Debe elegir un nombre único que contenga solo letras minúsculas y números.* Se puede comprobar la exclusividad del nombre de la cuenta de almacenamiento con el siguiente comando de Azure PowerShell.

	Test-AzureName -Storage <Proposed storage account name>

Si este comando devuelve "False", el nombre propuesto es único. A continuación, cree la cuenta de almacenamiento y establezca la suscripción para usarla con estos comandos.

	$staccount="<Unique storage account name>"
	New-AzureStorageAccount -StorageAccountName $staccount -Location "<Table V – Item 2 – Value column>"
	Set-AzureSubscription -SubscriptionName $subscr -CurrentStorageAccountName $staccount

Posteriormente, defina los nombres de cuatro conjuntos de disponibilidad. Rellene la tabla A.

Elemento | Propósito | Nombre del conjunto de disponibilidad
--- | --- | ---
1\. | Controladores de dominio | \_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_
2\. | Servidores SQL Server | \_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_
3\. | Servidores de aplicaciones de SharePoint | \_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_
4\. | Servidores front-end web de SharePoint | \_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_

**Tabla A: Nombre del conjunto de disponibilidad**

Necesitará estos nombres al crear las máquinas virtuales en las fases 2, 3 y 4.

Esta es la configuración resultante de la realización correcta de esta fase.

![](./media/virtual-machines-workload-intranet-sharepoint-phase1/workload-spsqlao_01.png)

## Paso siguiente

Para continuar con la configuración de esta carga de trabajo, vaya a [Fase 2: Configuración de los controladores de dominio](virtual-machines-workload-intranet-sharepoint-phase2.md).

## Recursos adicionales

[Implementación de SharePoint con grupos de disponibilidad AlwaysOn de SQL Server en Azure](virtual-machines-workload-intranet-sharepoint-overview.md)

[Granjas de servidores de SharePoint hospedadas en servicios de infraestructura de Azure](virtual-machines-sharepoint-infrastructure-services.md)

[Infografía de SharePoint con AlwaysOn de SQL Server](http://go.microsoft.com/fwlink/?LinkId=394788)

[Arquitecturas de Microsoft Azure para SharePoint 2013](https://technet.microsoft.com/library/dn635309.aspx)

[Directrices de implementación de los servicios de infraestructura de Azure](virtual-machines-infrastructure-services-implementation-guidelines.md)

[Carga de trabajo de servicios de infraestructura de Azure: aplicación de línea de negocio de alta disponibilidad](virtual-machines-workload-high-availability-lob-application.md)

<!---HONumber=Sept15_HO3-->