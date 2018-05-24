---
title: Uso de bases de datos SQL en Azure Stack | Microsoft Docs
description: Aprenda a implementar las bases de datos SQL como un servicio en Azure Stack y los pasos para implementar de forma rápida el adaptador del proveedor de recursos de SQL Server.
services: azure-stack
documentationCenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/01/2018
ms.author: jeffgilb
ms.reviewer: jeffgo
ms.openlocfilehash: 20b289c16a73bd20ed020987116975c8abe893f0
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/16/2018
ms.locfileid: "34198582"
---
# <a name="use-sql-databases-on-microsoft-azure-stack"></a>Uso de bases de datos SQL en Microsoft Azure Stack
Use el proveedor de recursos de SQL Server de Azure Stack para exponer las bases de datos SQL como un servicio de Azure Stack. El servicio del proveedor de recursos SQL se ejecuta en la máquina virtual del proveedor de recursos SQL, que es una máquina virtual de núcleo de Windows Server.

## <a name="prerequisites"></a>requisitos previos
Hay varios requisitos previos que se deben cumplir antes de implementar el proveedor de recursos SQL de Azure Stack. Siga los pasos siguientes en un equipo que pueda acceder a la máquina virtual del punto de conexión con privilegios:

- Si aún no lo ha hecho, [registre Azure Stack](.\azure-stack-registration.md) en Azure para poder descargar elementos de Azure Marketplace.
- Agregue la máquina virtual de núcleo de Windows Server a Marketplace de Azure Stack mediante la descarga de la imagen **núcleo de Windows Server 2016**. Si tiene que instalar una actualización, puede colocar un único paquete .MSU en la ruta de acceso local de la dependencia. Si hay más de un archivo .MSU, se produce un error al instalar el proveedor de recursos SQL.
- Descargue el binario del proveedor de recursos SQL y ejecute el extractor automático para extraer el contenido en un directorio temporal. El proveedor de recursos tiene una compilación mínima correspondiente de Azure Stack. Asegúrese de descargar el binario correcto para la versión de Azure Stack que ejecuta:
    - Azure Stack versión 1802 (1.0.180302.1): [SQL RP versión 1.1.18.0](https://aka.ms/azurestacksqlrp1802).
    - Azure Stack versión 1712 (1.0.180102.3, 1.0.180103.2 o 1.0.180106.1 (sistemas integrados)): [SQL RP versión 1.1.14.0](https://aka.ms/azurestacksqlrp1712).
- En instalaciones de sistemas integrados exclusivamente, debe proporcionar el certificado PKI de PaaS de SQL como se describe en la sección Certificados de PaaS opcionales de [Requisitos de infraestructura de clave pública para la implementación de Azure Stack
](.\azure-stack-pki-certs.md#optional-paas-certificates), al colocar el archivo .pfx en la ubicación especificada por el parámetro **DependencyFilesLocalPath**.
- Asegúrese de que tiene instalada la [versión más reciente de PowerShell de Azure Stack](.\azure-stack-powershell-install.md) (v1.2.11). 

## <a name="deploy-the-sql-resource-provider"></a>Implementar el proveedor de recursos SQL
Después de haberse preparado correctamente para instalar el proveedor de recursos SQL al cumplir todos los requisitos previos, ahora puede ejecutar el script **DeploySqlProvider.ps1** para implementar el proveedor de recursos SQL. El script DeploySqlProvider.ps1 se extrae como parte del binario del proveedor de recursos SQL descargado correspondiente a la versión de Azure Stack. 

> [!IMPORTANT]
> El script se debe ejecutar en un sistema con Windows 10 o Windows Server 2016 con la versión más reciente del runtime de .NET instalada.


Para implementar el proveedor de recursos SQL, abra una consola de PowerShell con privilegios elevados (administrativos) y cambie al directorio en el que ha extraído los archivos binarios del proveedor de recursos SQL.

> [!NOTE]
> Use una nueva ventana de consola de PowerShell para evitar problemas que puedan surgir de los módulos de PowerShell incorrectos que ya están cargados en el sistema.

Ejecute el script DeploySqlProvider.ps1, que lleva a cabo estos pasos:
- Carga los certificados y los demás artefactos en una cuenta de almacenamiento de Azure Stack.
- Publica los paquetes de la galería para poder implementar las bases de datos SQL a través de esta.
- Publica un paquete de galería para implementar los servidores de hospedaje.
- Implementa una VM con la imagen de Windows Server 2016 que creó en el paso 1 y, a continuación, instala el proveedor de recursos.
- Registra un registro de DNS local que se asigna a la VM del proveedor de recursos.
- Registra el proveedor de recursos en la instancia local de Azure Resource Manager (usuario y administrador).
- Opcionalmente, instala una única actualización de Windows durante la instalación del proveedor de recursos.

La implementación del proveedor de recursos SQL comienza y crea el grupo de recursos system.local.sqladapter. Las cuatro implementaciones necesarias de este grupo de recursos pueden tardar hasta 75 minutos en finalizar.

### <a name="deploysqlproviderps1-parameters"></a>Parámetros de DeploySqlProvider.ps1
Puede especificar estos parámetros en la línea de comandos. Si no lo hace, o se produce un error en la validación de algún parámetro, se le pedirá que proporcione los parámetros requeridos.

| Nombre de parámetro | DESCRIPCIÓN | Comentario o valor predeterminado |
| --- | --- | --- |
| **CloudAdminCredential** | Credencial del administrador de la nube, necesaria para el acceso al punto de conexión con privilegios. | _Obligatorio_ |
| **AzCredential** | Credenciales de la cuenta de administrador de servicio de Azure Stack. Use las mismas credenciales que para la implementación de Azure Stack. | _Obligatorio_ |
| **VMLocalCredential** | Credenciales para la cuenta de administrador local de la VM del proveedor de recursos de SQL. | _Obligatorio_ |
| **PrivilegedEndpoint** | Dirección IP o nombre DNS del punto de conexión con privilegios. |  _Obligatorio_ |
| **DependencyFilesLocalPath** | El archivo .pfx de certificados se debe colocar también en este directorio. | _Opcional_ (_obligatorio_ para sistemas integrados) |
| **DefaultSSLCertificatePassword** | Contraseña para el certificado .pfx. | _Obligatorio_ |
| **MaxRetryCount** | Número de veces que quiere volver a intentar cada operación si se produce un error.| 2 |
| **RetryDuration** | Intervalo de tiempo de expiración entre reintentos, en segundos. | 120 |
| **Desinstalación** | Se quita el proveedor de recursos y todos los recursos asociados (vea las notas siguientes). | Sin  |
| **DebugMode** | Impide la limpieza automática en caso de error. | Sin  |

>[!NOTE]
> Las SKU pueden tardar hasta una hora en estar visibles en el portal. No puede crear una base de datos hasta que se cree la SKU.


## <a name="deploy-the-sql-resource-provider-using-a-custom-script"></a>Implementar el proveedor de recursos SQL con un script personalizado
Para evitar la especificación manual de la información necesaria cuando se ejecuta el script DeploySqlProvider.ps1, puede personalizar el siguiente ejemplo de script si cambia la información de cuenta predeterminada y las contraseñas según sea necesario:

```powershell
# Install the AzureRM.Bootstrapper module, set the profile, and install the AzureRM and AzureStack modules.
Install-Module -Name AzureRm.BootStrapper -Force
Use-AzureRmProfile -Profile 2017-03-09-profile
Install-Module -Name AzureStack -RequiredVersion 1.2.11 -Force

# Use the NetBIOS name for the Azure Stack domain. On the Azure Stack SDK, the default is AzureStack but could have been changed at install time.
$domain = "AzureStack"

# For integrated systems, use the IP address of one of the ERCS virtual machines
$privilegedEndpoint = "AzS-ERCS01"

# Point to the directory where the resource provider installation files were extracted.
$tempDir = 'C:\TEMP\SQLRP'

# The service admin account (can be Azure Active Directory or Active Directory Federation Services).
$serviceAdmin = "admin@mydomain.onmicrosoft.com"
$AdminPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force
$AdminCreds = New-Object System.Management.Automation.PSCredential ($serviceAdmin, $AdminPass)

# Set credentials for the new resource provider VM local administrator account
$vmLocalAdminPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force
$vmLocalAdminCreds = New-Object System.Management.Automation.PSCredential ("sqlrpadmin", $vmLocalAdminPass)

# And the cloudadmin credential that's required for privileged endpoint access.
$CloudAdminPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force
$CloudAdminCreds = New-Object System.Management.Automation.PSCredential ("$domain\cloudadmin", $CloudAdminPass)

# Change the following as appropriate.
$PfxPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force

# Change directory to the folder where you extracted the installation files.
# Then adjust the endpoints.
. $tempDir\DeploySQLProvider.ps1 -AzCredential $AdminCreds `
  -VMLocalCredential $vmLocalAdminCreds `
  -CloudAdminCredential $cloudAdminCreds `
  -PrivilegedEndpoint $privilegedEndpoint `
  -DefaultSSLCertificatePassword $PfxPass `
  -DependencyFilesLocalPath $tempDir\cert
 ```

## <a name="verify-the-deployment-using-the-azure-stack-portal"></a>Comprobación de la implementación mediante Azure Stack Portal
Los pasos de esta sección se pueden usar para asegurarse de que el proveedor de recursos SQL se haya implementado correctamente.

> [!NOTE]
>  Cuando finalice la ejecución del script de instalación, debe actualizar el portal para ver la hoja de administración y los elementos de la galería.

1. Inicie sesión en el portal de administración como administrador de servicios.

2. Compruebe que la implementación se realizó correctamente. Vaya a **Grupos de recursos**. A continuación, seleccione el grupo de recursos **system.\<ubicación\>.sqladapter**. Compruebe que las cuatro implementaciones se realizaron correctamente.

      ![Comprobar la implementación del proveedor de recursos de SQL](./media/azure-stack-sql-rp-deploy/sqlrp-verify.png)


## <a name="next-steps"></a>Pasos siguientes

[Add hosting servers](azure-stack-sql-resource-provider-hosting-servers.md) (Agregar servidores de hospedaje)
