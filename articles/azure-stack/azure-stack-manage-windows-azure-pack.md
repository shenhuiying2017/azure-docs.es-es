---
title: "Administrar máquinas virtuales de Windows Azure Pack desde Azure Stack | Microsoft Docs"
description: "Aprenda cómo administrar las máquinas virtuales de Windows Azure Pack (WAP) desde el portal de usuarios en Azure Stack."
services: azure-stack
documentationcenter: 
author: walterov
manager: byronr
editor: 
ms.assetid: 213c2792-d404-4b44-8340-235adf3f8f0b
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/25/2017
ms.author: walterov
ms.openlocfilehash: b07a18055d149e20cd605a892063eccecf3df8a4
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="manage-windows-azure-pack-virtual-machines-from-azure-stack"></a>Administrar máquinas virtuales de Windows Azure Pack desde Azure Stack

*Se aplica a: Azure Stack Development Kit*

En Azure Stack Development Kit, puede habilitar el acceso desde el portal de usuarios de Azure Stack a las máquinas virtuales de inquilinos que se ejecutan en Windows Azure Pack. Los usuarios pueden usar el portal de Azure Stack para administrar sus máquinas virtuales y redes virtuales IaaS existentes. Estos recursos están disponibles en Windows Azure Pack a través de los componentes subyacentes Service Provider Foundation (SPF) y Virtual Machine Manager (VMM). En concreto, los usuarios pueden:

* Examinar los recursos.
* Examinar los valores de configuración.
* Detener o iniciar una máquina virtual.
* Conectarse a una máquina virtual a través del Protocolo de escritorio remoto (RDP).
* Crear y administrar puntos de control.
* Eliminar máquinas virtuales y redes virtuales.

Esta funcionalidad se proporciona mediante el conector de Windows Azure Pack para Azure Stack (versión preliminar). En este artículo se muestra cómo configurar el conector para un entorno de Azure Stack de nodo único.

Para esta versión preliminar del conector, tenga en cuenta lo siguiente:
* Use el conector de Windows Azure Pack únicamente en entornos de prueba (de Azure Stack y Windows Azure Pack) y no en las implementaciones de producción.
* Debe tener Windows Azure Pack Update Rollup (UR) 9.1 o una versión posterior, SPF de System Center y VMM UR 9 o posterior.
* Los componentes VMM y SPF pueden ser System Center 2012 R2 o System Center 2016.
* Debe seguir los pasos de configuración tanto en Azure Stack como en Windows Azure Pack.
* Las instrucciones se aplican a los entornos distintos de Cloud Platform System (CPS).
* Para revisar los problemas conocidos, consulte [Microsoft Azure Stack troubleshooting](azure-stack-troubleshooting.md) (Solución de problemas de Microsoft Azure Stack).


## <a name="architecture"></a>Arquitectura
En el siguiente diagrama se muestran los componentes principales del conector de Windows Azure Pack.

![Componentes del conector de Windows Azure Pack](media/azure-stack-manage-wap/image1.png)

Tenga en cuenta los detalles siguientes:
* El portal de usuarios de Azure Stack tiene acceso a la información de recursos de ambas nubes (Azure Stack y Windows Azure Pack).
* El usuario debe tener una cuenta que sea válida en ambos entornos.
* El portal de usuarios de Azure Stack debe tener acceso de red a los componentes que se ejecutan en Windows Azure Pack.
* En la sección **WAP** del diagrama, puede ver los nuevos módulos del conector de Windows Azure Pack (WAP Extension y Connector) y la API de inquilino existente de Windows Azure Pack con los componentes SPF y VMM.


## <a name="identity-management"></a>Administración de identidades
La API de inquilino de Windows Azure Pack debe confiar en el servicio de token de seguridad (STS) de Azure Stack.

Cuando un usuario realiza una acción a través del portal de Azure Stack dirigida a los recursos de Windows Azure Pack, el portal usa la API de inquilino de Windows Azure Pack. Por lo tanto, el token de autenticación de usuario proporcionado debe proceder de un STS de confianza. Observe el diagrama siguiente:

![Diagrama de autenticación del conector de Windows Azure Pack](media/azure-stack-manage-wap/image2.png)

En el entorno del kit de desarrollo, Windows Azure Pack y Azure Stack tienen proveedores de identidad independientes. Los usuarios que tienen acceso a ambos entornos del portal de Azure Stack deben tener el mismo nombre principal de usuario (UPN) en ambos proveedores de identidades. Por ejemplo, la cuenta *azurestackadmin@azurestack.local* también debe existir en el STS de Windows Azure Pack. Si AD FS no está configurado para admitir las relaciones de confianza saliente, se establecerá la confianza de los componentes de Windows Azure Pack (API de inquilino) a la instancia de Azure Stack de AD FS.

## <a name="prerequisites"></a>Requisitos previos

### <a name="download-the-windows-azure-pack-connector"></a>Descargar el conector de Windows Azure Pack
En el [Centro de descarga de Microsoft](https://aka.ms/wapconnectorazurestackdlc), descargue el archivo .exe y extráigalo en el equipo local. Luego, copie el contenido en un equipo que pueda acceder a su entorno de Windows Azure Pack.

### <a name="deployment-option-requirement"></a>Requisito de opción de implementación
Para integrar con Windows Azure Pack, puede implementar Azure Stack mediante la opción de AD FS o la opción de Azure Active Directory.

### <a name="connectivity-requirements"></a>Requisitos de conectividad
1. En el equipo en el que tiene acceso al portal de usuarios de Azure Stack, asegúrese de que puede acceder al portal de inquilinos de Windows Azure Pack a través del explorador web.
2. La máquina virtual AzS-WASP01 en Azure Stack debe ser capaz de conectarse al equipo del portal de inquilinos de Windows Azure Pack. Use Ping.exe para comprobar la conectividad de red. 
3.  Debe tener certificados válidos para los nuevos servicios del conector. Estos certificados SSL deben emitirlos una entidad de certificación (CA) de confianza. No puede usar certificados autofirmados. Los certificados SSL deben ser de confianza en Azure Stack (específicamente la máquina virtual de AzS-WASP01) y en cualquier otro equipo que el inquilino pueda usar para acceder al portal de usuarios de Azure Stack.
 
    >[!NOTE]
    Dado que AzS-WASP01 ejecuta Windows Server con la opción de instalación Server Core, puede usar una herramienta de línea de comandos, como Certutil.ext, para importar el certificado. Por ejemplo, podría copiar el archivo .cer en c:\temp en AzS-WASP01 y luego ejecutar el comando **certutil -addstore "CA" "c:\temp\certname.cer"**.

4.  Para establecer la conectividad RDP para máquinas virtuales de inquilino de Windows Azure Pack a través del portal de Azure Stack, el entorno de Windows Azure Pack debe permitir el tráfico de Escritorio remoto a las máquinas virtuales de inquilino.
5.  Para la conectividad entre máquinas virtuales de inquilino de Azure Stack y máquinas virtuales de inquilino de Windows Azure Pack, sus direcciones IP externas deben poder enrutarse a través de los dos entornos. Esta conectividad podría incluir también la asociación de un servidor DNS para resolver los nombres de las máquinas virtuales entre los entornos.

### <a name="iis-requirements"></a>Requisitos de IIS
El equipo que hospeda el portal de inquilino de Windows Azure Pack (que puede ser un host físico, una máquina virtual o varias máquinas virtuales) debe tener instalada la extensión de IIS URL Rewrite. Si aún no está instalada, puede descargarla [aquí](https://www.iis.net/downloads/microsoft/url-rewrite). Si varias máquinas virtuales hospedan el portal de inquilinos, instale la extensión en cada máquina virtual.

## <a name="configure-azure-stack"></a>Configurar Azure Stack
Antes de configurar el conector de Windows Azure Pack, debe habilitar el modo de varias nubes en el portal de usuarios de Azure Stack. Este modo permite a los usuarios seleccionar la nube desde la que se accederá a los recursos.

Para habilitar el modo de varias nubes, debe ejecutar el script Add-AzurePackConnector.ps1 después de la implementación de Azure Stack. En la siguiente tabla se describen los parámetros del script.


|  Parámetro | Description | Ejemplo |   
| -------- | ------------- | ------- |  
| AzurePackClouds | Los URI de los conectores de Windows Azure Pack. Estos URI deben corresponder a los portales de inquilinos de Windows Azure Pack. | @{CloudName = "AzurePack1"; CloudEndpoint = "https://waptenantportal1:40005"},@{CloudName = "AzurePack2"; CloudEndpoint = "https://waptenantportal2:40005"}<br><br>  (De manera predeterminada, el valor del puerto es 40005). |  
| AzureStackCloudName | Etiqueta para representar la nube local de Azure Stack.| "AzureStack" |
| DisableMultiCloud | Un conmutador para deshabilitar el modo de varias nubes.| N/D |
| | |

Puede ejecutar el script Add-AzurePackConnector.ps1 inmediatamente después de la implementación o más adelante. Para ejecutar el script inmediatamente después de la implementación, use la misma sesión de Windows PowerShell donde se completó la implementación de Azure Stack. En caso contrario, puede abrir una nueva sesión de Windows PowerShell como administrador (con la cuenta azurestackadmin).

1. Ejecute el script Add-AzurePackConnector.ps1 mediante los comandos siguientes (con los valores específicos de su entorno). Tenga en cuenta que el script Add-AzurePackConnector le permite agregar más de un punto de conexión del conector de Windows Azure Pack.
 
 ```powershell
    $cred = New-Object System.Management.Automation.PSCredential("cloudadmin@azurestack.local", `
    (ConvertTo-SecureString -String "<password>" -AsPlainText -Force))
    $session = New-PSSession -ComputerName 'azs-ercs01' `
     -Credential $cred `
     -ConfigurationName PrivilegedEndpoint `
     -Authentication Credssp

    # Enable Multicloud
    Invoke-Command -Session $session -ScriptBlock { Add-AzurePackConnector -AzurePackClouds `
    @{CloudName = "AzurePack_1"; CloudEndpoint = "https://waptenantportal1:40005"},`
    @{CloudName = "AzurePack_2"; CloudEndpoint = "https://waptenantportal2:40005" } `
    -AzureStackCloudName "AzureStack" }

 ```
> [!NOTE]
> En la compilación actual hay un problema por el que, una vez que finaliza el script Add-AzurePackConnector, permanece en un bucle de sondeo durante un largo período (varios minutos) hasta que termina. Una vez que aparezca el mensaje **VERBOSE: Step 'Configure Azure Pack Connector' status: 'Success'**, puede detener el script o esperar hasta que se detenga por sí solo. Esto no supone ninguna diferencia, ya que la configuración ya se habrá completado correctamente.

2. Tome nota de los archivos de salida que genera este script, uno para cada entorno de Windows Azure Pack especificado. Los archivos se encuentran en: \\\su1fileserver\SU1_Infrastructure_1\AzurePackConnectorOutput. Estos archivos contienen la información necesaria para configurar los entornos de Windows Azure Pack de destino. Este archivo se pasa como un parámetro a un script más adelante en estas instrucciones. Este archivo contiene la siguiente información:

    * **AzurePackConnectorEndpoint**: contiene el punto de conexión al servicio del conector de Windows Azure Pack.
    * **AuthenticationIdentityProviderPartner** contiene el siguiente par de valores:
        * Certificado de firma de token de autenticación en la que debe confiar la API de inquilino de Windows Azure Pack para aceptar las llamadas de la extensión del portal de Azure Stack.

        * El dominio "kerberos" asociado con el certificado de firma. Por ejemplo: https://adfs.local.azurestack.global.external/adfs/c1d72562-534e-4aa5-92aa-d65df289a107/.

3.  Vaya a la carpeta que contiene los archivos de salida (\\su1fileserver\SU1_Infrastructure_1\AzurePackConnectorOutput) y copie los archivos en el equipo local. Los archivos tendrán un aspecto similar al siguiente: AzurePack-06-27-15-50.txt.

4.  Pruebe la configuración.

    a. Abra el explorador e inicie sesión en el portal de usuarios de Azure Stack (https://portal.local.azurestack.external/).
    
    b. Después de iniciar sesión como inquilino y de que se cargue el portal, verá errores sobre la imposibilidad de capturar las suscripciones o extensiones de la nube de Azure Pack. Haga clic en **Aceptar** para cerrar estos mensajes. (Estos mensajes de error desaparecerán después de configurar Windows Azure Pack).

    c. Observe la lista desplegable **Nube** situada en la esquina superior izquierda del portal.

    ![Selector de nube en la interfaz de usuario de Azure Stack](media/azure-stack-manage-wap/image3.png)

## <a name="configure-windows-azure-pack"></a>Configurar Windows Azure Pack
Solo para esta versión preliminar del conector, Windows Azure Pack se debe configurar manualmente.

>[!IMPORTANT]
Para esta versión preliminar, use el conector de Windows Azure Pack únicamente en entornos de prueba y no en implementaciones de producción.

1.  Instale los archivos MSI del conector en la máquina virtual del portal de inquilinos de Windows Azure Pack e instale los certificados. (Si tiene varias máquinas virtuales del portal de inquilinos, debe completar este paso en cada máquina virtual).

    a. En el Explorador de archivos, copie la carpeta **WAPConnector** (que descargó anteriormente) en una carpeta denominada **c:\temp** en la máquina virtual del portal de inquilinos.

    b. Abra una conexión de consola o RDP a la máquina virtual del portal de inquilinos.

    c. Cambie los directorios a **c:\temp\wapconnector\setup\scripts** y ejecute el script **Install-Connector.ps1** para instalar tres archivos MSI. No hay ningún parámetro obligatorio.

     ```powershell
    cd C:\temp\wapconnector\setup\scripts\

    .\Install-Connector.ps1
    ```
     d. Cambie los directorios a **c:\inetpub** y compruebe que se hayan instalado los tres nuevos sitios:

       * MgmtSvc-Connector

       * MgmtSvc-ConnectorExtension

       * MgmtSvc-ConnectorController

    e. Desde la misma carpeta **c:\temp\wapconnector\setup\scripts**, ejecute el script **Configure-Certificates.ps1** para instalar los certificados. De manera predeterminada, usará el mismo certificado que está disponible para el sitio del portal de inquilinos en Windows Azure Pack. Asegúrese de que se trata de un certificado válido (de confianza de la máquina virtual AzS-WASP01 de Azure Stack y de todos los equipos cliente que tienen acceso al portal de Azure Stack). En caso contrario, no funcionará la comunicación. (También se puede pasar explícitamente una huella digital del certificado como parámetro mediante el parámetro -Thumbprint).

     ```powershell
        cd C:\temp\wapconnector\setup\scripts\

        .\Configure-Certificates.ps1
    ```

    f. Para finalizar la configuración de estos tres servicios, ejecute el script **Configure-WapConnector.ps1** para actualizar los parámetros del archivo Web.config.

    |  Parámetro | Description | Ejemplo |   
    | -------- | ------------- | ------- |  
    | TenantPortalFQDN | FQDN del portal de inquilinos de Windows Azure Pack. | tenant.contoso.com | 
    | TenantAPIFQDN | FQDN de la API de inquilino de Windows Azure Pack. | tenantapi.contoso.com  |
    | AzureStackPortalFQDN | FQDN del portal de usuarios de Azure Stack. | portal.local.azurestack.external |
    | | |
    
     ```powershell
    .\Configure-WapConnector.ps1 -TenantPortalFQDN "tenant.contoso.com" `
         -TenantAPIFQDN "tenantapi.contoso.com" `
         -AzureStackPortalFQDN "portal.local.azurestack.external"
    ```
    g. Si tiene varias máquinas virtuales del portal de inquilinos, repita el paso 1 para cada una de ellas.

2. Instale el nuevo MSI de la API de inquilino en cada máquina virtual de la API de inquilino de Windows Azure Pack.

    a. Si está usando un equilibrador de carga, quizás quiera marcar la máquina virtual como sin conexión.

    b. En el Explorador de archivos, copie la carpeta **WAPConnector** a una carpeta denominada **c:\temp** en cada máquina de la API de inquilino.

    c. Copie el archivo AzurePackConnectorOutput.txt que guardó anteriormente en **c:\temp\WAPConnector**.

    d. Abra una conexión de consola o RDP a la máquina virtual de la API de inquilino en la que copió los archivos.
    
    e. Cambie los directorios a **c:\temp\wapconnector\setup\scripts** y ejecute **Update-TenantAPI.ps1**. Esta nueva versión de la API de inquilino de WAP contiene un cambio para permitir una relación de confianza no solo con el STS actual, sino también con la instancia de AD FS en Azure Stack.

     ```powershell
    cd C:\temp\wapconnector\setup\packages\

    .\Update-TenantAPI.ps1
    ```

    f.  Repita el paso 2 en todas las demás máquinas virtuales que ejecuten la API de inquilino.
3. Desde **sola una**  de las máquinas virtuales de la API de inquilino, ejecute el script Configure-TrustAzureStack.ps1 para agregar una relación de confianza entre la API de inquilino y la instancia de AD FS en Azure Stack. Debe usar una cuenta con acceso de administrador del sistema a la base de datos Microsoft.MgmtSvc.Store. Este script tiene los parámetros siguientes:

    | Parámetro | Description | Ejemplo |
    | --------- | ------------| ------- |
   | SqlServer | Nombre de la instancia de SQL Server que contiene la base de datos Microsoft.MgmtSvc.Store. Este parámetro es obligatorio. | SQLServer | 
   | DataFile | Archivo de salida que se generó durante la configuración del modo de varias nubes de Azure Stack con anterioridad. Este parámetro es obligatorio. | AzurePack-06-27-15-50.txt | 
   | PromptForSqlCredential | Indica que el script debe solicitarle de manera interactiva una credencial de autenticación de SQL que se utilizará al conectarse a la instancia de SQL Server. La credencial proporcionada debe tener los permisos suficientes para desinstalar bases de datos y esquemas, así como para eliminar inicios de sesión de usuario. Si no se proporciona ninguno, el script supone que el contexto de usuario actual tiene acceso. | No se necesita ningún valor. |
   |  |  |

    Si no conoce la instancia de SQL Server que se debe usar, puede averiguarla. Conéctese al equipo de la API de inquilino, use el comando Unprotect-MgmtSvc para desproteger el archivo Web.config de la API de inquilino, y busque el nombre del servidor en la cadena de conexión. No olvide ejecutar Protect-MgmtSvc nuevamente para proteger el archivo Web.config de la API de inquilino.

  ```powershell
  cd C:\temp\wapconnector\setup\scripts\

 .\Configure-TrustAzureStack.ps1 -SqlServer "SQLServer" `
       -DataFile "C:\temp\wapconnector\AzurePackConnectorOutput.txt"
  ```

## <a name="example"></a>Ejemplo
En el ejemplo siguiente se muestra una implementación completa del conector de Windows Azure Pack en una configuración de Azure Stack de nodo único y dos instalaciones rápidas de Windows Azure Pack. (Cada instalación rápida se encuentra en un único equipo, con los nombres de ejemplo *wapcomputer1* y *wapcomputer2*).

```powershell
# Run the following script on the Azure Stack host
$cred = New-Object System.Management.Automation.PSCredential("cloudadmin@azurestack.local",`
     (ConvertTo-SecureString -String "p@ssw0rd" -AsPlainText -Force))
$session = New-PSSession -ComputerName 'azs-ercs01' -Credential $cred `
     -ConfigurationName PrivilegedEndpoint -Authentication Credssp
 
# Enable Multicloud
invoke-command -Session $session -ScriptBlock { Add-AzurePackConnector -AzurePackClouds `
     @{CloudName = "AzurePack_1"; CloudEndpoint = "https://wapcomputer1.contoso.com:40005"},`
     @{CloudName = "AzurePack_2"; CloudEndpoint = "https://wapcomputer2.contoso.com:40005"}`
     -AzureStackCloudName "AzureStack" }  

```
Descargue el archivo .exe desde el [Centro de descarga de Microsoft](https://aka.ms/wapconnectorazurestackdlc), extráigalo y copie la carpeta WAPConnector a una carpeta **c:\temp** en el equipo de Windows Azure Pack. Copie los archivos que se generaron como salida en el script anterior (ubicado en \\\su1fileserver\SU1_Infrastructure_1\AzurePackConnectorOutput) a la carpeta **c:\temp\WAPConnector**. (Los archivos tendrán un aspecto similar al siguiente: AzurePack-06-27-15-50.txt). A continuación, ejecute el siguiente script, una vez por instancia de Windows Azure Pack:

 ```powershell
# Install Connector components
cd C:\temp\WAPConnector\Setup\Scripts
.\Install-Connector.ps1

# Configure Certificates for the new Connector services
.\Configure-Certificates.ps1

# Configure the Connector services
.\Configure-WapConnector.ps1 -TenantPortalFQDN "wapcomputer1.contoso.com" `
     -TenantAPIFQDN "wapcomputer1.contoso.com" `
     -AzureStackPortalFQDN "portal.local.azurestack.external"

# Install the updated TenantAPI
.\Update-TenantAPI.ps1

# Establish trust with the Azure Stack AD FS
.\Configure-TrustAzureStack.ps1 -SqlServer "wapcomputer1" `
     -DataFile "C:\temp\wapconnector\AzurePack-06-27-15-50.txt" 

```
## <a name="troubleshooting-tips"></a>Sugerencias de solución de problemas
1.  Asegúrese de que haya conectividad de red entre Azure Stack y Windows Azure Pack. Esta conectividad debe ser entre cualquier equipo de inquilino que tenga acceso al portal de Azure Stack y la máquina virtual del portal de inquilinos de Windows Azure Pack donde se ejecutan los nuevos servicios del conector.
2.  Asegúrese de que todos los FQDN especificados sean correctos.
3.  Asegúrese de que los certificados SSL usados en los nuevos servicios del conector sean de confianza de Azure Stack (específicamente la máquina virtual AzS-WASP01) y de cualquier otro equipo que el inquilino pueda usar para acceder al portal de usuarios de Azure Stack.
4. Para revisar los problemas conocidos, vea [Microsoft Azure Stack troubleshooting](azure-stack-troubleshooting.md) (Solución de problemas de Microsoft Azure Stack).


## <a name="next-steps"></a>Pasos siguientes
[Usar los portales de administración y de usuarios en Azure Stack](azure-stack-manage-portals.md)
