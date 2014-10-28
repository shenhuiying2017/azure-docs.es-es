<properties linkid="dev-node-remotedesktop" urlDisplayName="Enable Remote Desktop" pageTitle="Enable remote desktop for cloud services (Node.js)" metaKeywords="Azure Node.js remote access, Azure Node.js remote connection, Azure Node.js VM access, Azure Node.js virtual machine access" description="Learn how to enable remote-desktop access for the virtual machines hosting your Azure Node.js application. " metaCanonical="" services="cloud-services" documentationCenter="nodejs" title="Enabling Remote Desktop in Azure" authors="larryfr" solutions="" manager="" editor="" />

<tags ms.service="cloud-services" ms.workload="tbd" ms.tgt_pltfrm="na" ms.devlang="nodejs" ms.topic="article" ms.date="09/17/2014" ms.author="larryfr"></tags>

# Habilitación de Escritorio remoto en Azure

Escritorio remoto le permite tener acceso al escritorio de una instancia de rol que se
ejecuta en Azure. Puede usar una conexión a Escritorio remoto para
configurar la máquina virtual o solucionar problemas con
la aplicación.

<div class="dev-callout">
    <b>Nota:</b>
    <p>Los pasos descritos en este art&iacute;culo solo se aplican a las aplicaciones Node hospedadas como un servicio en la nube de Azure.</p>
    </div>

Esta tarea incluye los siguientes pasos:

-   [Paso 1: Configuración del servicio para el acceso de Escritorio remoto mediante Azure PowerShell][Paso 1: Configuración del servicio para el acceso de Escritorio remoto mediante Azure PowerShell]
-   [Paso 2: Conexión a la instancia de rol][Paso 2: Conexión a la instancia de rol]
-   [Paso 3: Configuración del servicio para deshabilitar el acceso de Escritorio remoto
    mediante Azure PowerShell][Paso 3: Configuración del servicio para deshabilitar el acceso de Escritorio remoto
    mediante Azure PowerShell]

## <a name="step1"> </a>Paso 1: Configuración del servicio para el acceso de Escritorio remoto mediante Azure PowerShell

Para usar Escritorio remoto, necesita configurar la definición y la configuración del
servicio con un nombre de usuario, una contraseña y un certificado para
autenticarse con instancias de rol en la nube. [Azure PowerShell][Azure PowerShell] incluye el cmdlet **Enable-AzureServiceProjectRemoteDesktop**, que realiza
esta configuración en lugar del usuario.

Ejecute los siguientes pasos desde el equipo en que se ha creado la
definición de servicio.

1.  En el menú **Inicio**, seleccione **Azure PowerShell**.

    ![Entrada Azure PowerShell del menú Inicio][Entrada Azure PowerShell del menú Inicio]

2.  Cambie el directorio al directorio de servicio,
    **Enable-AzureServiceProjectRemoteDesktop** y, a continuación, escriba un nombre de usuario y una
    password contraseña para usarlos al autenticarse con las instancias de rol en la
    nube.

    ![enable-azureserviceprojectremotedesktop][enable-azureserviceprojectremotedesktop]

3.  Publique los cambios de la configuración del servicio en la nube. En el símbolo
    **Azure PowerShell**, escriba
    **Publish-AzureServiceProject**.

    ![publish-azureserviceproject][publish-azureserviceproject]

Cuando se hayan completado estos pasos, las instancias de rol del servicio
en la nube están configuradas para el acceso de Escritorio remoto.

## <a name="step2"> </a>Paso 2: Conexión a la instancia de rol

Con la implementación en funcionamiento en Azure, puede conectarse a
la instancia de rol.

1.  En el [Portal de administración de Azure][Portal de administración de Azure], seleccione **Servicios en la nube** y, a continuación, el servicio implementado en el paso 1 anterior.

    ![Portal de administración de Azure][1]

2.  Haga clic en **Instances** y, a continuación, haga clic en **Production** o **Staging** para ver las instancias del servicio. Seleccione una instancia y, a continuación, haga clic en **Connect** en la parte inferior de la página.

    ![La página Instancias][La página Instancias]

3.  Al hacer clic en **Conectar**, el explorador web le pide que guarde un
    archivo .rdp. Si usa Internet Explorer, haga clic en **Abrir**.

    ![solicitud para abrir o guardar el archivo .rdp][solicitud para abrir o guardar el archivo .rdp]

4.  Al abrir el archivo, aparece el siguiente indicador de seguridad:

    ![Indicador de seguridad de Windows][Indicador de seguridad de Windows]

5.  Haga clic en **Conectar** y, a continuación, aparecerá un indicador de seguridad para que escriba las
    credenciales de acceso a la instancia. Escriba la contraseña creada en
    el [paso 1][Paso 1: Configuración del servicio para el acceso de Escritorio remoto mediante Azure PowerShell] y haga clic en **Aceptar**.

    ![indicador de nombre de usuario/contraseña][indicador de nombre de usuario/contraseña]

Cuando se haya establecido la conexión, la conexión a Escritorio remoto muestra el
escritorio de la instancia en Azure. Ya ha obtenido acceso satisfactoriamente a la
instancia y puede ejecutar las tareas necesarias para
administrar la aplicación.

![Sesión de Escritorio remoto][Sesión de Escritorio remoto]

## <a name="step3"> </a>Paso 3: Configuración del servicio para deshabilitar el acceso de Escritorio remoto mediante Azure PowerShell

Cuando ya no necesite conexiones de Escritorio remoto a las instancias
de rol en la nube, deshabilite el acceso de Escritorio remoto mediante [Azure PowerShell][Azure PowerShell]

1.  En el menú **Inicio**, seleccione **Azure PowerShell**.

2.  Cambie el directorio al directorio de servicio y escriba
    **Disable-AzureServiceProjectRemoteDesktop**:

3.  Publique los cambios de la configuración del servicio en la nube. En el símbolo
    **Azure PowerShell**, escriba
    **Publish-AzureServiceProject**.

## Recursos adicionales

-   [Acceso remoto a las instancias de rol en Azure][Acceso remoto a las instancias de rol en Azure]
-   [Uso de Escritorio de remoto con los roles de Azure][Uso de Escritorio de remoto con los roles de Azure]

  [Paso 1: Configuración del servicio para el acceso de Escritorio remoto mediante Azure PowerShell]: #step1
  [Paso 2: Conexión a la instancia de rol]: #step2
  [Paso 3: Configuración del servicio para deshabilitar el acceso de Escritorio remoto
  mediante Azure PowerShell]: #step3
  [Azure PowerShell]: http://go.microsoft.com/?linkid=9790229&clcid=0x409
  [Entrada Azure PowerShell del menú Inicio]: ./media/cloud-services-nodejs-enable-remote-desktop/azure-powershell-menu.png
  [enable-azureserviceprojectremotedesktop]: ./media/cloud-services-nodejs-enable-remote-desktop/enable-rdp.png
  [publish-azureserviceproject]: ./media/cloud-services-nodejs-enable-remote-desktop/publish-rdp.png
  [Portal de administración de Azure]: http://manage.windowsazure.com
  [1]: ./media/cloud-services-nodejs-enable-remote-desktop/cloud-services-remote.png
  [La página Instancias]: ./media/cloud-services-nodejs-enable-remote-desktop/cloud-service-instance.png
  [solicitud para abrir o guardar el archivo .rdp]: ./media/cloud-services-nodejs-enable-remote-desktop/rdp-open.png
  [Indicador de seguridad de Windows]: ./media/cloud-services-nodejs-enable-remote-desktop/remote-desktop-12.png
  [indicador de nombre de usuario/contraseña]: ./media/cloud-services-nodejs-enable-remote-desktop/remote-desktop-13.png
  [Sesión de Escritorio remoto]: ./media/cloud-services-nodejs-enable-remote-desktop/remote-desktop-14.png
  [Acceso remoto a las instancias de rol en Azure]: http://msdn.microsoft.com/es-es/library/windowsazure/hh124107.aspx
  [Uso de Escritorio de remoto con los roles de Azure]: http://msdn.microsoft.com/es-es/library/windowsazure/gg443832.aspx
