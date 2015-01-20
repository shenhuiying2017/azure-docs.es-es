<properties urlDisplayName="Enable Remote Desktop" pageTitle="Habilite el Escritorio remoto para el acceso remoto de (Node.js)" metaKeywords="Azure Node.js de servicios en la nube, conexión remota de Node.js de Azure, acceso de VM de Node.js de Azure, acceso a la máquina virtual de Node.js de Azure." description="Vea cómo habilitar el acceso mediante Escritorio remoto para las máquinas virtuales que hospedan su aplicación Node.js de Azure. " metaCanonical="" services="cloud-services" documentationCenter="nodejs" title="Enabling Remote Desktop in Azure" authors="larryfr" solutions="" manager="wpickett" editor="" />

<tags ms.service="cloud-services" ms.workload="tbd" ms.tgt_pltfrm="na" ms.devlang="nodejs" ms.topic="article" ms.date="09/17/2014" ms.author="wpickett" />






# Habilitación de Escritorio remoto en Azure

Escritorio remoto le permite tener acceso al escritorio de una instancia de rol que se ejecuta en Azure. Puede usar una conexión a Escritorio remoto para configurar la máquina virtual o solucionar problemas con la aplicación.

<div class="dev-callout">
	<b>Nota:</b>
	<p>Los pasos descritos en este artículo solo se aplican a las aplicaciones Node hospedadas como un servicio en la nube de Azure.</p>
	</div>

Esta tarea incluye los siguientes pasos:

-   [Paso 1: Configuración del servicio para el acceso de Escritorio remoto mediante Azure PowerShell]
-   [Paso 2: Conexión a la instancia de rol]
-   [Paso 3: Configurar el servicio para deshabilitar el acceso al Escritorio remoto
    uso de PowerShell de Azure]

## <a name="step1"> </a>Paso 1: Configuración del servicio para el acceso de Escritorio remoto mediante Azure PowerShell

Para usar Escritorio remoto, necesita configurar la definición y la configuración del servicio con un nombre de usuario, una contraseña y un certificado para autenticarse con instancias de rol en la nube. [Azure PowerShell] incluye el cmdlet **Enable-AzureServiceProjectRemoteDesktop**, que realiza esta configuración en lugar del usuario.

Ejecute los siguientes pasos desde el equipo en que se ha creado la definición de servicio.

1.  En el menú **Inicio**, seleccione **Azure PowerShell**.

	![Azure PowerShell start menu entry][powershell-menu]

2.  Cambie el directorio al directorio de servicio, escriba **Enable-AzureServiceProjectRemoteDesktop** y, a continuación, escriba un nombre de usuario y una contraseña para usarlos al autenticarse con las instancias de rol en la nube.

	![enable-azureserviceprojectremotedesktop][enable-rdp]

3.  Publique los cambios de la configuración del servicio en la nube. En el símbolo **PowerShell de Azure**, escriba **Publish-AzureServiceProject**.

	![publish-azureserviceproject][publish-project]

Cuando se hayan completado estos pasos, las instancias de rol del servicio en la nube están configuradas para el acceso de Escritorio remoto.

## <a name="step2"> </a>Paso 2: Conexión a la instancia de rol

Con la implementación en funcionamiento en Azure, puede conectarse a la instancia de rol.

1.  En el [Portal de administración de Azure], seleccione **Servicios en la nube** y, a continuación, el servicio implementado en el paso 1 anterior.

	![azure management portal][cloud-services]

2.  Haga clic en **Instances** y, a continuación, haga clic en **Production** o **Staging** para ver las instancias del servicio. Seleccione una instancia y, a continuación, haga clic en **Connect** en la parte inferior de la página.

    ![The instances page][3]

2.  Al hacer clic en **Connect**, el explorador web le pide que guarde un archivo .rdp. Si usa Internet Explorer, haga clic en **Abrir**.

    ![prompt to open or save the .rdp file][4]

3.  Al abrir el archivo, aparece el siguiente indicador de seguridad:

    ![Windows security prompt][5]

4.  Haga clic en **Connect** y, a continuación, aparecerá un indicador de seguridad para que escriba las credenciales de acceso a la instancia. Escriba la contraseña creada en el [paso 1][Step 1: Configure the service for Remote Desktop access using Azure PowerShell] y, a continuación, haga clic en **OK**.

    ![username/password prompt][6]

Cuando se ha establecido la conexión, la conexión a Escritorio remoto muestra el escritorio de la instancia en Azure. Ya ha obtenido acceso satisfactoriamente a la instancia y puede ejecutar las tareas necesarias para administrar la aplicación.

![Remote desktop session][7]

## <a name="step3"> </a>Paso 3: Configuración del servicio para deshabilitar el acceso de Escritorio remoto mediante Azure PowerShell

Cuando ya no necesite conexiones de Escritorio remoto a las instancias de rol en la nube, deshabilite el acceso de Escritorio remoto mediante [Azure PowerShell]

1.  En el menú **Inicio**, seleccione **Azure PowerShell**.

2.  Cambie el directorio al directorio de servicio y escriba **Disable-AzureServiceProjectRemoteDesktop**:

3.  Publique los cambios de la configuración del servicio en la nube. En el símbolo **Azure PowerShell**, escriba **Publish-AzureServiceProject**.

## Recursos adicionales

- [Acceso remoto a las instancias de rol en Azure] 
- [Uso de Escritorio de remoto con los roles de Azure]

  [Paso 1: Configuración del servicio para el acceso de Escritorio remoto mediante Azure PowerShell]: #step1
  [Paso 2: Conexión a la instancia de rol]: #step2
  [Paso 3: Configuración del servicio para deshabilitar el acceso de Escritorio remoto mediante Azure PowerShell]: #step3
  [PowerShell de Azure]: http://go.microsoft.com/?linkid=9790229&clcid=0x409

[Portal de administración de Azure]: http://manage.windowsazure.com
[powershell-menu]: ./media/cloud-services-nodejs-enable-remote-desktop/azure-powershell-menu.png
[publish-project]: ./media/cloud-services-nodejs-enable-remote-desktop/publish-rdp.png
[enable-rdp]: ./media/cloud-services-nodejs-enable-remote-desktop/enable-rdp.png
[cloud-services]: ./media/cloud-services-nodejs-enable-remote-desktop/cloud-services-remote.png
  [3]: ./media/cloud-services-nodejs-enable-remote-desktop/cloud-service-instance.png
  [4]: ./media/cloud-services-nodejs-enable-remote-desktop/rdp-open.png
  [5]: ./media/cloud-services-nodejs-enable-remote-desktop/remote-desktop-12.png
  [6]: ./media/cloud-services-nodejs-enable-remote-desktop/remote-desktop-13.png
  [7]: ./media/cloud-services-nodejs-enable-remote-desktop/remote-desktop-14.png
  
  [Acceso remoto a las instancias de rol en Azure]: http://msdn.microsoft.com/es-es/library/windowsazure/hh124107.aspx
  [Uso de Escritorio de remoto con los roles de Azure]: http://msdn.microsoft.com/es-es/library/windowsazure/gg443832.aspx

<!--HONumber=35.2-->
