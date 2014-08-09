<properties linkid="manage-services-create-a-virtual-network" urlDisplayName="Create a virtual network" pageTitle="Create a virtual network - Azure service management" metaKeywords="" description="Learn how to create an Azure Virtual Network." metaCanonical="" services="virtual-machines,virtual-network" documentationCenter="" title="Create a Virtual Network in Azure" authors="" solutions="" manager="" editor="" />

Creación de una red virtual en Azure
====================================

Este tutorial le guiará paso a paso en la creación de una red virtual de Azure mediante el Portal de administración de Azure. Para obtener más información acerca de las redes virtuales de Azure, consulte [Red virtual](http://msdn.microsoft.com/es-es/library/windowsazure/jj156007.aspx).

En este tutorial se asume que no tiene ninguna experiencia previa con Azure. Se ha creado para que se familiarice con los pasos requeridos para crear una red virtual. Si busca escenarios de diseño e información avanzada sobre redes virtuales, consulte [Red virtual](http://msdn.microsoft.com/es-es/library/windowsazure/jj156007.aspx).

Después de finalizar este tutorial, tendrá una red virtual en la que podrá implementar los servicios de Azure y las máquinas virtuales.

**Nota:**

Este tutorial no le guía en la creación de la configuración entre locales. Si desea consultar un tutorial que le guíe en la creación de una red virtual con conectividad entre entornos de sitio a sitio (por ejemplo, conectividad con Active Directory o con SharePoint ubicados en su compañía), consulte [Creación de una red virtual para conectividad entre locales](/es-es/manage/services/networking/cross-premises-connectivity/).

Para obtener información acerca de procedimientos y configuración de la red virtual, consulte [Tareas de configuración de Red virtual de Azure](http://go.microsoft.com/fwlink/?LinkId=296652).

Para obtener directrices acerca de la implementación de AD DS en máquinas virtuales de Azure, consulte [Directrices para implementar Windows Server Active Directory en máquinas virtuales de Azure](http://msdn.microsoft.com/es-es/library/windowsazure/jj156090.aspx).

Objetivos
---------

En este tutorial, aprenderá a:

-   Configurar una red virtual básica de Azure en la que podrá agregar servicios en la nube y máquinas virtuales de Azure.

Requisitos previos
------------------

-   Una cuenta de Windows Live con al menos una suscripción válida y activa.

Creación de una red virtual
---------------------------

**Para crear una red virtual solo en la nube:**

1.  Inicie sesión en el [Portal de administración de Azure](http://manage.windowsazure.com/).

2.  En la esquina inferior izquierda de la pantalla, haga clic en **New**. En el panel de navegación, haga clic en **Redes** y, a continuación, en **Red virtual**. Haga clic en **Custom Create** para iniciar el asistente de configuración.

    ![](./media/create-virtual-network/createVNet_01_OpenVirtualNetworkWizard.png)

3.  En la página **Virtual Network Details**, escriba la información siguiente y, a continuación, haga clic en la flecha para avanzar situada en la esquina inferior derecha. Para obtener más información acerca de la configuración de la página de detalles, consulte la sección de la página **Detalles de red virtual** en [Acerca de la configuración de una red virtual en el Portal de administración](http://go.microsoft.com/fwlink/?LinkID=248092).

-   **Nombre**: Nombre de la red virtual. Escriba *YourVirtualNetwork*.

-   **Grupo de afinidad**: En la lista desplegable, seleccione **Crear un nuevo grupo de afinidad**. Con los grupos de afinidad podrá agrupar físicamente servicios de Azure en el mismo centro de datos para aumentar el rendimiento. Solo se puede asignar una red virtual a un grupo de afinidad.

-   **Región**: En la lista desplegable, seleccione la región deseada. La red virtual se creará en un centro de datos ubicado en la región especificada.

-   **Nombre del grupo de afinidad**: Nombre del nuevo grupo de afinidad. Escriba *YourAffinityGroup*.

    ![](./media/create-virtual-network/createVNet_02_VirtualNetworkDetails.png)

1.  En la página sobre **servidores DNS y conectividad VPN**, especifique la información siguiente y, a continuación, haga clic en la flecha siguiente en la parte inferior derecha. Para obtener más información acerca de la configuración de esta página, consulte la página **Servidores DNS y conectividad VPN** en [Acerca de la configuración de una red virtual en el Portal de administración](http://go.microsoft.com/fwlink/?LinkID=248092).

    -   **Servidores DNS (opcional)**: Introduzca el nombre del servidor DNS y la dirección IP que desea utilizar. Esta configuración no crea un servidor DNS, hace referencia a un servidor DNS ya existente.

        **Nota:**

        Si desea utilizar un servicio DNS público, puede especificar esa información en esta pantalla. En caso contrario, la resolución de nombres se configura de forma predeterminada en el servicio de Azure. Para obtener más información, consulte [Resolución de nombres](http://go.microsoft.com/fwlink/?linkid=248097).

    -   **No active la casilla de conectividad de punto a sitio o de sitio a sitio**. La red virtual que estamos creando en este tutorial no está pensada para la conectividad entre locales.

    ![](./media/create-virtual-network/createVNet_03_DNSServersandVPNConnectivity.png)

2.  En la página **Virtual Network Address Spaces**, especifique la información siguiente y, a continuación, haga clic en la marca de verificación en la parte inferior derecha para configurar la red. El espacio de direcciones debe ser un rango de direcciones privadas, con la notación CIDR 10.0.0.0/8, 172.16.0.0/12 o 192.168.0.0/16 (de conformidad con el documento RFC 1918). Para obtener más información acerca de la configuración de esta página, consulte la página **Espacios de direcciones de la red virtual** en [Acerca de la configuración de una red virtual en el Portal de administración](http://go.microsoft.com/fwlink/?LinkID=248092).

    -   **Address Space:** Haga clic en CIDR en la esquina superior derecha y, a continuación, escriba lo siguiente:

        -   **Starting IP:** 10.4.0.0

        -   **CIDR:** /16

    -   **Add subnet:** Escriba lo siguiente:

        -   **Cambie el nombre de la subred 1** por *FrontEndSubnet* con la dirección IP inicial *10.4.2.0/24* y, a continuación, haga clic en **add subnet**.

        -   **Cree una subred** denominada *SubredBackEnd* con la dirección IP inicial *10.4.3.0/24*.

        -   Compruebe que ahora tiene dos subredes creadas y, a continuación, haga clic en la marca de verificación de la parte inferior derecha para crear una red virtual.

    ![](./media/create-virtual-network/createVNet_04_VirtualNetworkAddressSpaces.png)

3.  Al hacer clic en la marca de verificación, la red virtual empezará a crearse. Cuando se cree la red virtual, verá **Created** debajo de **Status** en la página de redes del Portal de administración.

    ![](./media/create-virtual-network/createVNet_05_VirtualNetworkCreatedStatus.png)

4.  Una vez creada la red virtual, puede consultar los siguientes tutoriales:

    -   [Incorporación de una máquina virtual a una red virtual](/es-es/manage/services/networking/add-a-vm-to-a-virtual-network/): Utilice este tutorial básico para instalar una máquina virtual en su red virtual.

    -   Para obtener más información acerca de las máquinas virtuales y las opciones de instalación, consulte [Creación de una máquina virtual personalizada](/es-es/manage/windows/how-to-guides/custom-create-a-vm/) y [Máquinas virtuales de Azure](/es-es/manage/windows/).

    -   [Instalación de un nuevo bosque de Active Directory en Azure](/es-es/manage/services/networking/active-directory-forest/): Utilice este tutorial para instalar un nuevo bosque de Active Directory sin conectividad a cualquier otra red. El tutorial explica los pasos específicos requeridos para crear una máquina virtual (VM) para una nueva instalación de bosque. Si piensa utilizar este tutorial, no cree ninguna máquina virtual mediante el Portal de administración.

Otras referencias
-----------------

-   [Red virtual](http://msdn.microsoft.com/es-es/library/windowsazure/jj156007.aspx)

-   [P+F de Red virtual](http://go.microsoft.com/fwlink/?LinkId=296650)

-   [Tareas de configuración de Red virtual de Azure](http://go.microsoft.com/fwlink/?LinkId=296652)

-   [Configurar una red virtual con archivos de configuración de red](http://msdn.microsoft.com/es-es/library/windowsazure/jj156097.aspx)

-   [Resolución de nombres](http://go.microsoft.com/fwlink/?LinkId=248097)


