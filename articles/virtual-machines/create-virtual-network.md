<properties 
	pageTitle="Tutorial: Creación de una red virtual solo en la nube" 
	description="Obtenga información acerca de cómo crear una red virtual de Azure solo en la nube de ejemplo en este tutorial." 
	services="virtual-machines, virtual-network" 
	documentationCenter="" 
	authors="cherylmc" 
	manager="adinah" 
	editor=""/>

<tags 
	ms.service="virtual-network" 
	ms.workload="infrastructure-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="05/18/2015" 
	ms.author="cherylmc"/>

# Tutorial: Creación de una red virtual solo en la nube en Azure

Este tutorial le guiará paso a paso en el Portal de administración de Azure para crear una red virtual de Azure solo en la nube de ejemplo, con dos subredes. La red virtual resultante tendrá el aspecto siguiente:

![createvnet](./media/create-virtual-network/createVNet_06_VNetExample.png)

Por ejemplo, el objeto FrontEndSubnet se puede usar para servidores web y el objeto BackEndSubnet se puede usar para los servidores SQL o controladores de dominio.

En este tutorial se asume que no tiene ninguna experiencia previa con Azure. La descripción de esta configuración de ejemplo se ha creado de modo que pueda familiarizarse con los pasos que se requieren para crear una red virtual propia. Si desea crear una red virtual solo en la nube que funcione para su configuración específica, consulte [Configurar una red virtual solo en la nube en el Portal de administración](http://msdn.microsoft.com/library/azure/dn631643.aspx). Si busca escenarios de diseño e información avanzada sobre redes virtuales, consulte [Información general de la red virtual de Azure](http://msdn.microsoft.com/library/windowsazure/jj156007.aspx).


> [AZURE.NOTE]Este tutorial no le guiará en la creación de la configuración entre ubicaciones en la que la red virtual está conectada a la red de la organización. Si desea consultar un tutorial que le guíe en la creación de una red virtual con conectividad entre locales y una conexión VPN de sitio a sitio (por ejemplo, conectividad con Active Directory o con SharePoint ubicados en su compañía), consulte [Tutorial: Creación de una red virtual entre locales para conectividad de sitio a sitio](../virtual-network/virtual-networks-create-site-to-site-cross-premises-connectivity.md).


##  Objetivos

En este tutorial, aprenderá a configurar una red virtual de Azure solo en la nube con dos subredes.

##  Requisitos previos

*  Una cuenta de Microsoft con al menos una suscripción válida y activa a Azure. Si todavía no dispone de una suscripción a Azure, puede registrarse para obtener una evaluación gratuita en [Probar Azure](http://azure.microsoft.com/pricing/free-trial/). Si tiene una suscripción de MSDN, consulte [Precios especiales de Microsoft Azure: Ventajas de MSDN, MPN y Bizspark](http://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/).

##  Creación de la red virtual para este tutorial

Para crear esta red virtual de ejemplo solo en la nube, haga lo siguiente:

1. Inicie sesión en el Portal de administración.

2. En la esquina inferior izquierda de la pantalla, haga clic en **Nuevo** > **Servicios de red** > **Red virtual** y luego en **Creación personalizada** para iniciar el asistente para la configuración.

	![][Image1]

3. En la página **Detalles de la red virtual**, escriba la siguiente información:

- **Nombre**: escriba **YourVirtualNetwork**.

- **Región**: la red virtual se creará en un centro de datos ubicado en la región especificada. Para conseguir un rendimiento óptimo, en la lista desplegable seleccione la región en la que se encuentra.
 

	![][Image2]

4. Haga clic en la flecha de avance en la esquina inferior derecha. Para obtener más información acerca de la configuración de esta página, consulte la sección de la página Detalles de red virtual del documento [Acerca de la configuración de una red virtual en el Portal de administración](http://go.microsoft.com/fwlink/p/?linkid=248092&clcid=0x409).

5. En la página **Servidores DNS y conectividad VPN**, haga clic en la flecha siguiente situada en la parte inferior derecha. Azure asignará un servidor DNS de Azure basado en Internet a las nuevas máquinas virtuales que se agreguen a esta red virtual, lo que les dará acceso a los recursos de Internet. Para obtener más información acerca de la configuración de esta página, consulte la página Servidores DNS y conectividad VPN en [Acerca de la configuración de una red virtual en el Portal de administración](http://go.microsoft.com/fwlink/p/?linkid=248092&clcid=0x409).
	
6.	Al igual que una red real, la red virtual necesita un intervalo de direcciones IP (conocido como espacio de direcciones) que se asignan a las máquinas virtuales que coloque en ella. La red virtual también admite subredes, que necesitan sus propios espacios de direcciones que se obtienen del espacio de direcciones de la red virtual. Para este tutorial, se crearán las subredes BackEndSUbnet y SubredFrontEnd. En la página **Espacios de direcciones de la red virtual**, configure los valores siguientes:

	- Para Espacio de direcciones, seleccione **/16 (65535)** en **CIDR (RECUENTO DE DIRECCIONES)**.

	- Para las subredes, en la primera fila, escriba **BackEndSubnet** sobre el nombre existente y **10.0.1.0** como IP inicial. A continuación, seleccione **/24 (256)** en **CIDR (RECUENTO DE DIRECCIONES)**. Haga clic en **Agregar subred** y escriba **FrontEndSubnet** como nombre y **10.0.2.0** como IP inicial.
		
	![][Image4]

 Vuelva al diagrama de la red virtual y observará que ha configurado los siguientes espacios de direcciones:
 
	
- FrontEndSubnet: 10.0.2.0/24
- BackEndSubnet: 10.0.1.0/24

 Para obtener más información acerca de la configuración de esta página, consulte la página Espacios de direcciones de la red virtual del documento [Acerca de la configuración de una red virtual en el Portal de administración](http://go.microsoft.com/fwlink/p/?linkid=248092&clcid=0x409).


7. Haga clic en la marca de verificación de la parte inferior derecha de la página y se empezará a crear la red virtual. Cuando se haya creado la red virtual, en la sección Estado de la página **Redes** del Portal de administración de Azure verá **Creado**.  

	![][Image5]

Para seguir aprendiendo sobre los servicios de infraestructura de Azure, consulte lo siguiente:

- [Creación de una máquina virtual personalizada](virtual-machines-create-custom.md): use este tema para instalar una máquina virtual en su red virtual. Para obtener más información acerca de las máquinas virtuales y las opciones de instalación, consulte [Máquinas virtuales Azure](http://azure.microsoft.com/documentation/services/virtual-machines/).

- [Instalación de un nuevo bosque de Active Directory en una red virtual de Azure](../active-directory-new-forest-virtual-machine.md): use este tema para instalar un nuevo bosque de Windows Server Active Directory sin conectividad a ninguna otra red. El tutorial explica los pasos específicos requeridos para crear una máquina virtual (VM) para una nueva instalación de bosque. Si piensa utilizar este tutorial, no cree ninguna máquina virtual mediante el Portal de administración. Para obtener más información, consulte [Instrucciones para implementar Active Directory de Windows Server en máquinas virtuales Azure](http://msdn.microsoft.com/library/windowsazure/jj156090.aspx).

Para quitar esta red virtual, selecciónela, haga clic en **Eliminar** y luego en **Sí**.

Cuando esté listo para crear una red virtual solo en la nube que funcione para su configuración específica, consulte [Configurar una red virtual solo en la nube en el Portal de administración](http://msdn.microsoft.com/library/azure/dn631643.aspx).

Si busca escenarios de diseño e información avanzada sobre redes virtuales, consulte [Información general de Red virtual de Azure](http://msdn.microsoft.com/library/windowsazure/jj156007.aspx).

Para obtener información acerca de procedimientos y configuración de la red virtual, consulte [Tareas de configuración de Red virtual de Azure](http://go.microsoft.com/fwlink/p/?linkid=296652&clcid=0x409).


## Otras referencias

-  [P+F de Red virtual de Azure](http://go.microsoft.com/fwlink/p/?LinkId=296650)

-  [Tareas de configuración de Red virtual de Azure](http://go.microsoft.com/fwlink/p/?LinkId=296652)

-  [Configuración de una red virtual con un archivo de configuración de red](../virtual-network/virtual-networks-using-network-configuration-file.md)

-  [Resolución de nombres para las máquinas virtuales e instancias de rol](http://go.microsoft.com/fwlink/?LinkId=248097)


[Image1]: ./media/create-virtual-network/createVNet_01_OpenVirtualNetworkWizard.png
[Image2]: ./media/create-virtual-network/createVNet_02_VirtualNetworkDetails.png
[Image3]: ./media/create-virtual-network/createVNet_03_DNSServersandVPNConnectivity.png
[Image4]: ./media/create-virtual-network/createVNet_04_VirtualNetworkAddressSpaces.png
[Image5]: ./media/create-virtual-network/createVNet_05_VirtualNetworkCreatedStatus.png
[Image7]: ./media/create-virtual-network/createVNet_07_VNetExampleSpaces.png
[Image8]: ./media/create-virtual-network/createVNet_07_VNetExampleSpaces.png

 

<!---HONumber=July15_HO3-->