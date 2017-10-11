---
title: "Habilitación de Escritorio remoto para un servicio Azure Cloud Services | Microsoft Docs"
description: "Configuración de la aplicación de servicios en la nube de Azure para permitir conexiones a Escritorio remoto"
services: cloud-services
documentationcenter: 
author: thraka
manager: timlt
editor: 
ms.assetid: d3110ee8-6526-4585-aba5-d0bc9a713e9b
ms.service: cloud-services
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/05/2017
ms.author: adegeo
ms.openlocfilehash: 413e72e9a39fcde84f56bfc61a6bc72dbadf1c97
ms.sourcegitcommit: 50e23e8d3b1148ae2d36dad3167936b4e52c8a23
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/18/2017
---
# <a name="enable-remote-desktop-connection-for-a-role-in-azure-cloud-services"></a>Habilitación de la conexión a Escritorio remoto para un rol de Azure Cloud Services

> [!div class="op_single_selector"]
> * [Azure Portal](cloud-services-role-enable-remote-desktop-new-portal.md)
> * [Portal de Azure clásico](cloud-services-role-enable-remote-desktop.md)
> * [PowerShell](cloud-services-role-enable-remote-desktop-powershell.md)
> * [Visual Studio](../vs-azure-tools-remote-desktop-roles.md)

Puede habilitar una conexión a Escritorio remoto en el rol durante el desarrollo mediante la inclusión de los módulos de Escritorio remoto en la definición de servicio, o puede habilitar Escritorio remoto a través de la extensión de Escritorio remoto. El método preferido es usar la extensión de Escritorio remoto dado que puede habilitar Escritorio remoto incluso después de que se implementa la aplicación sin tener que volver a implementarla.

## <a name="configure-remote-desktop-from-the-azure-classic-portal"></a>Configuración de Escritorio remoto desde el Portal de Azure clásico
El Portal de Azure clásico usa el método de extensión de Escritorio remoto, por lo que puede habilitar Escritorio remoto incluso después de que se implemente la aplicación. En la pagina **Configurar** de su servicio en la nube, puede habilitar Escritorio remoto, cambiar la cuenta de Administrador local usada para conectarse a las máquinas virtuales o el certificado que se usa en la autenticación y definir la fecha de caducidad.

1. Haga clic en **Cloud Services**, en el nombre del servicio en la nube y, a continuación, en **Configurar**.
2. Haga clic en el botón **Remoto** situado en la parte inferior.

    ![Servicios en la nube remotos](./media/cloud-services-role-enable-remote-desktop/CloudServices_Remote.png)

   > [!WARNING]
   > se reiniciarán todas las instancias de rol la primera vez que habilite el Escritorio remoto y haga clic en Aceptar (marca de verificación). Para evitar un reinicio, el certificado que se usó para cifrar la contraseña debe instalarse en el rol. Para evitar un reinicio, [cargue un certificado para el servicio en la nube](cloud-services-configure-ssl-certificate.md#step-3-upload-a-certificate) y, luego, vuelva a este cuadro de diálogo.

3. En **Roles**, seleccione el rol de servicio que desea actualizar o seleccione **Todos** para todos los roles.
4. Realice alguno de los siguientes cambios:

   * Para habilitar Escritorio remoto, seleccione la casilla **Habilitar Escritorio remoto** . Para deshabilitar el Escritorio remoto, desmarque la casilla.
   * Cree una cuenta para usarla en las conexiones de Escritorio remoto con las instancias de rol.
   * Actualice la contraseña de la cuenta existente.
   * Seleccione un certificado cargado para usar en la autenticación (cargue el certificado usando **Cargar** en la página **Certificados**) o cree un certificado nuevo.
   * Cambie la fecha de caducidad para la configuración de Escritorio remoto.

5. Después terminar las actualizaciones de la configuración, haga clic en **Aceptar** (marca de verificación).

## <a name="remote-into-role-instances"></a>Acceso remoto en instancias de rol
Una vez que Escritorio remoto está habilitado en los roles, puede conectarse de forma remota a una instancia de rol a través de varias herramientas.

Para conectarse a una instancia de rol desde el Portal de Azure clásico:

1. Haga clic en **Instancias** para abrir la página **Instancias**.
2. Seleccione una instancia de rol que tenga el Escritorio remoto configurado.
3. Haga clic en **Conectar**y siga las instrucciones para abrir el escritorio.
4. Haga clic en **Abrir** y, a continuación, en **Conectar** para iniciar la conexión del Escritorio remoto.

### <a name="use-visual-studio-to-remote-into-a-role-instance"></a>Uso de Visual Studio para conectarse de forma remota a una instancia de rol
En el Explorador de servidores de Visual Studio:

1. Expanda el nodo **Azure** > **Cloud Services** > **[nombre del servicio en la nube]**.
2. Expanda **Almacenamiento provisional** o **Producción**.
3. Expanda el rol individual.
4. Haga clic con el botón derecho en una de las instancias de rol, haga clic en **Conectar con Escritorio remoto...**y, luego, escriba el nombre de usuario y la contraseña.

![Escritorio remoto del Explorador de servidores](./media/cloud-services-role-enable-remote-desktop/ServerExplorer_RemoteDesktop.png)

### <a name="use-powershell-to-get-the-rdp-file"></a>Uso de PowerShell para obtener el archivo RDP
Puede usar el cmdlet [Get-AzureRemoteDesktopFile](https://msdn.microsoft.com/library/azure/dn495261.aspx) para recuperar el archivo RDP. Luego, puede usar este archivo con Conexión a Escritorio remoto para tener acceso al servicio en la nube.

### <a name="programmatically-download-the-rdp-file-through-the-service-management-rest-api"></a>Descarga del archivo RDP mediante programación a través de la API de REST de administración de servicios
Puede usar la operación de REST [Descargar archivo RDP](https://msdn.microsoft.com/library/jj157183.aspx) para descargar el archivo RDP.

## <a name="to-configure-remote-desktop-in-the-service-definition-file"></a>Para configurar Escritorio remoto en el archivo de definición de servicio
Este método permite habilitar Escritorio remoto para la aplicación durante el desarrollo. Este enfoque requiere el almacenamiento de contraseñas cifradas en el archivo de configuración de servicio y cualquier actualización a la configuración de Escritorio remoto requeriría que se volviera a implementar la aplicación. Si desea evitar estas desventajas, debe usar el enfoque de extensión de Escritorio remoto descrito anteriormente.  

Puede usar Visual Studio para [habilitar una conexión a Escritorio remoto](../vs-azure-tools-remote-desktop-roles.md) mediante el enfoque de archivo de definición de servicio.  
Los pasos siguientes describen los cambios necesarios en los archivos de modelo de servicio para habilitar Escritorio remoto. Visual Studio realizará estos cambios automáticamente al publicar.

### <a name="set-up-the-connection-in-the-service-model"></a>Configuración de la conexión en el modelo de servicio
Use el elemento **Importaciones** para importar el módulo **RemoteAccess** y el módulo **RemoteForwarder** para el archivo [ServiceDefinition.csdef](cloud-services-model-and-package.md#csdef).

El archivo de definición de servicio debe parecerse al ejemplo siguiente con el elemento `<Imports>` agregado.

```xml
<ServiceDefinition name="<name-of-cloud-service>" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceDefinition" schemaVersion="2013-03.2.0">
    <WebRole name="WebRole1" vmsize="Small">
        <Sites>
            <Site name="Web">
                <Bindings>
                    <Binding name="Endpoint1" endpointName="Endpoint1" />
                </Bindings>
            </Site>
        </Sites>
        <Endpoints>
            <InputEndpoint name="Endpoint1" protocol="http" port="80" />
        </Endpoints>
        <Imports>
            <Import moduleName="Diagnostics" />
            <Import moduleName="RemoteAccess" />
            <Import moduleName="RemoteForwarder" />
        </Imports>
    </WebRole>
</ServiceDefinition>
```
El archivo [ServiceConfiguration.cscfg](cloud-services-model-and-package.md#cscfg) debe parecerse al ejemplo siguiente; observe los elementos `<ConfigurationSettings>` y `<Certificates>`. El certificado especificado debe [cargarse en el servicio en la nube](cloud-services-how-to-create-deploy.md#how-to-upload-a-certificate-for-a-cloud-service).

```xml
<?xml version="1.0" encoding="utf-8"?>
<ServiceConfiguration serviceName="<name-of-cloud-service>" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceConfiguration" osFamily="3" osVersion="*" schemaVersion="2013-03.2.0">
    <Role name="WebRole1">
        <Instances count="2" />
        <ConfigurationSettings>
            <Setting name="Microsoft.WindowsAzure.Plugins.RemoteAccess.Enabled" value="true" />
            <Setting name="Microsoft.WindowsAzure.Plugins.RemoteAccess.AccountUsername" value="[name-of-user-account]" />
            <Setting name="Microsoft.WindowsAzure.Plugins.RemoteAccess.AccountEncryptedPassword" value="[base-64-encrypted-user-password]" />
            <Setting name="Microsoft.WindowsAzure.Plugins.RemoteAccess.AccountExpiration" value="[certificate-expiration]" />
            <Setting name="Microsoft.WindowsAzure.Plugins.RemoteForwarder.Enabled" value="true" />
        </ConfigurationSettings>
        <Certificates>
            <Certificate name="Microsoft.WindowsAzure.Plugins.RemoteAccess.PasswordEncryption" thumbprint="[certificate-thumbprint]" thumbprintAlgorithm="sha1" />
        </Certificates>
    </Role>
</ServiceConfiguration>
```


## <a name="additional-resources"></a>Recursos adicionales
[How to Configure Cloud Services](cloud-services-how-to-configure.md) (Configuración de Cloud Services)
[Cloud services FAQ - Remote Desktop](cloud-services-faq.md) (Preguntas frecuentes sobre Cloud Services > Escritorio remoto)
