<properties  linkid="manage-services-cross-premises-connectivity" urlDisplayName="Cross-premises Connectivity" pageTitle="Create a cross-premises virtual network - Azure" metaKeywords="" description="Learn how to create an Azure Virtual Network with cross-premises connectivity." metaCanonical="" services="virtual-network" documentationCenter="" title="Create a Virtual Network for Site-to-Site Cross-Premises Connectivity" authors="" solutions="" manager="" editor="" />

<h1>Creación de una red virtual para una conectividad sitio a sitio entre locales</h1>   


En este tutorial se muestran los pasos que debe seguir para crear una red virtual entre locales. El tipo de conexión que crearemos es una conexión sitio a sitio. Si desea crear una VPN de punto a sitio utilizando certificados y un cliente VPN, consulte [Configurar una VPN de punto a sitio con el asistente del Portal de administración][1].

En este tutorial se asume que no tiene ninguna experiencia previa con Azure. La finalidad del mismo es ayudarle a familiarizarse con los pasos necesarios para crear una red virtual de sitio a sitio. Si busca escenarios de diseño e información avanzada sobre redes virtuales, consulte la [información general sobre Red virtual de Azure][2].

Cuando finalice este tutorial, dispondrá de una red virtual en la que poder implementar los servicios y las máquinas virtuales de Azure, que después podrán comunicarse directamente con la red de su empresa.

Para obtener información acerca de cómo agregar una máquina virtual y ampliar su dominio local de Active Directory a Red virtual de Azure, consulte los siguientes documentos:

* [How to Create a Custom Virtual Machine][3]

* [Instalación de una réplica de controlador de dominio de Active
  Directory en Red virtual de Azure][4]

Para obtener directrices acerca de la implementación de AD DS en máquinas virtuales de Azure, consulte [Directrices para implementar Windows Server Active Directory en máquinas virtuales de Azure][5].

Para obtener información acerca de procedimientos y configuración de la red virtual, consulte [Tareas de configuración de Red virtual de Azure][6].

## Objetivos

En este tutorial, aprenderá a:

* Configurar una red virtual básica de Azure a la que podrá agregar servicios de Azure.

* Configurar la red virtual para comunicarse con la red de su empresa.

## Requisitos previos

* Una cuenta de Windows Live con al menos una suscripción válida y activa.

* Espacio de direcciones (en notación CIDR) que se utilizará para la red virtual y las subredes.

* Nombre y dirección IP del servidor DNS (si desea utilizar su servidor DNS local para la resolución de nombres).

* Dispositivo VPN con una dirección IPv4 pública. Necesitará la dirección IP para completar el asistente. El dispositivo VPN no puede estar situado detrás de un NAT y debe cumplir los requisitos mínimos de los dispositivos. Consulte [Acerca de los dispositivos VPN de la red virtual][7] para obtener más información.
  
  Nota: puede utilizar RRAS como parte de la solución de VPN. Sin embargo, en este tutorial no se incluyen los pasos de configuración de RRAS.
  
  Para obtener información acerca de la configuración de RRAS, consulte [Plantillas del Servicio de Enrutamiento y acceso remoto][8].

* Conocimientos para configurar un enrutador, o bien conocer a alguien que le pueda ayudar con este paso.

* Espacio de direcciones para la red local.

## Pasos de alto nivel

1.  [Creación de una red virtual](#CreateVN)

2.  [Inicio de la puerta de enlace y recopilación de información para el administrador de red](#StartGateway)

3.  [Configuración del dispositivo VPN](#ConfigVPN)

## <a name="CreateVN">Creación de una red virtual</a>

**Para crear una red virtual que se conecte a la red de su empresa:**

1.  Inicie sesión en el [Portal de administración de Azure][9].

2.  En la esquina inferior izquierda de la pantalla, haga clic en **New**. En el panel de navegación, haga clic en **Redes** y, a continuación, en **Red virtual**. Haga clic en **Custom Create** para iniciar el asistente de configuración.
    
    ![](./media/virtual-networks-create-site-to-site-cross-premises-connectivity/CreateCrossVnet_01_OpenVirtualNetworkWizard.png)

3.  En la página **Virtual Network Details**, escriba la información siguiente y, a continuación, haga clic en la flecha para avanzar situada en la esquina inferior derecha. Para obtener más información acerca de la configuración de la página de detalles, consulte la sección **Página Detalles de red virtual** del documento [Acerca de la configuración de una red virtual en el Portal de administración][10].

* **NAME:** Asigne un nombre a la red virtual. Escriba *YourVirtualNetwork*.

* **GRUPO DE AFINIDAD:** En la lista desplegable, seleccione **Crear un nuevo grupo de afinidad**. Con los grupos de afinidad podrá agrupar físicamente servicios de Azure en el mismo centro de datos para aumentar el rendimiento. Solo se puede asignar una red virtual a un grupo de afinidad.

* **REGIÓN:** En la lista desplegable, seleccione la región que desee. La red virtual se creará en un centro de datos ubicado en la región especificada.

* **NOMBRE DEL GRUPO DE AFINIDAD:** Asigne un nombre al nuevo grupo de afinidad. Escriba *YourAffinityGroup*.
  
	![](./media/virtual-networks-create-site-to-site-cross-premises-connectivity/CreateCrossVnet_02_VirtualNetworkDetails.png)

1.  En la página **DNS Servers and VPN Connectivity**, escriba la información siguiente y, a continuación, haga clic en la flecha para avanzar situada en la esquina inferior derecha.
    
    <div  class="dev-callout" markdown="1">

	<b>Nota:</b>
	<p>Es posible seleccionar a la vez las configuraciones "Point-To-Site" y "Site-To-Site" de esta página. En este tutorial, seleccionaremos únicamente la configuración \*\*Site-To-Site\*\*. Para obtener más información acerca de la configuración de esta página, consulte la página "Servidores DNS y conectividad VPN" del documento <a href="http://go.microsoft.com/fwlink/?LinkID=248092">Acerca de la configuración de una red virtual en el Portal de administración</a>.

	</p>
	</div>


* **DNS SERVERS:** Escriba el nombre y la dirección IP del servidor DNS que desee usar para la resolución de nombres. Normalmente se trata de un servidor DNS utilizado para la resolución de nombres local. Mediante este valor no se crea un servidor DNS. Escriba el nombre *YourDNS* y la dirección IP *10.1.0.4*.
* **Configure Point-To-Site VPN:** Deje este campo en blanco.
* **Configure Site-To-Site VPN:** Active la casilla.
* **LOCAL NETWORK:** Seleccione **Specify a New Local Network** en la lista desplegable.
  
	![](./media/virtual-networks-create-site-to-site-cross-premises-connectivity/CreateCrossVNet_03_DNSServersandVPNConnectivity.png)

1.  En la página **Site-To-Site Connectivity**, escriba la información siguiente y, a continuación, haga clic en la marca de verificación que aparece en la esquina inferior derecha de la página. Para obtener más información acerca de la configuración de esta página, consulte la sección **Página Conectividad de sitio a sitio** del documento [Acerca de la configuración de una red virtual en el Portal de administración][10].

* **NAME:** Escriba *YourCorpHQ*.

* **VPN DEVICE IP ADDRESS:** Escriba la dirección IP pública del dispositivo VPN. Si no dispone de esta información, deberá obtenerla antes de realizar los pasos siguientes del asistente. Tenga en cuenta que el dispositivo VPN no puede encontrarse detrás de un NAT. Para obtener más información acerca de los dispositivos de VPN, consulte [Acerca de los dispositivos VPN de la red virtual][11].

* **ADDRESS SPACE:** Escriba *10.1.0.0/16*.
* **Add address space:** Este tutorial no requiere espacio de direcciones adicional.
  
	![](./media/virtual-networks-create-site-to-site-cross-premises-connectivity/CreateCrossVnet_04_SitetoSite.png)

1.  En la página **Virtual Network Address Spaces**, escriba la información siguiente y, a continuación, haga clic en la marca de verificación que aparece en la esquina inferior derecha para configurar la red.
    
    El espacio de direcciones debe ser un rango de direcciones privadas, con la notación CIDR 10.0.0.0/8, 172.16.0.0/12 o 192.168.0.0/16 (de conformidad con el documento RFC 1918). Para obtener más información acerca de la configuración de esta página, consulte la página **Espacios de direcciones de la red virtual** del documento [Acerca de la configuración de una red virtual en el Portal de administración][10].

* **Address Space:** Haga clic en **CIDR** en la esquina superior derecha y, a continuación, escriba lo siguiente:
  * **Starting IP:** 10.4.0.0
  * **CIDR:** /16

* **Add subnet:** Realice estos pasos:
  * **Cambie el nombre de la subred 1** por *FrontEndSubnet* con la dirección IP inicial *10.4.2.0/24* y, a continuación, haga clic en **add subnet**.
  * **Agregue una subred** llamada *BackEndSubnet* con la dirección IP inicial *10.4.3.0/24*.
  * **Agregue una subred** llamada *ADDNSSubnet* con la dirección IP inicial *10.4.4.0/24*.
  * **Agregue una subred de puerta de enlace** con la dirección IP inicial *10.4.1.0/24*.
  * **Compruebe** que ahora tenga tres subredes y una subred de puerta de enlace y, a continuación, haga clic en la marca de verificación situada en la esquina inferior derecha para crear la red virtual.
  
	![](./media/virtual-networks-create-site-to-site-cross-premises-connectivity/CreateCrossVnet_05_VirtualNetworkAddressSpaces.png)

1.  Al hacer clic en la marca de verificación, la red virtual empezará a crearse. Cuando se cree, verá "Created" debajo de "Status" en la página de redes del Portal de administración.
    
    ![](./media/virtual-networks-create-site-to-site-cross-premises-connectivity/CreateCrossVNet_06_VirtualNetworkCreatedStatus.png)

## <a name="StartGateway">Inicio de la puerta de enlace</a>

Después de crear la red virtual de Azure, siga este procedimiento para configurar la puerta de enlace de la red virtual a fin de crear la VPN de sitio a sitio. Para este procedimiento necesita un dispositivo VPN que cumpla los requisitos mínimos. Para obtener más información acerca de los dispositivos de VPN y su configuración, consulte [Acerca de los dispositivos VPN de la red virtual][7].

**Para iniciar la puerta de enlace:**

1.  Cuando se cree la red virtual, en la página **Redes**, esta presentará el estado **Created**.
    
    En la columna **NAME**, haga clic en **YourVirtualNetwork** para abrir el panel.
    
    ![](./media/virtual-networks-create-site-to-site-cross-premises-connectivity/CreateCrossVNet_07_ClickYourVirtualNetwork.png)

2.  Haga clic en **PANEL** en la parte superior de la página. En la parte inferior de la página Panel, haga clic en **CREATE GATEWAY**. Seleccione **Dynamic Routing** o **Static Routing** como tipo de puerta de enlace según sus preferencias.
    
    Tenga en cuenta que, si desea utilizar esta red virtual para conexiones punto a sitio además de sitio a sitio, debe seleccionar "Dynamic Routing" como tipo de puerta de enlace. Antes de crear la puerta de enlace, compruebe que el dispositivo VPN sea compatible con el tipo de puerta de enlace que desea crear. Consulte [Acerca de los dispositivos VPN de la red virtual][7]. Cuando el sistema le pida confirmación de que desea crear la puerta de enlace, haga clic en **YES**.
    
    ![](./media/virtual-networks-create-site-to-site-cross-premises-connectivity/CreateCrossVnet_08_CreateGateway.png)

3.  Cuando comience el proceso de creación, verá un mensaje informándole de que la puerta de enlace ya se inició.
    
    La puerta de enlace puede tardar hasta 15 minutos en crearse.

4.  Una vez que se cree la puerta de enlace, deberá recopilar los siguientes datos, que se utilizarán para configurar el dispositivo VPN.

* Dirección IP de la puerta de enlace
* Clave compartida
* Plantilla de script de configuración de dispositivo VPN
  
	Los siguientes pasos le muestran cómo realizar este proceso.

1.  Para localizar la dirección IP de la puerta de enlace: la dirección IP de la puerta de enlace está situada en la página **PANEL** de la red virtual.
    
    ![](./media/virtual-networks-create-site-to-site-cross-premises-connectivity/CreateCrossVnet_09_GatewayIP.png)

2.  Para obtener una clave compartida: la clave compartida está situada en la página **PANEL** de la red virtual. Haga clic en "Manage Key" en la parte inferior de la pantalla y, a continuación, copie la clave que aparece en el cuadro de diálogo.
    
    ![](./media/virtual-networks-create-site-to-site-cross-premises-connectivity/CreateCrossVNet_10_ManageSharedKey.png)

3.  Descargue la plantilla del script de configuración del dispositivo VPN. En el panel, haga clic en **Download VPN Device Script**.

4.  En el cuadro de diálogo **Download a VPN Device Configuration Script**, seleccione el proveedor, la plataforma y el sistema operativo del dispositivo VPN de la empresa. Haga clic en el botón de marca de verificación y guarde el archivo.
    
    ![](./media/virtual-networks-create-site-to-site-cross-premises-connectivity/CreateCrossVnet_11_DownloadVPNDeviceScript.png)

Si no encuentra el dispositivo VPN en la lista desplegable, consulte [Acerca de los dispositivos VPN de la red virtual][7] en MSDN Library para obtener plantillas de script adicionales.

## <a name="ConfigVPN">Configuración del dispositivo VPN (administrador de red)</a>

Dado que cada dispositivo VPN es diferente, este procedimiento es de alto nivel y solo debe realizarlo el administrador de red.

Puede obtener el script de configuración de VPN en el Portal de administración o en el documento [Acerca de los dispositivos VPN de la red virtual][12], que también incluye información sobre los tipos de enrutamiento y los dispositivos que son compatibles con la configuración de enrutamiento seleccionada.

Para obtener información adicional acerca de la configuración de una puerta de enlace de red virtual, consulte [Configurar una puerta de enlace de red virtual en el Portal de administración][13], así como la documentación del dispositivo VPN.

En este procedimiento se asume lo siguiente:

* La persona que va a configurar el dispositivo VPN está capacitada para ello. Debido al número de dispositivos compatibles con la red virtual y a las configuraciones específicas de cada familia de dispositivos, estos pasos no constituyen una guía detallada de la configuración del dispositivo. Por lo tanto, es importante que la persona encargada de configurar el dispositivo esté familiarizada tanto con el dispositivo como con su configuración.

* El dispositivo que seleccionó es compatible con la red virtual. Consulte en [este documento][7] la información de compatibilidad de los dispositivos.

**Para configurar el dispositivo VPN:**

1.  Modifique el script de configuración de VPN. Configurará los siguientes parámetros:
    
    a. Directivas de seguridad
    
    b. Túnel de entrada
    
    c. Túnel de salida

2.  Ejecute el script de configuración de VPN modificado para configurar el dispositivo VPN.

3.  Pruebe la conexión ejecutando uno de los comandos siguientes:
    
    <table  border="1">
    <tr>
    <th>-</th>
    
    <th>Cisco ASA</th>
    
    <th>Cisco ISR/ASR</th>
    
    <th>Juniper SSG/ISG</th>
    
    <th>Juniper SRX/J</th>
    
    </tr>
    
    	
    <tr>
    <td><b>SA de comprobación de modo principal</b>
    </td>
    
    <td><font  face="courier" size="-1">show crypto isakmp sa</font>
    </td>
    
    <td><font  face="courier" size="-1">show crypto isakmp sa</font>
    </td>
    
    <td><font  face="courier" size="-1">get ike cookie</font>
    </td>
    
    <td><font  face="courier" size="-1">show security ike security-association</font>
    </td>
    
    </tr>
    
    	
    <tr>
    <td><b>SA de comprobación de modo rápido</b>
    </td>
    
    <td><font  face="courier" size="-1">show crypto ipsec sa</font>
    </td>
    
    <td><font  face="courier" size="-1">show crypto ipsec sa</font>
    </td>
    
    <td><font  face="courier" size="-1">get sa</font>
    </td>
    
    <td><font  face="courier" size="-1">show security ipsec security-association</font>
    </td>
    
    </tr>
    
    </table>

## Pasos siguientes

Para ampliar el dominio local de Active Directory a la red virtual que acaba de crear, realice los siguientes tutoriales:

* [How to Create a Custom Virtual Machine][3]

* [Instalación de una réplica de controlador de dominio de Active Directory en Red virtual de Azure][4]

Si desea exportar la configuración de la red virtual a un archivo de configuración de red para guardarlo como copia de seguridad o utilizarlo como plantilla, consulte [Exportar la configuración de red virtual a un archivo de configuración de red][14].

## Otras referencias

* [Red virtual][2]

* [P+F de Red virtual][15]

* [Configurar una red virtual con archivos de configuración de red][16]

* [Incorporación de una máquina virtual a una red virtual][17]

* [Acerca de los dispositivos VPN de la red virtual][11]

* [Resolución de nombres][18]



[1]: http://go.microsoft.com/fwlink/?LinkId=296653
[2]: http://msdn.microsoft.com/en-us/library/windowsazure/jj156007.aspx
[3]: http://go.microsoft.com/fwlink/?LinkID=294356
[4]: http://go.microsoft.com/fwlink/?LinkId=299877
[5]: http://msdn.microsoft.com/en-us/library/windowsazure/jj156090.aspx
[6]: http://go.microsoft.com/fwlink/?LinkId=296652
[7]: http://go.microsoft.com/fwlink/?LinkID=248098
[8]: http://msdn.microsoft.com/library/windowsazure/dn133801.aspx
[9]: http://manage.windowsazure.com/
[10]: http://go.microsoft.com/fwlink/?LinkID=248092
[11]: http://msdn.microsoft.com/en-us/library/windowsazure/jj156075.aspx
[12]: http://go.microsoft.com/fwlink/?LinkId=248098
[13]: http://go.microsoft.com/fwlink/?LinkId=299878
[14]: http://go.microsoft.com/fwlink/?LinkID=299880
[15]: http://msdn.microsoft.com/library/windowsazure/dn133803.aspx
[16]: http://msdn.microsoft.com/en-us/library/windowsazure/jj156097.aspx
[17]: http://www.windowsazure.com/en-us/manage/services/networking/add-a-vm-to-a-virtual-network/
[18]: http://go.microsoft.com/fwlink/?LinkId=248097