---
title: Antes de implementar App Service en Azure Stack | Microsoft Docs
description: Pasos necesarios para implementar App Service en Azure Stack
services: azure-stack
documentationcenter: 
author: apwestgarth
manager: stefsch
editor: 
ms.assetid: 
ms.service: azure-stack
ms.workload: app-service
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/17/2017
ms.author: anwestg
ms.openlocfilehash: d4398d1c292548b08d91d70a8ba35b31234c5d5f
ms.sourcegitcommit: 3fca41d1c978d4b9165666bb2a9a1fe2a13aabb6
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/15/2017
---
# <a name="before-you-get-started-with-app-service-on-azure-stack"></a>Antes de empezar a trabajar con App Service en Azure Stack

Antes de implementar Azure App Service en Azure Stack, debe completar los requisitos previos de este artículo.

## <a name="download-the-installer-and-helper-scripts"></a>Descarga de los scripts de la aplicación auxiliar y el instalador

1. Descargue los [scripts de la aplicación auxiliar para la implementación de App Service en Azure Stack](https://aka.ms/appsvconmashelpers).
2. Descargue el [instalador de App Service en Azure Stack](https://aka.ms/appsvconmasinstaller).
3. Extraiga los archivos del archivo ZIP de scripts de la aplicación auxiliar. Aparecen los archivos y la estructura de carpetas siguientes:
   - Common.ps1
   - Create-AADIdentityApp.ps1
   - Create-ADFSIdentityApp.ps1
   - Create-AppServiceCerts.ps1
   - Get-AzureStackRootCert.ps1
   - Remove-AppService.ps1
   - Módulos
     - GraphAPI.psm1
    
## <a name="prepare-for-high-availability"></a>Preparación para lograr alta disponibilidad

Azure App Service en Azure Stack no puede ofrecer alta disponibilidad actualmente porque Azure Stack solo implementa cargas de trabajo en un dominio de error individual.

Para preparar Azure App Service en Azure Stack para que proporcione alta disponibilidad, implemente el servidor de archivos y la instancia de SQL Server necesarios en una configuración de alta disponibilidad. Si Azure Stack admite varios dominios de error, se proporcionará una guía acerca de cómo habilitar Azure App Service en Azure Stack en una configuración de alta disponibilidad.


## <a name="get-certificates"></a>Obtención de certificados

### <a name="certificates-required-for-the-azure-stack-development-kit"></a>Certificados necesarios para el Azure Stack Development Kit

Este primer script funciona con la entidad de certificación de Azure Stack para crear cuatro certificados que necesita App Service:

| Nombre de archivo | Uso |
| --- | --- |
| _.appservice.local.azurestack.external.pfx | Certificado SSL predeterminado de App Service |
| Api.appservice.local.azurestack.external.pfx | Certificado SSL de API de App Service |
| ftp.appservice.local.azurestack.external.pfx | Certificado SSL del publicador de App Service |
| Sso.appservice.local.azurestack.external.pfx | Certificado de aplicación de identidad de App Service |

Ejecute el script en el host del kit de desarrollo de Azure Stack y asegúrese de que está ejecutando PowerShell como azurestack\CloudAdmin:

1. En una sesión de PowerShell que se ejecuta como azurestack\AzureStackAdmin, ejecute el script Create-AppServiceCerts.ps1 desde la carpeta donde extrajo los scripts de la aplicación auxiliar. El script crea cuatro certificados en la misma carpeta que el script de creación de certificados que App Service necesita.
2. Escriba una contraseña para proteger los archivos .pfx y anótela. Debe escribirla en App Service en el instalador de Azure Stack.

#### <a name="create-appservicecertsps1-parameters"></a>Parámetros de Create-AppServiceCerts.ps1

| . | Obligatorio u opcional | Valor predeterminado | DESCRIPCIÓN |
| --- | --- | --- | --- |
| pfxPassword | Obligatorio | Null | Contraseña que ayuda a proteger la clave privada del certificado |
| DomainName | Obligatorio | local.azurestack.external | Región y sufijo de dominio de Azure Stack |

### <a name="certificates-required-for-a-production-deployment-of-azure-app-service-on-azure-stack"></a>Certificados necesarios para una implementación de producción de Azure App Service en Azure Stack

Para que funcione el proveedor de recursos de producción, debe proporcionar los siguientes cuatro certificados.

#### <a name="default-domain-certificate"></a>Certificado de dominio predeterminado

El certificado de dominio predeterminado se coloca en el rol de Front-End. Las aplicaciones del usuario lo utilizan para solicitudes de dominio comodín o predeterminado para Azure App Service. El certificado también se utiliza para operaciones de control de código fuente (Kudu).

El certificado debe estar en formato .pfx y debe ser un certificado comodín con dos firmantes. Esto permite que el dominio predeterminado y el punto de conexión de SCM para las operaciones de control de código fuente se realicen mediante un certificado.

| Formato | Ejemplo |
| --- | --- |
| \*.appservice.\<region\>.\<DomainName\>.\<extension\> | \*.appservice.redmond.azurestack.external |
| \*.scm.appservice.<region>.<DomainName>.<extension> | \*.appservice.scm.redmond.azurestack.external |

#### <a name="api-certificate"></a>Certificado de API

El certificado de la API se coloca en el rol de administración. El proveedor de recursos lo usa para ayudar a proteger las llamadas a la API. El certificado para publicar debe contener un firmante que coincida con la entrada DNS de la API.

| Formato | Ejemplo |
| --- | --- |
| api.appservice.\<region\>.\<DomainName\>.\<extension\> | api.appservice.redmond.azurestack.external |

#### <a name="publishing-certificate"></a>Certificado de publicador

El certificado para el rol de publicador protege el tráfico FTPS de los propietarios de aplicaciones cuando cargan contenido. El certificado para publicar debe contener un firmante que coincida con la entrada DNS de FTPS.

| Formato | Ejemplo |
| --- | --- |
| ftp.appservice.\<region\>.\<DomainName\>.\<extension\> | api.appservice.redmond.azurestack.external |

#### <a name="identity-certificate"></a>Certificado de identidad

El certificado para la aplicación de identidad permite:
- La integración entre Azure Active Directory (Azure AD) o el directorio de Active Directory Federation Services (AD FS), Azure Stack y App Service para admitir la integración con el proveedor de recursos de proceso.
- Escenarios de inicio de sesión único para herramientas de desarrollo avanzadas de Azure App Service en Azure Stack.

El certificado de identidad debe contener un firmante que coincida con el siguiente formato:

| Formato | Ejemplo |
| --- | --- |
| sso.appservice.\<region\>.\<DomainName\>.\<extension\> | sso.appservice.redmond.azurestack.external |

### <a name="azure-resource-manager-root-certificate-for-azure-stack"></a>Certificado raíz de Azure Resource Manager para Azure Stack

En una sesión de PowerShell que se ejecuta como azurestack\CloudAdmin, ejecute el script Get-AzureStackRootCert.ps1 desde la carpeta donde extrajo los scripts de la aplicación auxiliar. El script crea cuatro certificados en la misma carpeta que el script de creación de certificados que App Service necesita.

| Parámetro Get-AzureStackRootCert.ps1 | Obligatorio u opcional | Valor predeterminado | DESCRIPCIÓN |
| --- | --- | --- | --- |
| PrivelegedEndpoint | Obligatorio | AzS-ERCS01 | Punto de conexión con privilegios |
| CloudAdminCredential | Obligatorio | AzureStack\CloudAdmin | Credenciales de cuenta de dominio para administradores de nube de Azure Stack |


## <a name="prepare-the-file-server"></a>Preparación del servidor de archivos

Azure App Service necesita utilizar un servidor de archivos. Para las implementaciones de producción, se debe configurar el servidor de archivos para tener alta disponibilidad y ser capaz de administrar los errores.

Para su uso solo con las implementaciones del kit de desarrollo de Azure Stack, puede usar la [plantilla de implementación de Azure Resource Manager de ejemplo](https://aka.ms/appsvconmasdkfstemplate) para implementar un servidor de archivos de nodo único configurado. El servidor de archivos de nodo único estará en un grupo de trabajo.

### <a name="provision-groups-and-accounts-in-active-directory"></a>Aprovisionar grupos y cuentas en Active Directory


1. Crear los siguientes grupos de seguridad global de Active Directory:
   - FileShareOwners
   - FileShareUsers
2. Crear las siguientes cuentas de Active Directory como cuentas de servicio:
   - FileShareOwner
   - FileShareUser

   Como procedimiento recomendado de seguridad, los usuarios de estas cuentas (y para todos los roles de web) deben distinguirse entre sí y tener contraseñas y nombres de usuario seguros. Establezca las contraseñas con las condiciones siguientes:
   - Habilitar **La contraseña nunca expira**.
   - Habilitar **El usuario no puede cambiar la contraseña**.
   - Deshabilitar **El usuario debe cambiar la contraseña en el siguiente inicio de sesión**.
3. Agregue las cuentas a las pertenencias a grupos como sigue:
   - Agregar **FileShareOwner** al grupo **FileShareOwners**.
   - Agregar **FileShareUser** al grupo **FileShareUsers**.

### <a name="provision-groups-and-accounts-in-a-workgroup"></a>Aprovisionar grupos y cuentas en un grupo de trabajo

>[!NOTE]
> Cuando se está configurando un servidor de archivos, ejecute los siguientes comandos en una ventana de símbolo del sistema con privilegios administrativos. *No use PowerShell.*

Cuando se usa la plantilla de Azure Resource Manager, los usuarios ya están creados.

1. Ejecute los comandos siguientes para crear las cuentas FileShareOwner y FileShareUser. Reemplazar `<password>` por sus propios valores.
``` DOS
net user FileShareOwner <password> /add /expires:never /passwordchg:no
net user FileShareUser <password> /add /expires:never /passwordchg:no
```
2. Establezca las contraseñas de las cuentas para que no expiren nunca al ejecutar los siguientes comandos WMIC:
``` DOS
WMIC USERACCOUNT WHERE "Name='FileShareOwner'" SET PasswordExpires=FALSE
WMIC USERACCOUNT WHERE "Name='FileShareUser'" SET PasswordExpires=FALSE
```
3. Cree los grupos locales FileShareUsers y FileShareOwners y agrégueles las cuentas en el primer paso:
``` DOS
net localgroup FileShareUsers /add
net localgroup FileShareUsers FileShareUser /add
net localgroup FileShareOwners /add
net localgroup FileShareOwners FileShareOwner /add
```

### <a name="provision-the-content-share"></a>Aprovisionar el recurso compartido de contenido

El recurso compartido de contenido incluye el contenido del sitio web del inquilino. El procedimiento para aprovisionar el recurso compartido de contenido en un único servidor de archivos es el mismo para los entornos de Active Directory y del grupo de trabajo. Pero es diferente para un clúster de conmutación por error en Active Directory.

#### <a name="provision-the-content-share-on-a-single-file-server-active-directory-or-workgroup"></a>Aprovisionamiento del recurso compartido de contenido en un único servidor de archivos (Active Directory o grupo de trabajo)

En un único servidor de archivos, ejecute los siguientes comandos en un símbolo del sistema con privilegios elevados. Reemplace el valor de `C:\WebSites` por las rutas de acceso correspondientes de su entorno.

```DOS
set WEBSITES_SHARE=WebSites
set WEBSITES_FOLDER=C:\WebSites
md %WEBSITES_FOLDER%
net share %WEBSITES_SHARE% /delete
net share %WEBSITES_SHARE%=%WEBSITES_FOLDER% /grant:Everyone,full
```

### <a name="add-the-fileshareowners-group-to-the-local-administrators-group"></a>Adición del grupo FileShareOwners al grupo de administradores local

Para que Administración remota de Windows funcione correctamente, debe agregar el grupo FileShareOwners al grupo de administradores local.

#### <a name="active-directory"></a>Active Directory

Ejecute los comandos siguientes en un símbolo del sistema con privilegios elevados en el servidor de archivos o en cada servidor de archivos que actúa como un nodo del clúster de conmutación por error. Reemplace el valor de `<DOMAIN>` por el nombre de dominio que desea usar.

```DOS
set DOMAIN=<DOMAIN>
net localgroup Administrators %DOMAIN%\FileShareOwners /add
```

#### <a name="workgroup"></a>Grupo de trabajo

Ejecute el siguiente comando en un símbolo del sistema con privilegios elevados en el servidor de archivos:

```DOS
net localgroup Administrators FileShareOwners /add
```

### <a name="configure-access-control-to-the-shares"></a>Configurar el control de acceso a los recursos compartidos

Ejecute los comandos siguientes en un símbolo del sistema con privilegios elevados en el servidor de archivos o en el nodo del clúster de conmutación por error, que es el propietario actual del recurso del clúster. Reemplace los valores en cursiva por valores específicos de su entorno.

#### <a name="active-directory"></a>Active Directory
```DOS
set DOMAIN=<DOMAIN>
set WEBSITES_FOLDER=C:\WebSites
icacls %WEBSITES_FOLDER% /reset
icacls %WEBSITES_FOLDER% /grant Administrators:(OI)(CI)(F)
icacls %WEBSITES_FOLDER% /grant %DOMAIN%\FileShareOwners:(OI)(CI)(M)
icacls %WEBSITES_FOLDER% /inheritance:r
icacls %WEBSITES_FOLDER% /grant %DOMAIN%\FileShareUsers:(CI)(S,X,RA)
icacls %WEBSITES_FOLDER% /grant *S-1-1-0:(OI)(CI)(IO)(RA,REA,RD)
```

#### <a name="workgroup"></a>Grupo de trabajo
```DOS
set WEBSITES_FOLDER=C:\WebSites
icacls %WEBSITES_FOLDER% /reset
icacls %WEBSITES_FOLDER% /grant Administrators:(OI)(CI)(F)
icacls %WEBSITES_FOLDER% /grant FileShareOwners:(OI)(CI)(M)
icacls %WEBSITES_FOLDER% /inheritance:r
icacls %WEBSITES_FOLDER% /grant FileShareUsers:(CI)(S,X,RA)
icacls %WEBSITES_FOLDER% /grant *S-1-1-0:(OI)(CI)(IO)(RA,REA,RD)
```

## <a name="prepare-the-sql-server-instance"></a>Preparación de la instancia de SQL Server

Para que Azure App Service en Azure Stack hospede y mida bases de datos, debe preparar una instancia de SQL Server para almacenar las bases de datos de App Service.

Para implementaciones del Kit de desarrollo de Azure Stack, puede usar SQL Server Express 2014 SP2 o versiones posteriores.

Para entornos de producción y para ofrecer alta disponibilidad, debe usar una versión completa de SQL Server 2014 SP2 o versiones posteriores, habilitar la autenticación de modo mixto y realizar la implementación en una [configuración de alta disponibilidad](https://docs.microsoft.com/sql/sql-server/failover-clusters/high-availability-solutions-sql-server).

La instancia de SQL Server para Azure App Service en Azure Stack debe ser accesible desde todos los roles de App Service. SQL Server se puede implementar en la suscripción del proveedor predeterminada en Azure Stack. O bien, puede usar la infraestructura existente en su organización (siempre y cuando haya conectividad con Azure Stack). Si va a usar una imagen de Azure Marketplace, no olvide configurar el firewall adecuadamente. 

Para cualquiera de los roles de SQL Server, puede utilizar una instancia predeterminada o una instancia con nombre. Si usa una instancia con nombre, asegúrese de iniciar manualmente el servicio SQL Server Browser y abrir el puerto 1434.

## <a name="create-an-azure-active-directory-application"></a>Creación de una aplicación de Azure Active Directory

Configuración de una entidad de servicio de Azure AD para admitir lo siguiente:
- Integración del conjunto de escalado de máquinas virtuales en los niveles de trabajo
- Inicio de sesión único para las herramientas avanzadas del desarrollador y el portal de Azure Functions

Estos pasos se aplican solo a entornos de Azure Stack protegidos con Azure AD.

Los administradores deben configurar SSO para:
- Habilitar las herramientas avanzadas de desarrollador en App Service (Kudu).
- Habilitar el uso de la experiencia del portal de Azure Functions.

Siga estos pasos:

1. Abra una instancia de PowerShell como azurestack\AzureStackAdmin.
2. Vaya a la ubicación de los scripts descargados y extraídos en el [paso de requisitos previos](https://docs.microsoft.com/azure/azure-stack/azure-stack-app-service-before-you-get-started#download-the-azure-app-service-on-azure-stack-installer-and-helper-scripts).
3. [Instale PowerShell para Azure Stack](azure-stack-powershell-install.md).
4. Ejecute el script **Create-AADIdentityApp.ps1**. Cuando se le pida, escriba el identificador del inquilino de Azure AD que esté usando para la implementación de Azure Stack. Por ejemplo, escriba **myazurestack.onmicrosoft.com**.
5. En la ventana **Credencial**, escriba la cuenta y la contraseña de administrador del servicio de Azure AD. Seleccione **Aceptar**.
6. Escriba la contraseña y la ruta de acceso del archivo del [certificado creado anteriormente](https://docs.microsoft.com/en-gb/azure/azure-stack/azure-stack-app-service-before-you-get-started#certificates-required-for-azure-app-service-on-azure-stack). El certificado creado para este paso de forma predeterminada es **sso.appservice.local.azurestack.external.pfx**.
7. El script crea una nueva aplicación en la instancia de Azure AD del inquilino. Tome nota del identificador de la aplicación que se devuelve en la salida de PowerShell. Necesitará esta información durante la instalación.
8. Abra una nueva ventana del explorador e inicie sesión en [Azure Portal](https://portal.azure.com) como administrador del servicio Azure Active Directory.
9. Abra el proveedor de recursos de Azure AD.
10. Seleccione **Registros de aplicaciones**.
11. Busque el identificador de la aplicación devuelto como parte del paso 7. Se muestra una aplicación de App Service.
12. Seleccione **Aplicación** en la lista.
13. Seleccione **Permisos necesarios** > **Conceder permisos** > **Sí**.

| Parámetro Create-AADIdentityApp.ps1 | Obligatorio u opcional | Valor predeterminado | DESCRIPCIÓN |
| --- | --- | --- | --- |
| DirectoryTenantName | Obligatorio | Null | Identificador de inquilino de Azure AD. Proporcione el GUID o una cadena. Un ejemplo es myazureaaddirectory.onmicrosoft.com. |
| AdminArmEndpoint | Obligatorio | Null | Punto de conexión de Azure Resource Manager del administrador. Por ejemplo, adminmanagement.local.azurestack.external. |
| TenantARMEndpoint | Obligatorio | Null | Punto de conexión de Azure Resource Manager del inquilino. Por ejemplo, management.local.azurestack.external. |
| AzureStackAdminCredential | Obligatorio | Null | Credenciales del administrador del servicio de Azure AD. |
| CertificateFilePath | Obligatorio | Null | Ruta de acceso del archivo de certificado de la aplicación de identidad generado anteriormente. |
| CertificatePassword | Obligatorio | Null | Contraseña que ayuda a proteger la clave privada del certificado. |

## <a name="create-an-active-directory-federation-services-application"></a>Creación de una aplicación de Servicios de federación de Active Directory

Para entornos de Azure Stack protegidos por AD FS, debe configurar una entidad de servicio de AD FS para admitir lo siguiente:
- Integración del conjunto de escalado de máquinas virtuales en los niveles de trabajo
- Inicio de sesión único para las herramientas avanzadas del desarrollador y el portal de Azure Functions

Los administradores deben configurar SSO para:
- Configurar una entidad de servicio para la integración del conjunto de escalado de máquinas virtuales en los niveles de trabajo.
- Habilitar las herramientas avanzadas de desarrollador en App Service (Kudu).
- Habilitar el uso de la experiencia del portal de Azure Functions.

Siga estos pasos:

1. Abra una instancia de PowerShell como azurestack\AzureStackAdmin.
2. Vaya a la ubicación de los scripts descargados y extraídos en el [paso de requisitos previos](https://docs.microsoft.com/en-gb/azure/azure-stack/azure-stack-app-service-before-you-get-started#download-the-azure-app-service-on-azure-stack-installer-and-helper-scripts).
3. [Instale PowerShell para Azure Stack](azure-stack-powershell-install.md).
4.  Ejecute el script **Create-ADFSIdentityApp.ps1**.
5.  En la ventana **Credencial**, escriba la cuenta y la contraseña de administrador de la nube de AD FS. Seleccione **Aceptar**.
6.  Proporcione la ruta de acceso del archivo y la contraseña del [certificado creado anteriormente](https://docs.microsoft.com/en-gb/azure/azure-stack/azure-stack-app-service-before-you-get-started#certificates-required-for-azure-app-service-on-azure-stack). El certificado creado para este paso de forma predeterminada es **sso.appservice.local.azurestack.external.pfx**.

| Parámetro Create-ADFSIdentityApp.ps1 | Obligatorio u opcional | Valor predeterminado | DESCRIPCIÓN |
| --- | --- | --- | --- |
| AdminArmEndpoint | Obligatorio | Null | Punto de conexión de Azure Resource Manager del administrador. Por ejemplo, adminmanagement.local.azurestack.external. |
| PrivilegedEndpoint | Obligatorio | Null | Punto de conexión de acceso con privilegios. Por ejemplo, AzS-ERCS01. |
| CloudAdminCredential | Obligatorio | Null | Credencial de cuenta de dominio de administración en la nube de Azure Stack. Por ejemplo, Azurestack\CloudAdmin. |
| CertificateFilePath | Obligatorio | Null | Ruta de acceso al archivo PFX del certificado de la aplicación de identidad. |
| CertificatePassword | Obligatorio | Null | Contraseña que ayuda a proteger la clave privada del certificado. |


## <a name="next-steps"></a>pasos siguientes

[Instale el proveedor de recursos de App Service](azure-stack-app-service-deploy.md)
