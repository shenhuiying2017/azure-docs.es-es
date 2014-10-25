<properties linkid="manage-services-create-a-virtual-network" urlDisplayName="Tutorial: Create a cloud-only virtual network" pageTitle="Tutorial: Create a cloud-only virtual network" metaKeywords="" description="Learn how to create an example cloud-only Azure Virtual Network in this tutorial." metaCanonical="" services="virtual-machines,virtual-network" documentationCenter="" title="Tutorial: Create a Clound-only Virtual Network in Azure" authors="cherylmc" solutions="" manager="adinah" editor="" />

<tags ms.service="virtual-network" ms.workload="infrastructure-services" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="09/29/2014" ms.author="cherylmc"></tags>

# Tutorial: Creación de una red virtual solo en la nube en Azure

Este tutorial le guiará paso a paso en el Portal de administración de Azure para crear una red virtual de Azure solo en la nube de ejemplo, con dos subredes. La red virtual resultante tendrá el aspecto siguiente:

![createvnet][]

Por ejemplo, el objeto FrontEndSubnet se puede usar para servidores web y el objeto BackEndSubnet se puede usar para los servidores SQL o controladores de dominio.

En este tutorial se asume que no tiene ninguna experiencia previa con Azure. La descripción de esta configuración de ejemplo se ha creado de modo que pueda familiarizarse con los pasos que se requieren para crear una red virtual propia. Si desea crear una red virtual solo en la nube que funcione para su configuración específica, consulte [Configurar una red virtual solo en la nube en el Portal de administración][]. Si busca escenarios de diseño e información avanzada sobre redes virtuales, consulte [Red virtual][].

<div class="dev-callout"> 
<b>Nota:</b> 
<p>Este tutorial no le guiar&aacute; en la creaci&oacute;n de la configuraci&oacute;n entre ubicaciones en la que la red virtual est&aacute; conectada a la red de la organizaci&oacute;n. Si desea consultar un tutorial que le gu&iacute;e en la creaci&oacute;n de una red virtual con conectividad entre ubicaciones y una conexi&oacute;n VPN de sitio a sitio (por ejemplo, conectividad con Active Directory o con SharePoint ubicados en su compa&ntilde;&iacute;a), consulte el <a href="/es-es/manage/services/networking/cross-premises-connectivity/">Tutorial: Creaci&oacute;n de una red virtual entre locales para una conectividad sitio a sitio</a>.</p> 
</div>

## Objetivos

En este tutorial, aprenderá a configurar una red virtual de Azure solo en la nube con dos subredes.

## Requisitos previos

-   Una cuenta de Microsoft con al menos una suscripción válida y activa a Azure. Si todavía no dispone de una suscripción a Azure, puede registrarse para una prueba gratuita en [Probar Azure][]. Si tiene una suscripción de MSDN, consulte [Microsoft Azure Special Pricing: Ventajas de MSDN, MPN y Bizspark][].

## Creación de la red virtual para este tutorial

Para crear esta red virtual de ejemplo solo en la nube, haga lo siguiente:

1.  Inicie sesión en el [Portal de administración de Azure][].

2.  En la esquina inferior izquierda de la pantalla, haga clic en **Nuevo** \> **Servicios de red** \> **Red virtual** y luego en **Creación personalizada** para iniciar el asistente para la configuración.

    ![][]

3.  En la página **Detalles de la red virtual**, escriba la siguiente información:

-   **Nombre:** escriba **YourVirtualNetwork**.

-   **Región:** la red virtual se creará en un centro de datos ubicado en la región especificada. Para conseguir un rendimiento óptimo, en la lista desplegable seleccione la región en la que se encuentra.

    ![][1]

1.  Haga clic en la flecha de avance en la esquina inferior derecha. Para obtener más información acerca de la configuración de esta página, consulte la sección de la página Detalles de red virtual del documento [Acerca de la configuración de una red virtual en el Portal de administración][].

2.  En la página **Servidores DNS y conectividad VPN**, haga clic en la flecha siguiente situada en la parte inferior derecha. Azure asignará un servidor DNS de Azure basado en Internet a las nuevas máquinas virtuales que se agreguen a esta red virtual, lo que les dará acceso a los recursos de Internet. Para obtener más información acerca de la configuración de esta página, consulte la página Servidores DNS y conectividad VPN en [Acerca de la configuración de una red virtual en el Portal de administración][].

3.  Al igual que una red real, la red virtual necesita un intervalo de direcciones IP (conocido como espacio de direcciones) que se asignan a las máquinas virtuales que coloque en ella. La red virtual también admite subredes, que necesitan sus propios espacios de direcciones que se obtienen del espacio de direcciones de la red virtual. Para este tutorial, se crearán las subredes BackEndSUbnet y SubredFrontEnd. En la página **Espacios de direcciones de la red virtual**, configure los valores siguientes:

    -   Para Espacio de direcciones, seleccione **/16 (65535)** en **CIDR (RECUENTO DE DIRECCIONES)**.

    -   Para las subredes, en la primera fila, escriba **BackEndSubnet** sobre el nombre existente y **10.0.1.0** como IP inicial. A continuación, seleccione **/24 (256)** en **CIDR (RECUENTO DE DIRECCIONES)**. Haga clic en **agregar subred** y escriba **FrontEndSubnet** como nombre y **10.0.2.0** como IP inicial.

    ![][2]

Vuelva al diagrama de la red virtual y observará que ha configurado los siguientes espacios de direcciones:

    ![][Image7] 

Tenga en cuenta que el espacio de direcciones de una red virtual debe provenir de los espacios de direcciones privados de 10.0.0.0/8, 172.16.0.0/12 o 192.168.0.0/16 y especificarse en la anotación Enrutamiento entre dominios sin clase (CIDR) (también conocida como la notación de prefijo de red). Para obtener más información acerca de la configuración de esta página, consulte la página Espacios de direcciones de la red virtual del documento [Acerca de la configuración de una red virtual en el Portal de administración][].

1.  Haga clic en la marca de verificación de la parte inferior derecha de la página y se empezará a crear la red virtual. Cuando se haya creado la red virtual, en la sección Estado de la página **Redes** del Portal de administración de Azure verá **Creado**.

    ![][3]

Para seguir aprendiendo sobre los servicios de infraestructura de Azure, consulte lo siguiente:

-   [Creación de una máquina virtual personalizada][] Use este tema para instalar una máquina virtual en su red virtual. Para obtener más información acerca de las máquinas virtuales y las opciones de instalación, consulte [Máquinas virtuales de Azure][].

-   [Instalación de un nuevo bosque de Active Directory en una red virtual de Azure][]: use este tema para instalar un nuevo bosque de Windows Server Active Directory sin conectividad a ninguna otra red. El tutorial explica los pasos específicos requeridos para crear una máquina virtual (VM) para una nueva instalación de bosque. Si piensa utilizar este tutorial, no cree ninguna máquina virtual mediante el Portal de administración. Para obtener más información, consulte [Instrucciones para implementar Active Directory de Windows Server en máquinas virtuales de Azure][].

Para quitar esta red virtual, selecciónela, haga clic en **Eliminar** y luego en **Sí**.

Cuando esté listo para crear una red virtual solo en la nube que funcione para su configuración específica, consulte [Configurar una red virtual solo en la nube en el Portal de administración][].

Si busca escenarios de diseño e información avanzada sobre redes virtuales, consulte [Red virtual][].

Para obtener información acerca de procedimientos y configuración de la red virtual, consulte [Tareas de configuración de Red virtual de Azure][].

## Otras referencias

-   [P+F de Red virtual][]

-   [Tareas de configuración de Red virtual de Azure][4]

-   [Configurar una red virtual con archivos de configuración de red][]

-   [Resolución de nombres][]

  [createvnet]: ./media/create-virtual-network/createVNet_06_VNetExample.png
  [Configurar una red virtual solo en la nube en el Portal de administración]: http://msdn.microsoft.com/library/azure/dn631643.aspx
  [Red virtual]: http://msdn.microsoft.com/library/windowsazure/jj156007.aspx
  [Tutorial: Creación de una red virtual entre locales para una conectividad sitio a sitio]: /es-es/manage/services/networking/cross-premises-connectivity/
  [Probar Azure]: http://www.windowsazure.com/pricing/free-trial/
  [Microsoft Azure Special Pricing: Ventajas de MSDN, MPN y Bizspark]: http://azure.microsoft.com/es-es/pricing/member-offers/msdn-benefits-details/
  [Portal de administración de Azure]: http://manage.windowsazure.com/
  []: ./media/create-virtual-network/createVNet_01_OpenVirtualNetworkWizard.png
  [1]: ./media/create-virtual-network/createVNet_02_VirtualNetworkDetails.png
  [Acerca de la configuración de una red virtual en el Portal de administración]: http://go.microsoft.com/fwlink/?linkid=248092&clcid=0x409
  [2]: ./media/create-virtual-network/createVNet_04_VirtualNetworkAddressSpaces.png
  [3]: ./media/create-virtual-network/createVNet_05_VirtualNetworkCreatedStatus.png
  [Creación de una máquina virtual personalizada]: http://www.windowsazure.com/es-es/manage/windows/how-to-guides/custom-create-a-vm/
  [Máquinas virtuales de Azure]: http://www.windowsazure.com/es-es/manage/windows/
  [Instalación de un nuevo bosque de Active Directory en una red virtual de Azure]: http://www.windowsazure.com/es-es/manage/services/networking/active-directory-forest/
  [Instrucciones para implementar Active Directory de Windows Server en máquinas virtuales de Azure]: http://msdn.microsoft.com/es-es/library/windowsazure/jj156090.aspx
  [Tareas de configuración de Red virtual de Azure]: http://go.microsoft.com/fwlink/?linkid=296652&clcid=0x409
  [P+F de Red virtual]: http://go.microsoft.com/fwlink/?LinkId=296650
  [4]: http://go.microsoft.com/fwlink/?LinkId=296652
  [Configurar una red virtual con archivos de configuración de red]: http://msdn.microsoft.com/es-es/library/windowsazure/jj156097.aspx
  [Resolución de nombres]: http://go.microsoft.com/fwlink/?LinkId=248097
