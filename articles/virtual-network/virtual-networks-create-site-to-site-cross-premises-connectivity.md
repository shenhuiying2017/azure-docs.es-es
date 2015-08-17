<properties 
	pageTitle="Tutorial: Creación de una red virtual entre locales para una conectividad sitio a sitio" 
	description="Obtenga información acerca de cómo crear una red virtual de Azure con conectividad entre locales en este tutorial." 
	services="virtual-network" 
	documentationCenter="" 
	authors="cherylmc" 
	manager="adinah" 
	editor="tysonn"/>

<tags 
	ms.service="virtual-network" 
	ms.workload="infrastructure-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="04/28/2015" 
	ms.author="cherylmc"/>



# Tutorial: Creación de una red virtual entre locales para una conectividad sitio a sitio

En este tutorial se muestran los pasos que debe seguir para crear, por ejemplo, una red virtual entre locales con conexión sitio a sitio.

Si desea crear una red virtual solo en la nube, consulte [Tutorial: Creación de una red virtual solo en la nube en Azure](../virtual-machines/create-virtual-network.md). Si desea crear una VPN de punto a sitio utilizando certificados y un cliente VPN, consulte [Configurar una VPN de punto a sitio con el asistente del Portal de administración](http://go.microsoft.com/fwlink/p/?LinkId=296653).

En este tutorial se asume que no tiene ninguna experiencia previa con Azure. La finalidad del mismo es ayudarle a familiarizarse con los pasos necesarios para crear una red virtual entre locales de ejemplo. Si busca escenarios de diseño e información avanzada sobre redes virtuales, consulte [Información general de la red virtual de Azure](http://msdn.microsoft.com/library/windowsazure/jj156007.aspx).

Después de completar este tutorial, tendrá una red virtual entre locales de ejemplo. La siguiente ilustración muestra los detalles, según la configuración de ejemplo de este tutorial.

![](./media/virtual-networks-create-site-to-site-cross-premises-connectivity/CreateCrossVnet_12_TutorialCrossPremVNet.png)

Para obtener una copia de esta ilustración y una que pueda usar para representar su propia red virtual entre locales, consulte [Ejemplo de la red virtual entre locales de ejemplo del tema del tutorial](http://gallery.technet.microsoft.com/Example-cross-premises-e5ecb8bb).

Tenga en cuenta que los parámetros de configuración utilizados en este tutorial no se han personalizado para la red de su organización. Si configura la red virtual y la conexión sitio a sitio con los parámetros de configuración utilizados a modo de ejemplo en este tema, no funcionará. Para configurar una red virtual entre locales que funcione, deberá trabajar con su departamento informático y con su administrador de red para obtener la configuración correcta. Para obtener más información, consulte la sección **Requisitos previos** de este tema:

Para obtener información acerca de cómo agregar una máquina virtual y ampliar su dominio local de Active Directory a Red virtual de Azure, consulte los siguientes documentos:

-  [Cómo crear de manera personalizada una máquina virtual](http://go.microsoft.com/fwlink/p/?LinkID=294356)

-  [Instalación de una réplica de controlador de dominio de Active Directory en Red virtual de Azure](http://go.microsoft.com/fwlink/p/?LinkId=299877)

Para obtener directrices acerca de la implementación de AD DS en máquinas virtuales de Azure, consulte [Directrices para implementar Windows Server Active Directory en máquinas virtuales de Azure](http://msdn.microsoft.com/library/windowsazure/jj156090.aspx).

Para obtener información acerca de procedimientos y configuración de la red virtual, consulte [Tareas de configuración de Red virtual de Azure](http://go.microsoft.com/fwlink/p/?LinkId=296652).

##  Objetivos

En este tutorial, aprenderá a:

-  Configurar un ejemplo de red virtual de Azure entre locales a la que podrá agregar servicios de Azure.

-  Configurar la red virtual para comunicarse con la red de una organización.

##  Requisitos previos

-  Una cuenta de Microsoft con al menos una suscripción válida y activa a Azure. Si todavía no dispone de una suscripción a Azure, puede registrarse para obtener una evaluación gratuita en [Probar Azure](http://azure.microsoft.com/pricing/free-trial/). Si tiene una suscripción de MSDN, consulte [Precios especiales de Microsoft Azure: Ventajas de MSDN, MPN y Bizspark](http://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/).

Si utiliza este tutorial para configurar una red virtual entre locales que funciones y que esté personalizada para su organización, necesitará lo siguiente:

-  Los espacios de direcciones de IPv4 privado (en notación CIDR) para la red virtual y sus subredes.

-  Nombre y dirección IP de un servidor DNS local.

-  Dispositivo VPN con una dirección IPv4 pública. Necesitará la dirección IP para completar el asistente. El dispositivo VPN no puede estar situado detrás de un traductor de direcciones de red (NAT) y debe cumplir los requisitos mínimos de los dispositivos. Consulte [Acerca de los dispositivos VPN de la red virtual](http://go.microsoft.com/fwlink/p/?LinkID=248098) para obtener más información.

	Nota: puede utilizar el servicio de enrutamiento y acceso remoto (RRAS) en un Windows Server como parte de la solución de VPN. Sin embargo, en este tutorial no se incluyen los pasos de configuración de RRAS.
	Para obtener información acerca de la configuración de RRAS, consulte [Plantillas del Servicio de Enrutamiento y acceso remoto](http://msdn.microsoft.com/library/windowsazure/dn133801.aspx).

-  Conocimientos para configurar un enrutador para una conexión en modo túnel de IPsec, o bien alguien que le pueda ayudar con este paso.

-  El conjunto de espacios de direcciones (en notación CIDR) que resume las ubicaciones alcanzables de su red local.


## Pasos de alto nivel

1.	[Creación de una red virtual](#CreateVN)

2.	[Inicio de la puerta de enlace y recopilación de información para el administrador de red](#StartGateway)

3.  [Configuración del dispositivo VPN](#ConfigVPN)

##  <a name="CreateVN">Creación de una red virtual</a>

Para crear un ejemplo de red virtual que se conecte a la red de una empresa:

1.	Inicie sesión en el [Portal de administración de Azure](http://manage.windowsazure.com/).

2.	En la esquina inferior izquierda de la pantalla, haga clic en **Nuevo**. En el panel de navegación, haga clic en **Redes** y, a continuación, en **Red virtual**. Haga clic en **Creación personalizada** para iniciar el Asistente para configuración.

	![](./media/virtual-networks-create-site-to-site-cross-premises-connectivity/CreateCrossVnet_01_OpenVirtualNetworkWizard.png)

3.	En la página **Detalles de la red virtual**, escriba la información siguiente y, a continuación, haga clic en la flecha siguiente situada en la parte inferior derecha. Para obtener más información acerca de la configuración de la página de detalles, consulte la sección **Página Detalles de red virtual** del documento [Acerca de la configuración de una red virtual en el Portal de administración](http://go.microsoft.com/fwlink/p/?LinkID=248092).

	-  **Nombre**: Nombre de la red virtual. Por ejemplo, en este tutorial escriba **YourVirtualNetwork**.

	-  **Ubicación:** En la lista desplegable, seleccione la región deseada. La red virtual se creará en un centro de datos de Azure ubicado en la región especificada.

	
4.	En la página **Servidores DNS y conectividad VPN**, escriba la información siguiente y, a continuación, haga clic en la flecha para avanzar situada en la esquina inferior derecha.

> [AZURE.NOTE] Es posible seleccionar a la vez las configuraciones **De punto a sitio** y **De sitio a sitio** de esta página. En este tutorial, seleccionaremos únicamente la configuración **De sitio a sitio**. Para obtener más información acerca de la configuración de esta página, consulte la página **Servidores DNS y conectividad VPN** en [Acerca de la configuración de una red virtual en el Portal de administración](http://go.microsoft.com/fwlink/p/?LinkID=248092).

	-  **DNS SERVERS:** Enter the DNS server name and IP address that you want to use for name resolution. Typically this would be a DNS server that you use for on-premises name resolution. This setting does not create a DNS server. For the example in this tutorial, type **YourDNS** for the name and **10.1.0.4** for the IP address.
	-  **Configure Point-To-Site VPN:** Leave this field blank. 
	-  **Configure Site-To-Site VPN:** Select checkbox.
	-  **LOCAL NETWORK:** Select **Specify a New Local Network** from the drop-down list.
 
	![](./media/virtual-networks-create-site-to-site-cross-premises-connectivity/CreateCrossVNet_03_DNSServersandVPNConnectivity.png)

5.	En la página **Conectividad de sitio a sitio**, escriba la información siguiente y, a continuación, haga clic en la marca de verificación que aparece en la esquina inferior derecha de la página. Para obtener más información acerca de la configuración de esta página, consulte la sección **Página Conectividad de sitio a sitio** del documento [Acerca de la configuración de una red virtual en el Portal de administración](http://go.microsoft.com/fwlink/p/?LinkID=248092). 

	-  **NOMBRE:** Para el ejemplo de este tutorial, escriba **YourCorpHQ**.

	-  **DIRECCIÓN IP DEL DISPOSITIVO VPN:** Para el ejemplo de este tutorial, escriba **3.2.1.1**. De lo contrario, escriba la dirección IP pública del dispositivo VPN. Si no dispone de esta información, deberá obtenerla antes de realizar los pasos siguientes del asistente. Tenga en cuenta que el dispositivo VPN no se puede encontrar detrás de un NAT. Para obtener más información acerca de los dispositivos VPN, consulte [Acerca de los dispositivos VPN de la red virtual](http://msdn.microsoft.com/library/windowsazure/jj156075.aspx).

	-  **ESPACIO DE DIRECCIONES**: Para el ejemplo de este tutorial, escriba **10.1.0.0/16**.
	-  **Agregar espacio de direcciones:** Este tutorial no requiere espacio de direcciones adicional.

	![](./media/virtual-networks-create-site-to-site-cross-premises-connectivity/CreateCrossVnet_04_SitetoSite.png)

6.  En la página **Espacios de direcciones de red virtual**, escriba la información siguiente y, a continuación, haga clic en la marca de verificación que aparece en la esquina inferior derecha para configurar la red.

	El espacio de direcciones debe ser un rango de direcciones privadas, con la notación CIDR desde los espacios de direcciones 10.0.0.0/8, 172.16.0.0/12 o 192.168.0.0/16 (de conformidad con el documento RFC 1918). Para obtener más información acerca de la configuración de esta página, consulte la **página Espacios de direcciones de la red virtual** del documento [Acerca de la configuración de una red virtual en el Portal de administración](http://go.microsoft.com/fwlink/?LinkID=248092).

	-  **Espacio de direcciones:** Para el ejemplo de este tutorial, haga clic en **CIDR** en la esquina superior derecha y, a continuación, escriba lo siguiente:
		-  **IP inicial:** 10.4.0.0
		-  **CIDR:** /16
	-  **Agregar subred:** Para el ejemplo de este tutorial, escriba lo siguiente:
		-  Cambie el nombre de **Subnet-1** por **FrontEndSubnet** con la dirección IP inicial **10.4.2.0/24**.
		-  Agregue una subred llamada **BackEndSubnet** con la dirección IP inicial **10.4.3.0/24**.
		-  Agregue una subred llamada **ADDNSSubnet** con la dirección IP inicial **10.4.4.0/24**.
		-  Agregue una subred de puerta de enlace con la dirección IP inicial **10.4.1.0/24**.
	-  Por ejemplo en este tutorial, compruebe que ahora tenga tres subredes y una subred de puerta de enlace y, a continuación, haga clic en la marca de verificación situada en la esquina inferior derecha para crear la red virtual.

	![](./media/virtual-networks-create-site-to-site-cross-premises-connectivity/CreateCrossVnet_05_VirtualNetworkAddressSpaces.png)

7.	Al hacer clic en la marca de verificación, la red virtual empezará a crearse. Cuando se haya creado la red virtual, verá **Creada** bajo **Estado** en la página Redes del Portal de administración.

	![](./media/virtual-networks-create-site-to-site-cross-premises-connectivity/CreateCrossVNet_06_VirtualNetworkCreatedStatus.png)

##  <a name="StartGateway">Inicio de la puerta de enlace</a>

Después de crear la red virtual de Azure, siga este procedimiento para configurar la puerta de enlace de la red virtual a fin de crear la VPN de sitio a sitio. Para este procedimiento necesita un dispositivo VPN que cumpla los requisitos mínimos. Para obtener más información acerca de los dispositivos VPN y su configuración, consulte [Acerca de los dispositivos VPN de la red virtual](http://go.microsoft.com/fwlink/p/?LinkID=248098).

**Para iniciar la puerta de enlace:**

1.	Cuando se cree la red virtual, en la página **Redes**, esta presentará el estado **Creada**.

	En la columna **NOMBRE**, haga clic en **YourVirtualNetwork** (en el ejemplo creado en este tutorial) para abrir el panel.
 
	![](./media/virtual-networks-create-site-to-site-cross-premises-connectivity/CreateCrossVNet_07_ClickYourVirtualNetwork.png)

2.	Haga clic en **PANEL** en la parte superior de la página. En la parte inferior de la página Panel, haga clic en **CREAR PUERTA DE ENLACE**. Seleccione **Enrutamiento dinámico** o **Enrutamiento estático** como tipo de puerta de enlace según sus preferencias.

	Tenga en cuenta que, si desea utilizar esta red virtual para conexiones punto a sitio además de sitio a sitio, debe seleccionar **Enrutamiento dinámico** como tipo de puerta de enlace. Antes de crear la puerta de enlace, compruebe que el dispositivo VPN sea compatible con el tipo de puerta de enlace que desea crear. Consulte [Acerca de los dispositivos VPN de la red virtual](http://go.microsoft.com/fwlink/p/?LinkID=248098). Cuando el sistema le pida confirmación de que desea crear la puerta de enlace, haga clic en **SÍ**.

	![](./media/virtual-networks-create-site-to-site-cross-premises-connectivity/CreateCrossVnet_08_CreateGateway.png)

3.	Cuando comience el proceso de creación, verá un mensaje informándole de que la puerta de enlace ya se inició.

	La puerta de enlace puede tardar hasta 15 minutos en crearse.

4.	Una vez que se cree la puerta de enlace, deberá recopilar los siguientes datos, que se utilizarán para configurar el dispositivo VPN.

	-  Dirección IP de la puerta de enlace
	-  Clave compartida
	-  Plantilla de script de configuración de dispositivo VPN

	Los siguientes pasos le muestran cómo realizar este proceso.

5.	Para localizar la dirección IP de la puerta de enlace: La dirección IP de la puerta de enlace está situada en la página **PANEL** de la red virtual. Aquí tiene un ejemplo:

	![](./media/virtual-networks-create-site-to-site-cross-premises-connectivity/CreateCrossVnet_09_GatewayIP.png)

6.	Para obtener una clave compartida: La clave compartida está situada en la página **PANEL** de la red virtual. Haga clic en **Administrar clave** en la parte inferior de la pantalla y, a continuación, copie la clave que aparece en el cuadro de diálogo. Necesitará esta clave para configurar el túnel de IPsec en el dispositivo de VPN de la empresa.

	![](./media/virtual-networks-create-site-to-site-cross-premises-connectivity/CreateCrossVNet_10_ManageSharedKey.png)

7.	Para descargar la plantilla de secuencia de comandos de configuración de dispositivo VPN: En el panel, haga clic en **Descargar script de dispositivo VPN**.

8.	En el cuadro de diálogo **Descargar el script de configuración de un dispositivo VPN**, seleccione el proveedor, la plataforma y el sistema operativo del dispositivo VPN de la empresa. Haga clic en el botón de marca de verificación y guarde el archivo.

	![](./media/virtual-networks-create-site-to-site-cross-premises-connectivity/CreateCrossVnet_11_DownloadVPNDeviceScript.png)

Si no encuentra el dispositivo VPN en la lista desplegable, consulte [Acerca de los dispositivos VPN de la red virtual](http://go.microsoft.com/fwlink/p/?LinkID=248098) en MSDN Library para obtener plantillas de script adicionales.


##  <a name="ConfigVPN">Configuración del dispositivo VPN (administrador de red)</a>

Dado que cada dispositivo VPN es diferente, este procedimiento es de alto nivel y solo debe realizarlo el administrador de red.

Puede obtener el script de configuración de VPN en el Portal de administración o en el documento [Acerca de los dispositivos VPN de la red virtual](http://go.microsoft.com/fwlink/p/?LinkId=248098), que también incluye información sobre los tipos de enrutamiento y los dispositivos que son compatibles con la configuración de enrutamiento seleccionada.

Para obtener información adicional acerca de la configuración de una puerta de enlace de red virtual, consulte [Configurar una puerta de enlace de red virtual en el Portal de administración](http://go.microsoft.com/fwlink/p/?LinkId=299878), así como la documentación del dispositivo VPN.

En este procedimiento se asume lo siguiente:

-  La persona que va a configurar el dispositivo VPN está capacitada para ello. Debido al número de dispositivos compatibles con la red virtual y a las configuraciones específicas de cada familia de dispositivos, estos pasos no constituyen una guía detallada de la configuración del dispositivo. Por lo tanto, es importante que la persona encargada de configurar el dispositivo esté familiarizada tanto con el dispositivo como con su configuración. 

-  El dispositivo que seleccionó es compatible con la red virtual. Consulte [aquí](http://go.microsoft.com/fwlink/p/?LinkID=248098) la información de compatibilidad de los dispositivos.


**Para configurar el dispositivo VPN:**

1.	Modifique el script de configuración de VPN. Configurará los siguientes parámetros:

	a. Directivas de seguridad

	b. Túnel de entrada

	c. Túnel de salida

2.	Ejecute el script de configuración de VPN modificado para configurar el dispositivo VPN.

3.	Pruebe la conexión ejecutando uno de los comandos siguientes:

	<table border="1">
	<tr>
	<th>-</th>
	<th>Cisco ASA</th>
	<th>Cisco ISR/ASR</th>
	<th>Juniper SSG/ISG</th>
	<th>Juniper SRX/J</th>
	</tr>
	
	<tr>
	<td><b>SA de comprobación de modo principal</b></td>
	<td><FONT FACE="courier" SIZE="-1">show crypto isakmp sa</FONT></td>
	<td><FONT FACE="courier" SIZE="-1">show crypto isakmp sa</FONT></td>
	<td><FONT FACE="courier" SIZE="-1">get ike cookie</FONT></td>
	<td><FONT FACE="courier" SIZE="-1">show security ike security-association</FONT></td>
	</tr>
	
	<tr>
	<td><b>SA de comprobación de modo rápido</b></td>
	<td><FONT FACE="courier" SIZE="-1">show crypto ipsec sa</FONT></td>
	<td><FONT FACE="courier" SIZE="-1">show crypto ipsec sa</FONT></td>
	<td><FONT FACE="courier" SIZE="-1">get sa</FONT></td>
	<td><FONT FACE="courier" SIZE="-1">show security ipsec security-association</FONT></td>
	</tr>
	</table>


##  Pasos siguientes
Para ampliar el dominio local de Active Directory a la red virtual que acaba de crear, realice los siguientes tutoriales:

-  [Cómo crear de manera personalizada una máquina virtual](http://go.microsoft.com/fwlink/p/?LinkID=294356)

-  [Instalación de una réplica de controlador de dominio de Active Directory en Red virtual de Azure](http://go.microsoft.com/fwlink/p/?LinkId=299877)

Si desea exportar la configuración de la red virtual a un archivo de configuración de red para guardarlo como copia de seguridad o utilizarlo como plantilla, consulte [Exportar la configuración de red virtual a un archivo de configuración de red](http://go.microsoft.com/fwlink/p/?LinkID=299880).

## Otras referencias

-  [Introducción técnica de la red virtual de Azure](http://msdn.microsoft.com/library/windowsazure/jj156007.aspx)

-  [Preguntas frecuentes sobre redes virtuales](http://msdn.microsoft.com/library/windowsazure/dn133803.aspx)

-  [Configuración de una red virtual con un archivo de configuración de red](virtual-networks-using-network-configuration-file.md)

-  [Agregar una máquina virtual a una red virtual](../virtual-machines/virtual-machines-create-custom.md)

-  [Acerca de los dispositivos VPN de la red virtual](http://msdn.microsoft.com/library/windowsazure/jj15] 75.aspx)

-  [Resolución de nombres para las máquinas virtuales e instancias de rol](http://go.microsoft.com/fwlink/p/?LinkId=248097)
-  [Configuración de un entorno de nube híbrida para pruebas](virtual-networks-setup-hybrid-cloud-environment-testing.md)





 

<!---HONumber=August15_HO6-->