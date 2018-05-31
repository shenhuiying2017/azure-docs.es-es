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
ms.openlocfilehash: 9154f509f9019c28515970869678aa6633d16163
ms.sourcegitcommit: c47ef7899572bf6441627f76eb4c4ac15e487aec
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/04/2018
ms.locfileid: "33206187"
---
# <a name="update-the-sql-resource-provider-adapter"></a>Actualización del adaptador del proveedor de recursos de SQL
Cuando las compilaciones de Azure Stack se actualicen, podría lanzarse un nuevo adaptador del proveedor de recursos de Azure SQL. Aunque el adaptador existente continúa funcionando, se recomienda actualizar a la compilación más reciente lo antes posible. Las actualizaciones deben instalarse en orden: no se pueden omitir versiones (consulte la lista de versiones en los [requisitos previos para implementar el proveedor de recursos](.\azure-stack-sql-resource-provider-deploy.md#prerequisites)).

Para actualizar el proveedor de recursos, use el script *UpdateMySQLProvider.ps1*. El procedimiento es similar al usado para instalar un proveedor de recursos, como se describe en [Implementación del proveedor de recursos](.\azure-stack-sql-resource-provider-deploy.md) en este artículo. El script se incluye con la descarga del proveedor de recursos.

El script *UpdateSQLProvider.ps1* crea una nueva máquina virtual con el código del proveedor de recursos más reciente y migra la configuración de la máquina virtual antigua a la nueva. La configuración que se migra incluye información de base de datos y del servidor de hospedaje, así como el registro DNS necesario.

El script requiere el uso de los mismos argumentos que se describen para el script DeploySqlProvider.ps1. Proporcione aquí también el certificado. 

Se recomienda descargar la imagen de Windows Server 2016 Core más reciente de Marketplace Management (Administración de Marketplace). Si tiene que instalar una actualización, puede colocar un único paquete .MSU en la ruta de acceso local de la dependencia. Si se encuentra más de un archivo .MSU, se producirá un error en el script.

Este es un ejemplo del script *UpdateSQLProvider.ps1* que puede ejecutar desde el símbolo del sistema de PowerShell. Asegúrese de cambiar la información de la cuenta y las contraseñas según sea necesario: 

> [!NOTE]
> El proceso de actualización solo se aplica a sistemas integrados.

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

# The service admin account (can be Azure AD or AD FS).
$serviceAdmin = "admin@mydomain.onmicrosoft.com"
$AdminPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force
$AdminCreds = New-Object System.Management.Automation.PSCredential ($serviceAdmin, $AdminPass)

# Set credentials for the new Resource Provider VM.
$vmLocalAdminPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force
$vmLocalAdminCreds = New-Object System.Management.Automation.PSCredential ("sqlrpadmin", $vmLocalAdminPass)

# And the cloudadmin credential required for privileged endpoint access.
$CloudAdminPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force
$CloudAdminCreds = New-Object System.Management.Automation.PSCredential ("$domain\cloudadmin", $CloudAdminPass)

# Change the following as appropriate.
$PfxPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force

# Change directory to the folder where you extracted the installation files.
# Then adjust the endpoints.
. $tempDir\UpdateSQLProvider.ps1 -AzCredential $AdminCreds `
  -VMLocalCredential $vmLocalAdminCreds `
  -CloudAdminCredential $cloudAdminCreds `
  -PrivilegedEndpoint $privilegedEndpoint `
  -DefaultSSLCertificatePassword $PfxPass `
  -DependencyFilesLocalPath $tempDir\cert
 ```

## <a name="updatesqlproviderps1-parameters"></a>Parámetros de UpdateSQLProvider.ps1
Puede especificar estos parámetros en la línea de comandos. Si no lo hace, o se produce un error en la validación de algún parámetro, se le pedirá que proporcione los parámetros requeridos.

| Nombre de parámetro | DESCRIPCIÓN | Comentario o valor predeterminado |
| --- | --- | --- |
| **CloudAdminCredential** | Credencial del administrador de la nube, necesaria para el acceso al punto de conexión con privilegios. | _Obligatorio_ |
| **AzCredential** | Credenciales de la cuenta de administrador de servicio de Azure Stack. Use las mismas credenciales que para la implementación de Azure Stack. | _Obligatorio_ |
| **VMLocalCredential** | Credenciales para la cuenta de administrador local de la VM del proveedor de recursos de SQL. | _Obligatorio_ |
| **PrivilegedEndpoint** | Dirección IP o nombre DNS del punto de conexión con privilegios. |  _Obligatorio_ |
| **DependencyFilesLocalPath** | El archivo .pfx de certificados se debe colocar también en este directorio. | _Opcional_ (_obligatorio_ para varios nodos) |
| **DefaultSSLCertificatePassword** | Contraseña para el certificado .pfx. | _requerido_ |
| **MaxRetryCount** | Número de veces que quiere volver a intentar cada operación si se produce un error.| 2 |
| **RetryDuration** |Intervalo de tiempo de expiración entre reintentos, en segundos. | 120 |
| **Desinstalación** | Se quita el proveedor de recursos y todos los recursos asociados (vea las notas siguientes). | Sin  |
| **DebugMode** | Impide la limpieza automática en caso de error. | Sin  |


## <a name="next-steps"></a>Pasos siguientes

[Mantenimiento del proveedor de recursos SQL](azure-stack-sql-resource-provider-maintain.md)
