<properties urlDisplayName="Create a virtual machine in the Preview Portal" pageTitle="Creaci&oacute;n de una m&aacute;quina virtual que ejecuta Windows en el Portal de vista previa de Azure" metaKeywords="Azure image gallery vm" description="Aprenda a crear una m&aacute;quina virtual (VM) de Azure que ejecute Windows Server usando la Galer&iacute;a de m&aacute;quinas virtuales del Portal de vista previa de Azure." metaCanonical="" services="virtual-machines" documentationCenter="" title="Creaci&oacute;n de una m&aacute;quina virtual que ejecuta Windows en el Portal de vista previa de Azure" authors="danlep,kathydav,rasquill" solutions="" manager="timlt" editor="tysonn" />

<tags ms.service="virtual-machines" ms.workload="infrastructure-services" ms.tgt_pltfrm="vm-windows" ms.devlang="na" ms.topic="article" ms.date="09/16/2014" ms.author="danlep,kathydav,rasquill" />

# Creación de una máquina virtual que ejecuta Windows en el Portal de vista previa de Azure

<div class="dev-center-tutorial-selector sublanding"><a href="/es-es/documentation/articles/virtual-machines-windows-tutorial/" title="Portal de Azure">Portal de Azure</a><a href="/es-es/documentation/articles/virtual-machines-windows-tutorial-azure-preview/" title="Portal de Azure en vista previa" class="current">Portal de Azure en vista previa</a></div>

En este tutorial se muestra lo fácil que es crear una máquina virtual (VM) de Azure que ejecuta Windows, usando como ejemplo una imagen de Windows Server de la Galería de imágenes del Portal de vista previa de Azure. La Galería ofrece una gran variedad de imágenes, incluidos los sistemas operativos Windows, sistemas operativos basados en Linux e imágenes de aplicaciones.

> [WACOM.NOTE] No es necesario que tenga experiencia con máquinas virtuales de Azure para completar este tutorial. Sin embargo, sí necesita una cuenta de Azure. Puede crear una cuenta de evaluación gratuita en pocos minutos. Para obtener más información, consulte [Creación de una cuenta de Azure][Creación de una cuenta de Azure].

En este tutorial se explica lo siguiente:

-   [Creación de una máquina virtual][Creación de una máquina virtual]
-   [Inicio de sesión en una máquina virtual después de su creación][Inicio de sesión en una máquina virtual después de su creación]

Si desea obtener más información, consulte [Máquinas virtuales][Máquinas virtuales].

## <span id="createvirtualmachine"></span> </a>Creación de una máquina virtual

Esta sección muestra cómo usar el Portal de vista previa para crear una máquina virtual, utilizando Windows Server como ejemplo. Puede usar la configuración predeterminada de Azure para la mayoría de la configuración y crear la máquina virtual en unos pocos minutos.

> [WACOM.NOTE] La cantidad y el tipo de imágenes disponibles en la galería depende del tipo de suscripción que tenga. Este tutorial utiliza una imagen de Windows Server, pero una suscripción a MSDN puede poner a su disposición imágenes adicionales, incluidas imágenes de escritorio.

1.  Inicie sesión en el [Portal de vista previa de Azure][Portal de vista previa de Azure]. Consulte la oferta [Evaluación gratuita][Evaluación gratuita] si todavía no tiene una suscripción.

2.  En el menú Concentrador, haga clic en **Nuevo**.

    ![Select New from the Command Bar][Select New from the Command Bar]

3.  En **Nuevo**, haga clic en **Todo** y después, bajo **Galería**, haga clic en **Máquinas virtuales**. Haga clic en **Centro de datos de Windows Server 2012 R2**. Desde aquí, haga clic en **Crear**.

    ![Select a VM image from the Gallery][Select a VM image from the Gallery]

4.  En el cuadro **Crear VM**, rellene los campos **Nombre de host** (con el nombre que desee para la máquina virtual), **Nombre de usuario** (con un nombre administrativo) y el campo **Contraseña** (con una contraseña segura)

    ![Configure host name and log on credentials][Configure host name and log on credentials]

    > [WACOM.NOTE] El campo **Nombre de usuario** se refiere a la cuenta administrativa que usa para administrar el servidor. Cree una contraseña única para esta cuenta y asegúrese de recordarla. **Necesitará el nombre de usuario y la contraseña para iniciar sesión en la máquina virtual**.

5.  Para utilizar la configuración predeterminada para las opciones de máquina virtual restantes y comenzar a crear dicha máquina virtual, haga clic en **Crear**. No obstante, si lo desea, antes de hacer clic en **Crear**, puede explorar las opciones de **Configuración opcional**. Por ejemplo, puede configurar diagnósticos opcionales en la máquina virtual para realizar un seguimiento de numerosas métricas de dicha máquina posteriormente. Haga clic en **Configuración opcional**, haga clic en **Diagnósticos** y cambie la opción **Estado** a **Activado**.

    ![Turn on VM diagnostics][Turn on VM diagnostics]

    > [WACOM.NOTE] Si activa Diagnósticos de Azure, Azure almacenará los datos de diagnósticos en una cuenta de almacenamiento de Azure, lo que dará lugar a un coste de almacenamiento adicional.

6.  Mientras Azure crea la máquina virtual, puede realizar un seguimiento del progreso en **Notificaciones**, en el menú Concentrador. Después de que Azure cree la máquina virtual, la verá en su panel de inicio.

    ![VM appears on the Startboard][VM appears on the Startboard]

## <span id="logon"></span> </a>Inicio de sesión en una máquina virtual después de su creación

En esta sección se muestra cómo iniciar sesión en la máquina virtual para poder administrar su configuración y las aplicaciones que va a ejecutar en ella.

> [WACOM.NOTE] Para conocer los requisitos y sugerencias de solución de problemas, consulte [Conexión con una máquina virtual de Azure con RDP o SSH][Conexión con una máquina virtual de Azure con RDP o SSH].

1.  Si no lo ha hecho todavía, inicie sesión en el [Portal de vista previa de Azure][Portal de vista previa de Azure].

2.  Haga clic en la máquina virtual en el panel de inicio. Si necesita buscarlo, haga clic en **Examinar** y, después, en **Máquinas virtuales**.

    ![Browse to find the VM][Browse to find the VM]

3.  En el cuadro de la máquina virtual, haga clic en **Conectar** en la parte superior.

    ![Iniciar sesión en la nueva máquina virtual][Iniciar sesión en la nueva máquina virtual]

4.  Haga clic en **Abrir** para usar el archivo de protocolo de escritorio remoto que se creó automáticamente para la máquina virtual.

5.  Haga clic en **Conectar** para continuar con el proceso de conexión.

    ![Continuar con la conexión][Continuar con la conexión]

6.  Escriba el nombre de usuario y la contraseña de la cuenta administrativa en la máquina virtual y, a continuación, haga clic en **Aceptar**.

7.  Haga clic en **Sí** para verificar la identidad de la máquina virtual.

    ![Verificar la identidad de la máquina][Verificar la identidad de la máquina]

    Ahora puede trabajar con la máquina virtual igual que hace con cualquier otro servidor.

## Pasos siguientes

Para obtener más información sobre la configuración de máquinas virtuales de Windows en Azure, consulte los siguientes artículos:

[Conexión de máquinas virtuales en un Servicio en la nube][Conexión de máquinas virtuales en un Servicio en la nube]

[Creación y carga de su propio disco duro virtual con el sistema operativo Windows Server][Creación y carga de su propio disco duro virtual con el sistema operativo Windows Server]

[Asociación de discos de datos a una máquina virtual][Asociación de discos de datos a una máquina virtual]

[Administración de la disponibilidad de las máquinas virtuales][Administración de la disponibilidad de las máquinas virtuales]

[Acerca de los ajustes de configuración de máquinas virtuales en Azure][Acerca de los ajustes de configuración de máquinas virtuales en Azure]

  [Creación de una cuenta de Azure]: http://www.windowsazure.com/es-es/develop/php/tutorials/create-a-windows-azure-account/
  [Creación de una máquina virtual]: #createvirtualmachine
  [Inicio de sesión en una máquina virtual después de su creación]: #logon
  [Máquinas virtuales]: http://go.microsoft.com/fwlink/p/?LinkID=271224
  [Portal de vista previa de Azure]: https://portal.azure.com
  [Evaluación gratuita]: http://www.windowsazure.com/es-es/pricing/free-trial/
  [Select New from the Command Bar]: ./media/virtual-machines-windows-tutorial-azure-preview/new_button_preview_portal.png
  [Select a VM image from the Gallery]: ./media/virtual-machines-windows-tutorial-azure-preview/image_gallery_preview_portal.png
  [Configure host name and log on credentials]: ./media/virtual-machines-windows-tutorial-azure-preview/create_vm_preview_portal.png
  [Turn on VM diagnostics]: ./media/virtual-machines-windows-tutorial-azure-preview/vm_diagnostics_status_preview_portal.png
  [VM appears on the Startboard]: ./media/virtual-machines-windows-tutorial-azure-preview/vm_startboard_preview_portal.png
  [Conexión con una máquina virtual de Azure con RDP o SSH]: http://go.microsoft.com/fwlink/p/?LinkId=398294
  [Browse to find the VM]: ./media/virtual-machines-windows-tutorial-azure-preview/browse_vm_preview_portal.png
  [Iniciar sesión en la nueva máquina virtual]: ./media/virtual-machines-windows-tutorial-azure-preview/connect_vm_preview_portal.png
  [Continuar con la conexión]: ./media/virtual-machines-log-on-windows-server/connectpublisher.png
  [Verificar la identidad de la máquina]: ./media/virtual-machines-log-on-windows-server/connectverify.png
  [Conexión de máquinas virtuales en un Servicio en la nube]: http://www.windowsazure.com/es-es/documentation/articles/cloud-services-connect-virtual-machine/
  [Creación y carga de su propio disco duro virtual con el sistema operativo Windows Server]: http://www.windowsazure.com/es-es/documentation/articles/virtual-machines-create-upload-vhd-windows-server/
  [Asociación de discos de datos a una máquina virtual]: http://www.windowsazure.com/es-es/documentation/articles/storage-windows-attach-disk/
  [Administración de la disponibilidad de las máquinas virtuales]: http://www.windowsazure.com/es-es/documentation/articles/manage-availability-virtual-machines/
  [Acerca de los ajustes de configuración de máquinas virtuales en Azure]: http://msdn.microsoft.com/library/azure/dn763935.aspx
