<properties urlDisplayName="Create a virtual machine in the Preview Portal" pageTitle="Creación de una máquina virtual con Windows en el Portal de vista previa de Azure" metaKeywords="Azure image gallery vm" description="Aprenda a crear una máquina virtual (VM) de Azure que ejecute Windows Server usando la Galería de máquinas virtuales del Portal de vista previa de Azure." metaCanonical="" services="virtual-machines" documentationCenter="" title="Create a Virtual Machine Running Windows in the Azure Preview Portal" authors="danlep,kathydav,rasquill" solutions="" manager="timlt" editor="tysonn" />

<tags ms.service="virtual-machines" ms.workload="infrastructure-services" ms.tgt_pltfrm="vm-windows" ms.devlang="na" ms.topic="article" ms.date="12/11/2014" ms.author="danlep,kathydav,rasquill" />

<!---Workflow can be confusing...have to select the correct size with **Browse all pricing tiers** then click Select on that pane and the **Recommended pricing tiers** pane to apply. But even after that, it didn't seem to pick the Premium storage account...showed up as 'standard GRS' when I inspected what type of storage account would be used for the VM. 
-->

# Creación de una máquina virtual en el Portal de vista previa de Azure#

<div class="dev-center-tutorial-selector sublanding"><a href="/es-es/documentation/articles/virtual-machines-windows-tutorial/" title="Azure Portal">Portal de Azure</a><a href="/es-es/documentation/articles/virtual-machines-windows-tutorial-azure-preview/" title="Azure Preview Portal" class="current">Portal de vista previa de Azure</a></div>

En este tutorial se muestra lo fácil que es crear una máquina virtual (VM) de Azure que ejecuta Windows, usando como ejemplo una imagen de Windows Server de la Galería de imágenes del Portal de vista previa de Azure. La Galería ofrece una gran variedad de imágenes, incluidos los sistemas operativos Windows, sistemas operativos basados en Linux e imágenes de aplicaciones. 

> [WACOM.NOTE] No es necesario que tenga experiencia con máquinas virtuales de Azure para completar este tutorial. Sin embargo, sí necesita una cuenta de Azure. Puede crear una cuenta de evaluación gratuita en pocos minutos. Para obtener más información, consulte [Creación de una cuenta de Azure](http://www.windowsazure.com/es-es/develop/php/tutorials/create-a-windows-azure-account/). 

En este tutorial se explica lo siguiente:

- [Creación de una máquina virtual](#createvirtualmachine)
- [Inicio de sesión en una máquina virtual después de su creación](#logon)

Si desea obtener más información, consulte [Máquinas virtuales](http://go.microsoft.com/fwlink/p/?LinkID=271224).


##<a id="createvirtualmachine"> </a>Creación de una máquina virtual##

En esta sección se muestra cómo usar el Portal de vista previa para crear una máquina virtual, con Windows Server 2012 R2 Datacenter como ejemplo. Puede usar la configuración predeterminada de Azure para la mayoría de la configuración y crear la máquina virtual en unos pocos minutos.

> [WACOM.NOTE] Las imágenes disponibles dependen de su suscripción. Este tutorial utiliza una imagen de Windows Server, pero una suscripción a MSDN puede ofrecer imágenes adicionales, incluidas las imágenes de escritorio. 
 

1. Inicie sesión en el [Portal de vista previa de Azure](https://portal.azure.com). Si todavía no dispone de una suscripción, consulte la oferta de [Versión de evaluación gratuita](http://www.windowsazure.com/es-es/pricing/free-trial/).

2. En el menú Concentrador, haga clic en **Nuevo**.

	![Select New from the Command Bar](./media/virtual-machines-windows-tutorial-azure-preview/new_button_preview_portal.png)

3. En el cuadro **Nuevo**, haga clic en **Todo**, haga clic en **Máquinas virtuales**, en **Windows Server 2012 R2 Datacenter** y, a continuación, en **Crear**.

	![Select a VM image from the Gallery](./media/virtual-machines-windows-tutorial-azure-preview/image_gallery_preview_portal.png)
	
4. En el cuadro **Crear VM**, rellene los campos **Nombre de host**, **Nombre de usuario** y el campo **Contraseña**.  

	>[WACOM.NOTE] **El campo Nombre de usuario** se refiere a la cuenta administrativa que usa para administrar el servidor. Cree una contraseña única para esta cuenta y asegúrese de recordarla. **Necesitará el nombre de usuario y la contraseña para iniciar sesión en la máquina virtual**.
	

	![Configure host name and log on credentials](./media/virtual-machines-windows-tutorial-azure-preview/create_vm_name_pwd_preview_portal.png)
	
	
5. Revise la configuración predeterminada, como el **nivel de precios**, la **configuración opcional** y la **ubicación**. Estas opciones afectan al tamaño de la máquina virtual, así como las opciones de red, como la pertenencia al dominio. Por ejemplo, para probar el almacenamiento Premium en una máquina virtual, necesitará seleccionar una región y el tamaño que admite. 

	>[WACOM.NOTE] El almacenamiento Premium está en vista previa, disponible para las máquinas virtuales de la serie DS en determinadas regiones. Para obtener más información, consulte [Almacenamiento Premium: Almacenamiento de alto rendimiento para cargas de trabajo de máquina Virtual de Azure](http://azure.microsoft.com/es-es/documentation/articles/storage-premium-storage-preview-portal/).

	![Configure host name and log on credentials](./media/virtual-machines-windows-tutorial-azure-preview/create_vm_preview_portal.png)
	
6. Cuando haya terminado de revisar o actualizar la configuración, haga clic en **Crear**.	

7. Mientras Azure crea la máquina virtual, puede realizar un seguimiento del progreso en **Notificaciones**, en el menú Concentrador. Después de que Azure cree la máquina virtual, la verá en su panel de inicio.

	![VM appears on the Startboard](./media/virtual-machines-windows-tutorial-azure-preview/vm_startboard_preview_portal.png)

## <a id="logon"> </a>Inicio de sesión en una máquina virtual después de su creación ##

En esta sección se muestra cómo iniciar sesión en la máquina virtual para poder administrar su configuración y las aplicaciones que va a ejecutar en ella.

>[WACOM.NOTE] Para obtener consejos acerca de los requisitos y la solución de problemas, vea [Conexión a una máquina virtual de Azure con RDP o SSH](http://go.microsoft.com/fwlink/p/?LinkId=398294).

1. Si no lo ha hecho todavía, inicie sesión en el [Portal de vista previa de Azure](https://portal.azure.com).

2. Haga clic en la máquina virtual en el panel de inicio. Si necesita buscarlo, haga clic en **Examinar** y, después, en **Máquinas virtuales**. 

	![Browse to find the VM](./media/virtual-machines-windows-tutorial-azure-preview/browse_vm_preview_portal.png)

3. En el cuadro de la máquina virtual, haga clic en **Conectar** en la parte superior.

	![Log on to the virtual machine](./media/virtual-machines-windows-tutorial-azure-preview/connect_vm_preview_portal.png)

4. Haga clic en **Abrir** para usar el archivo de protocolo de escritorio remoto que se creó automáticamente para la máquina virtual.
	
5. Haga clic en **Conectar** para continuar con el proceso de conexión.

	![Continue with connecting](./media/virtual-machines-log-on-windows-server/connectpublisher.png)

6. Escriba el nombre de usuario y la contraseña de la cuenta administrativa en la máquina virtual y, a continuación, haga clic en **Aceptar**.
	
7. Haga clic en **Sí** para verificar la identidad de la máquina virtual.

	![Verify the identity of the machine](./media/virtual-machines-log-on-windows-server/connectverify.png)

	Ahora puede trabajar con la máquina virtual igual que hace con cualquier otro servidor.

##Pasos siguientes 

Para obtener más información sobre la configuración de máquinas virtuales de Windows en Azure, consulte los siguientes artículos:

[Conexión de máquinas virtuales en un Servicio en la nube](http://www.windowsazure.com/es-es/documentation/articles/cloud-services-connect-virtual-machine/)

[Creación y carga de su propio disco duro virtual con el sistema operativo Windows Server](http://www.windowsazure.com/es-es/documentation/articles/virtual-machines-create-upload-vhd-windows-server/)

[Asociación de discos de datos a una máquina virtual](http://www.windowsazure.com/es-es/documentation/articles/storage-windows-attach-disk/)

[Administración de la disponibilidad de las máquinas virtuales](http://www.windowsazure.com/es-es/documentation/articles/manage-availability-virtual-machines/)

[Acerca de los ajustes de configuración de máquinas virtuales en Azure](http://msdn.microsoft.com/library/azure/dn763935.aspx)

[Creación de una máquina virtual]: #custommachine
[Inicio de sesión en una máquina virtual después de su creación]: #logon

<!--HONumber=35.2-->
