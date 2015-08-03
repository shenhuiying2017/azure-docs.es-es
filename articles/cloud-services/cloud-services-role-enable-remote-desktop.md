<properties 
pageTitle="Configuración de una conexión a Escritorio remoto para un rol de servicios en la nube de Azure" 
description="Configuración de la aplicación de servicios en la nube de Azure para permitir conexiones a Escritorio remoto" 
services="cloud-services" 
documentationCenter="" 
authors="Thraka" 
manager="timlt" 
editor=""/>
<tags 
ms.service="cloud-services" 
ms.workload="tbd" 
ms.tgt_pltfrm="na" 
ms.devlang="na" 
ms.topic="article" 
ms.date="07/06/2015" 
ms.author="adegeo"/>

# Configuración de una conexión a Escritorio remoto para un rol de Azure
Después de crear un servicio en la nube que ejecuta su aplicación, puede tener acceso remoto a una instancia de rol en esa aplicación para configurar valores o solucionar problemas. El servicio en la nube debe estar configurado para Escritorio remoto.

* ¿Necesita un certificado para habilitar la autenticación de Escritorio remoto? [Cree](cloud-services-certs-create.md)uno y configúrelo a continuación.
* ¿Ya tiene una configuración de Escritorio remoto para el servicio en la nube? [Conéctese](#remote-into-role-instances) al servicio en la nube.

## Configuración de una conexión a Escritorio remoto para roles web o de trabajo
Para habilitar una conexión a Escritorio remoto para un rol web o de trabajo, puede configurar la conexión en el modelo de servicio de la aplicación, o puede usar el Portal de administración de Azure para configurar la conexión después de que las instancias están en ejecución.

### Configuración de la conexión en el modelo de servicio
El elemento **Imports** debe agregarse al archivo de definición de servicio que importa los módulos **RemoteAccess** y **RemoteForwarder** en el modelo de servicio. Al crear un proyecto de Azure mediante Visual Studio, los archivos del modelo de servicio se crean automáticamente.

El modelo de servicio consta de los archivos [ServiceDefinition.csdef](cloud-services-model-and-package.md#csdef) y [ServiceConfiguration.cscfg](cloud-services-model-and-package.md#cscfg). El archivo de definición se empaqueta con los archivos binarios de rol cuando la aplicación del servicio en la nube está preparada para la implementación. El archivo ServiceConfiguration.cscfg se implementa con el paquete de aplicación y se usa en Azure para determinar cómo se debe ejecutar la aplicación.

Después de crear el proyecto, puede usar Visual Studio para [habilitar una conexión a Escritorio remoto](https://msdn.microsoft.com/library/gg443832.aspx).

Después de configurar la conexión, el archivo de definición de servicio debe parecerse al ejemplo siguiente con el elemento `<Imports>` agregado.

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

El archivo de configuración de servicio debe parecerse al ejemplo siguiente con los valores que proporcionó al configurar la conexión; observe los elementos `<ConfigurationSettings>` y `<Certificates>`:

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

Cuando [empaquete](cloud-services-model-and-package.md#cspkg) y [publique](cloud-services-how-to-create-deploy-portal.md) la aplicación, debe asegurarse de que la casilla **Habilitar Escritorio remoto para todos los Roles** esté seleccionada. [Este](https://msdn.microsoft.com/library/ff683672.aspx)artículo proporciona listas de tareas comunes relacionadas con el uso de Visual Studio y servicios en la nube.

### Configuración de la conexión en instancias en ejecución
En la página de configuración del servicio en la nube, puede habilitar o modificar la configuración de la conexión a Escritorio remoto. Para obtener más información, vea [Configuración del acceso remoto a instancias de rol](cloud-services-how-to-configure.md).




## Acceso remoto en instancias de rol
Para acceder a instancias de roles web o de trabajo, debe usar un archivo de protocolo de Escritorio remoto (RDP). Puede descargar el archivo desde el Portal de administración, o puede recuperarlo mediante programación.

### Descarga del archivo RDP a través del Portal de administración

Puede usar los siguientes pasos para recuperar el archivo RDP del Portal de administración y, luego, utilizar Conexión a Escritorio remoto para conectarse a la instancia con el archivo:

1.  En la página Instancias, seleccione la instancia y, luego, haga clic en **Conectar** en la barra de comandos.
2.  Haga clic en **Guardar** para guardar el archivo de protocolo de Escritorio remoto en el equipo local.
3.  Abra **Conexión a Escritorio remoto**, haga clic en **Mostrar opciones** y, luego, haga clic en **Abrir**.
4.  Vaya a la ubicación donde guardó el archivo RDP, seleccione el archivo, haga clic en **Abrir** y, luego, haga clic en **Conectar**. Siga las instrucciones para completar la conexión.

### Uso de PowerShell para obtener el archivo RDP
Puede usar el cmdlet [Get-AzureRemoteDesktopFile](https://msdn.microsoft.com/library/azure/dn495261.aspx) para recuperar el archivo RDP.

### Uso de Visual Studio para descargar el archivo RDP
En Visual Studio, puede usar el Explorador de servidores para crear una conexión a Escritorio remoto.

1.  En el Explorador de servidores, expanda el nodo **Azure\Servicios en la nube\[nombre del servicio en la nube]**.
2.  Expanda **Ensayo** o **Producción**.
3.  Expanda el rol individual.
4.  Haga clic con el botón derecho en una de las instancias de rol, haga clic en **Conectar con Escritorio remoto...** y, luego, escriba el nombre de usuario y la contraseña.

### Descarga del archivo RDP mediante programación a través de la API de REST de administración de servicios
Puede usar la operación de REST [Descargar archivo RDP](https://msdn.microsoft.com/library/jj157183.aspx) para descargar el archivo RDP. Luego, puede usar este archivo con Conexión a Escritorio remoto para tener acceso al servicio en la nube.

## Pasos siguientes
Puede que tenga que [empaquetar](cloud-services-model-and-package.md) o [cargar (implementar)](cloud-services-how-to-create-deploy-portal.md) su aplicación.

<!---HONumber=July15_HO4-->