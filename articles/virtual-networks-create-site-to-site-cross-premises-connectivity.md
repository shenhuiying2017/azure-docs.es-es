<properties linkid="manage-services-cross-premises-connectivity" urlDisplayName="Tutorial: Create a Cross-Premises Virtual Network for Site-to-Site Connectivity" pageTitle="Tutorial: Create a Cross-Premises Virtual Network for Site-to-Site Connectivity" metaKeywords="" description="Learn how to create an Azure Virtual Network with cross-premises connectivity in this tutorial." metaCanonical="" services="virtual-network" documentationCenter="" title="Create a Virtual Network for Site-to-Site Cross-Premises Connectivity" authors="cherylmc" solutions="" manager="adinah" editor="" />

<tags ms.service="virtual-network" ms.workload="infrastructure-services" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="09/23/2014" ms.author="cherylmc"></tags>

# Tutorial: Creación de una red virtual entre locales para una conectividad sitio a sitio

En este tutorial se muestran los pasos que debe seguir para crear, por ejemplo, una red virtual entre locales con conexión sitio a sitio. Si desea crear una VPN de punto a sitio utilizando certificados y un cliente VPN, consulte [Configurar una VPN de punto a sitio con el asistente del Portal de administración][].

En este tutorial se asume que no tiene ninguna experiencia previa con Azure. La finalidad del mismo es ayudarle a familiarizarse con los pasos necesarios para crear una red virtual entre locales. Si busca escenarios de diseño e información avanzada sobre redes virtuales, consulte la [información general sobre Red virtual de Azure][].

Después de finalizar este tutorial, tendrá un ejemplo de red virtual entre locales en la que podrá implementar los servicios de Azure y las máquinas virtuales.

Tenga en cuenta que los parámetros de configuración utilizados en este tutorial no se han personalizado para la red de su organización. Si configura la red virtual y la conexión sitio a sitio con los parámetros de configuración utilizados a modo de ejemplo en este tema, no funcionará. Para configurar una red virtual entre locales que funcione, deberá trabajar con su departamento informático y con su administrador de red para obtener la configuración correcta. Para obtener más información, consulte la sección de **Requisitos previos** de este tema:

Para obtener información acerca de cómo agregar una máquina virtual y ampliar su dominio local de Active Directory a Red virtual de Azure, consulte los siguientes documentos:

-   [How to Create a Custom Virtual Machine][]

-   [Instalación de una réplica de controlador de dominio de Active Directory en Red virtual de Azure][]

Para obtener directrices acerca de la implementación de AD DS en máquinas virtuales de Azure, consulte [Directrices para implementar Windows Server Active Directory en máquinas virtuales de Azure][].

Para obtener información acerca de procedimientos y configuración de la red virtual, consulte [Tareas de configuración de Red virtual de Azure][].

## Objetivos

En este tutorial, aprenderá a:

-   Configurar un ejemplo de red virtual de Azure entre locales a la que podrá agregar servicios de Azure.

-   Configurar la red virtual para comunicarse con la red de una organización.

## Requisitos previos

-   Una cuenta de Microsoft con al menos una suscripción válida y activa de Azure. Si todavía no dispone de una suscripción de Azure, puede registrarse para una prueba gratuita en [Try Azure][]. Si tiene una suscripción de MSDN, consulte [Microsoft Azure Special Pricing: MSDN, MPN, and Bizspark Benefits][].

Si utiliza este tutorial para configurar una red virtual entre locales que funciones y que esté personalizada para su organización, necesitará lo siguiente:

-   Los espacios de direcciones de IPv4 privado (en notación CIDR) para la red virtual y sus subredes.

-   Nombre y dirección IP de un servidor DNS local.

-   Dispositivo VPN con una dirección IPv4 pública. Necesitará la dirección IP para completar el asistente. El dispositivo VPN no puede estar situado detrás de un traductor de direcciones de red (NAT) y debe cumplir los requisitos mínimos de los dispositivos. Consulte [Acerca de los dispositivos VPN de la red virtual][] para obtener más información.

    Nota: Puede utilizar el servicio de enrutamiento y acceso remoto (RRAS) en un Windows Server como parte de la solución de VPN. Sin embargo, en este tutorial no se incluyen los pasos de configuración de RRAS.
    Para obtener información acerca de la configuración de RRAS, consulte [Plantillas del Servicio de Enrutamiento y acceso remoto][].

-   Conocimientos para configurar un enrutador para una conexión en modo túnel de IPsec, o bien alguien que le pueda ayudar con este paso.

-   El conjunto de espacios de direcciones (en notación CIDR) que resume las ubicaciones alcanzables de su red local.

## Pasos de alto nivel

1.  [Creación de una red virtual][]

2.  [Inicio de la puerta de enlace y recopilación de información para el administrador de red][]

3.  [Configuración del dispositivo VPN][]

## <a name="CreateVN">Creación de una red virtual</a>

Para crear un ejemplo de red virtual que se conecte a la red de una empresa:

1.  Inicie sesión en el [Portal de administración de Azure][].

2.  En la esquina inferior izquierda de la pantalla, haga clic en **New**. En el panel de navegación, haga clic en **Redes** y, a continuación, en **Red virtual**. Haga clic en **Custom Create** para iniciar el asistente de configuración.

    ![][]

3.  En la página **Virtual Network Details**, escriba la información siguiente y, a continuación, haga clic en la flecha para avanzar situada en la esquina inferior derecha. Para obtener más información acerca de la configuración de la página de detalles, consulte la sección **Página Detalles de red virtual** del documento [Acerca de la configuración de una red virtual en el Portal de administración][].

    -   **NAME:** Asigne un nombre a la red virtual. Por ejemplo en este tutorial escriba **YourVirtualNetwork**.

    -   **REGIÓN:** En la lista desplegable, seleccione la región que desee. La red virtual se creará en un centro de datos de Azure ubicado en la región especificada.

4.  En la página **DNS Servers and VPN Connectivity**, escriba la información siguiente y, a continuación, haga clic en la flecha para avanzar situada en la esquina inferior derecha.

    <div class="dev-callout"> 
<b>Nota:</b> 
<p>Es posible seleccionar a la vez las configuraciones<b>Point-To-Site</b> y <b>Site-To-Site</b> de esta p&aacute;gina. En este tutorial, seleccionaremos &uacute;nicamente la configuraci&oacute;n <b>Site-To-Site</b>. Para obtener m&aacute;s informaci&oacute;n acerca de la configuraci&oacute;n de esta p&aacute;gina, consulte la p&aacute;gina <b>Servidores DNS y conectividad VPN</b> en <a href="http://go.microsoft.com/fwlink/?LinkID=248092">Acerca de la configuraci&oacute;n de una red virtual en el Portal de administraci&oacute;n</a>.</p> 
</div>

    -   **DNS SERVERS:** Escriba el nombre y la dirección IP del servidor DNS que desee usar para la resolución de nombres. Normalmente se trata de un servidor DNS utilizado para la resolución de nombres local. Mediante este valor no se crea un servidor DNS. Por ejemplo en este tutorial, escriba el nombre **YourDNS** y la dirección IP **10.1.0.4**.
    -   **Configure Point-To-Site VPN:** Deje este campo en blanco.
    -   **Configure Site-To-Site VPN:** Active la casilla.
    -   **LOCAL NETWORK:** Seleccione **Specify a New Local Network** en la lista desplegable.

    ![][1]

5.  En la página **Site-To-Site Connectivity**, escriba la información siguiente y, a continuación, haga clic en la marca de verificación que aparece en la esquina inferior derecha de la página. Para obtener más información acerca de la configuración de esta página, consulte la sección **Página Conectividad de sitio a sitio** del documento [Acerca de la configuración de una red virtual en el Portal de administración][].

    -   **NAME:** Por ejemplo en este tutorial escriba **YourCorpHQ**.

    -   **VPN DEVICE IP ADDRESS:** Escriba la dirección IP pública del dispositivo VPN. Si no dispone de esta información, deberá obtenerla antes de realizar los pasos siguientes del asistente. Tenga en cuenta que el dispositivo VPN no puede encontrarse detrás de un NAT. Para obtener más información acerca de los dispositivos de VPN, consulte [Acerca de los dispositivos VPN de la red virtual][2].

    -   **ADDRESS SPACE:** Por ejemplo en este tutorial escriba **10.1.0.0/16**.
    -   **Add address space:** Este tutorial no requiere espacio de direcciones adicional.

    ![][3]

6.  En la página **Virtual Network Address Spaces**, escriba la información siguiente y, a continuación, haga clic en la marca de verificación que aparece en la esquina inferior derecha para configurar la red.

    El espacio de direcciones debe ser un rango de direcciones privadas, con la notación CIDR desde los espacios de direcciones 10.0.0.0/8, 172.16.0.0/12 o 192.168.0.0/16 (de conformidad con el documento RFC 1918). Para obtener más información acerca de la configuración de esta página, consulte la página **Espacios de direcciones de la red virtual** del documento [Acerca de la configuración de una red virtual en el Portal de administración][].

    -   **Address Space:** Por ejemplo, en este tutorial haga clic en **CIDR** en la esquina superior derecha y, a continuación, escriba lo siguiente:

        -   **Starting IP:** 10.4.0.0
        -   **CIDR:** /16
    -   **Add subnet:** Por ejemplo en este tutorial escriba lo siguiente:

        -   Cambie el nombre de la **Subnet-1** por **FrontEndSubnet** con la dirección IP inicial **10.4.2.0/24**.
        -   Agregue una subred llamada **BackEndSubnet** con la dirección IP inicial **10.4.3.0/24**.
        -   Agregue una subred llamada **ADDNSSubnet** con la dirección IP inicial **10.4.4.0/24**.
        -   Agregue una subred de puerta de enlace con la dirección IP inicial **10.4.1.0/24**.
    -   Por ejemplo en este tutorial, compruebe que ahora tenga tres subredes y una subred de puerta de enlace y, a continuación, haga clic en la marca de verificación situada en la esquina inferior derecha para crear la red virtual.

    ![][4]

7.  Al hacer clic en la marca de verificación, la red virtual empezará a crearse. Cuando se cree la red virtual, verá **Created** debajo de **Status** en la página de redes del Portal de administración.

    ![][5]

## <a name="StartGateway">Inicio de la puerta de enlace</a>

Después de crear la red virtual de Azure, siga este procedimiento para configurar la puerta de enlace de la red virtual a fin de crear la VPN de sitio a sitio. Para este procedimiento necesita un dispositivo VPN que cumpla los requisitos mínimos. Para obtener más información acerca de los dispositivos de VPN y su configuración, consulte [Acerca de los dispositivos VPN de la red virtual][].

**Para iniciar la puerta de enlace:**

1.  Cuando se cree la red virtual, en la página **Redes**, esta presentará el estado **Created**.

    En la columna **NAME**, haga clic en **YourVirtualNetwork** (en el ejemplo creado en este tutorial) para abrir el panel.

    ![][6]

2.  Haga clic en **PANEL** en la parte superior de la página. En la parte inferior de la página Panel, haga clic en **CREATE GATEWAY**. Seleccione **Dynamic Routing** o **Static Routing** como tipo de puerta de enlace según sus preferencias.

    Tenga en cuenta que, si desea utilizar esta red virtual para conexiones punto a sitio además de sitio a sitio, debe seleccionar **Dynamic Routing** como tipo de puerta de enlace. Antes de crear la puerta de enlace, compruebe que el dispositivo VPN sea compatible con el tipo de puerta de enlace que desea crear. Consulte [Acerca de los dispositivos VPN de la red virtual][]. Cuando el sistema le pida confirmación de que desea crear la puerta de enlace, haga clic en **YES**.

    ![][7]

3.  Cuando comience el proceso de creación, verá un mensaje informándole de que la puerta de enlace ya se inició.

    La puerta de enlace puede tardar hasta 15 minutos en crearse.

4.  Una vez que se cree la puerta de enlace, deberá recopilar los siguientes datos, que se utilizarán para configurar el dispositivo VPN.

    -   Dirección IP de la puerta de enlace
    -   Clave compartida
    -   Plantilla de script de configuración de dispositivo VPN

    Los siguientes pasos le muestran cómo realizar este proceso.

5.  Para ubicar la dirección IP de la puerta de enlace: La dirección IP de la puerta de enlace está situada en la página **PANEL** de la red virtual. Aquí tiene un ejemplo:

    ![][8]

6.  Para adquirir la clave compartida: La clave compartida está situada en la página **PANEL** de la red virtual. Haga clic en **Manage Key** en la parte inferior de la pantalla y, a continuación, copie la clave que aparece en el cuadro de diálogo. Necesitará esta clave para configurar el túnel de IPsec en el dispositivo de VPN de la empresa.

    ![][9]

7.  Para descargar la plantilla del script de configuración del dispositivo VPN: En el panel, haga clic en **Download VPN Device Script**.

8.  En el cuadro de diálogo **Download a VPN Device Configuration Script**, seleccione el proveedor, la plataforma y el sistema operativo del dispositivo VPN de la empresa. Haga clic en el botón de marca de verificación y guarde el archivo.

    ![][10]

Si no encuentra el dispositivo VPN en la lista desplegable, consulte [Acerca de los dispositivos VPN de la red virtual][] en MSDN Library para obtener plantillas de script adicionales.

## <a name="ConfigVPN">Configuración del dispositivo VPN (administrador de red)</a>

Dado que cada dispositivo VPN es diferente, este procedimiento es de alto nivel y solo debe realizarlo el administrador de red.

Puede obtener el script de configuración de VPN en el Portal de administración o en el documento [Acerca de los dispositivos VPN de la red virtual][11], que también incluye información sobre los tipos de enrutamiento y los dispositivos que son compatibles con la configuración de enrutamiento seleccionada.

Para obtener información adicional acerca de la configuración de una puerta de enlace de red virtual, consulte [Configurar una puerta de enlace de red virtual en el Portal de administración][], así como la documentación del dispositivo VPN.

En este procedimiento se asume lo siguiente:

-   La persona que va a configurar el dispositivo VPN está capacitada para ello. Debido al número de dispositivos compatibles con la red virtual y a las configuraciones específicas de cada familia de dispositivos, estos pasos no constituyen una guía detallada de la configuración del dispositivo. Por lo tanto, es importante que la persona encargada de configurar el dispositivo esté familiarizada tanto con el dispositivo como con su configuración.

-   El dispositivo que seleccionó es compatible con la red virtual. Consulte en [este documento][Acerca de los dispositivos VPN de la red virtual] la información de compatibilidad de los dispositivos.

**Para configurar el dispositivo VPN:**

1.  Modifique el script de configuración de VPN. Configurará los siguientes parámetros:

    a. directivas de seguridad

    b. túnel de entrada

    c. túnel de salida

2.  Ejecute el script de configuración de VPN modificado para configurar el dispositivo VPN.

3.  Pruebe la conexión ejecutando uno de los comandos siguientes:

    | -                                        | Cisco ASA                                                   | Cisco ISR/ASR                                               | Juniper SSG/ISG                                      | Juniper SRX/J                                                                  |
    |------------------------------------------|-------------------------------------------------------------|-------------------------------------------------------------|------------------------------------------------------|--------------------------------------------------------------------------------|
    | **SA de comprobación de modo principal** | <font face="courier" size="-1">show crypto isakmp sa</font> | <font face="courier" size="-1">show crypto isakmp sa</font> | <font face="courier" size="-1">get ike cookie</font> | <font face="courier" size="-1">show security ike security-association</font>   |
    | **SA de comprobación de modo rápido**    | <font face="courier" size="-1">show crypto ipsec sa</font>  | <font face="courier" size="-1">show crypto ipsec sa</font>  | <font face="courier" size="-1">get sa</font>         | <font face="courier" size="-1">show security ipsec security-association</font> |

## Pasos siguientes

Para ampliar el dominio local de Active Directory a la red virtual que acaba de crear, realice los siguientes tutoriales:

-   [How to Create a Custom Virtual Machine][]

-   [Instalación de una réplica de controlador de dominio de Active Directory en Red virtual de Azure][]

Si desea exportar la configuración de la red virtual a un archivo de configuración de red para guardarlo como copia de seguridad o utilizarlo como plantilla, consulte [Exportar la configuración de red virtual a un archivo de configuración de red][].

## Otras referencias

-   [Red virtual][información general sobre Red virtual de Azure]

-   [P+F de Red virtual][]

-   [Configurar una red virtual con archivos de configuración de red][]

-   [Incorporación de una máquina virtual a una red virtual][]

-   [Acerca de los dispositivos VPN de la red virtual][2]

-   [Resolución de nombres][]

  [Configurar una VPN de punto a sitio con el asistente del Portal de administración]: http://go.microsoft.com/fwlink/?LinkId=296653
  [información general sobre Red virtual de Azure]: http://msdn.microsoft.com/es-es/library/windowsazure/jj156007.aspx
  [How to Create a Custom Virtual Machine]: http://go.microsoft.com/fwlink/?LinkID=294356
  [Instalación de una réplica de controlador de dominio de Active Directory en Red virtual de Azure]: http://go.microsoft.com/fwlink/?LinkId=299877
  [Directrices para implementar Windows Server Active Directory en máquinas virtuales de Azure]: http://msdn.microsoft.com/es-es/library/windowsazure/jj156090.aspx
  [Tareas de configuración de Red virtual de Azure]: http://go.microsoft.com/fwlink/?LinkId=296652
  [Try Azure]: http://www.windowsazure.com/pricing/free-trial/
  [Microsoft Azure Special Pricing: MSDN, MPN, and Bizspark Benefits]: http://azure.microsoft.com/es-es/pricing/member-offers/msdn-benefits-details/
  [Acerca de los dispositivos VPN de la red virtual]: http://go.microsoft.com/fwlink/?LinkID=248098
  [Plantillas del Servicio de Enrutamiento y acceso remoto]: http://msdn.microsoft.com/library/windowsazure/dn133801.aspx
  [Creación de una red virtual]: #CreateVN
  [Inicio de la puerta de enlace y recopilación de información para el administrador de red]: #StartGateway
  [Configuración del dispositivo VPN]: #ConfigVPN
  [Portal de administración de Azure]: http://manage.windowsazure.com/
  []: ./media/virtual-networks-create-site-to-site-cross-premises-connectivity/CreateCrossVnet_01_OpenVirtualNetworkWizard.png
  [Acerca de la configuración de una red virtual en el Portal de administración]: http://go.microsoft.com/fwlink/?LinkID=248092
  [1]: ./media/virtual-networks-create-site-to-site-cross-premises-connectivity/CreateCrossVNet_03_DNSServersandVPNConnectivity.png
  [2]: http://msdn.microsoft.com/es-es/library/windowsazure/jj156075.aspx
  [3]: ./media/virtual-networks-create-site-to-site-cross-premises-connectivity/CreateCrossVnet_04_SitetoSite.png
  [4]: ./media/virtual-networks-create-site-to-site-cross-premises-connectivity/CreateCrossVnet_05_VirtualNetworkAddressSpaces.png
  [5]: ./media/virtual-networks-create-site-to-site-cross-premises-connectivity/CreateCrossVNet_06_VirtualNetworkCreatedStatus.png
  [6]: ./media/virtual-networks-create-site-to-site-cross-premises-connectivity/CreateCrossVNet_07_ClickYourVirtualNetwork.png
  [7]: ./media/virtual-networks-create-site-to-site-cross-premises-connectivity/CreateCrossVnet_08_CreateGateway.png
  [8]: ./media/virtual-networks-create-site-to-site-cross-premises-connectivity/CreateCrossVnet_09_GatewayIP.png
  [9]: ./media/virtual-networks-create-site-to-site-cross-premises-connectivity/CreateCrossVNet_10_ManageSharedKey.png
  [10]: ./media/virtual-networks-create-site-to-site-cross-premises-connectivity/CreateCrossVnet_11_DownloadVPNDeviceScript.png
  [11]: http://go.microsoft.com/fwlink/?LinkId=248098
  [Configurar una puerta de enlace de red virtual en el Portal de administración]: http://go.microsoft.com/fwlink/?LinkId=299878
  [Exportar la configuración de red virtual a un archivo de configuración de red]: http://go.microsoft.com/fwlink/?LinkID=299880
  [P+F de Red virtual]: http://msdn.microsoft.com/library/windowsazure/dn133803.aspx
  [Configurar una red virtual con archivos de configuración de red]: http://msdn.microsoft.com/es-es/library/windowsazure/jj156097.aspx
  [Incorporación de una máquina virtual a una red virtual]: http://www.windowsazure.com/es-es/manage/services/networking/add-a-vm-to-a-virtual-network/
  [Resolución de nombres]: http://go.microsoft.com/fwlink/?LinkId=248097
