---
title: Uso de bases de datos SQL en Azure Stack | Microsoft Docs
description: "Aprenda a implementar las bases de datos SQL como un servicio en Azure Stack y los pasos para implementar de forma rápida el adaptador del proveedor de recursos de SQL Server"
services: azure-stack
documentationCenter: 
author: JeffGoldner
manager: bradleyb
editor: 
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/10/2017
ms.author: JeffGo
ms.openlocfilehash: 6e65af68dcd2306aabda65efdf8fe056c0d9b4a4
ms.sourcegitcommit: 6a22af82b88674cd029387f6cedf0fb9f8830afd
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/11/2017
---
# <a name="use-sql-databases-on-microsoft-azure-stack"></a>Uso de bases de datos SQL en Microsoft Azure Stack

*Se aplica a: Sistemas integrados de Azure Stack y Azure Stack Development Kit*

Use el adaptador del proveedor de recursos de SQL Server para exponer las bases de datos SQL como un servicio de [Azure Stack](azure-stack-poc.md). Después de instalar el proveedor de recursos y conectarse a una o varias instancias de SQL Server, usted y sus usuarios pueden crear:
- bases de datos para las aplicaciones nativas de la nube
- sitios web basados en SQL
- cargas de trabajo basadas en SQL que no tengan que aprovisionar una máquina virtual (VM) que hospede SQL Server cada vez.

El proveedor de recursos no admite todas las funcionalidades de administración de bases de datos de [Azure SQL Database](https://azure.microsoft.com/services/sql-database/). Por ejemplo, los grupos de bases de datos elásticas y la posibilidad de aumentar o disminuir el rendimiento de las bases de datos automáticamente no están disponibles. Sin embargo, el proveedor de recursos admite operaciones similares de creación, lectura, actualización y eliminación (CRUD). La API no es compatible con SQL DB.

## <a name="sql-server-resource-provider-adapter-architecture"></a>Arquitectura del adaptador del proveedor de recursos de SQL Server
El proveedor de recursos tiene tres componentes:

- **La máquina virtual del adaptador del proveedor de recursos SQL**, que es una máquina virtual Windows que ejecuta los servicios del proveedor.
- **El proveedor de recursos en sí**, que procesa las solicitudes de aprovisionamiento y expone los recursos de las bases de datos.
- **Los servidores que hospedan SQL Server**, que proporcionan capacidad para las bases de datos, conocidos como servidores de hospedaje.

Debe crear uno o varios servidores SQL, o proporcionar acceso a instancias externas de SQL.

## <a name="deploy-the-resource-provider"></a>Implementar el proveedor de recursos

1. Si no lo ha hecho ya, registre el kit de desarrollo y descargue la imagen de Windows Server 2016 Datacenter Core, que puede descargarse desde Administración de Marketplace. Debe utilizar una imagen de Windows Server 2016 Core. También puede usar un script para crear una [imagen de Windows Server 2016](https://docs.microsoft.com/azure/azure-stack/azure-stack-add-default-image). El entorno de tiempo de ejecución de .NET 3.5 ya no es necesario.

2. Inicie sesión en un host que pueda tener acceso a la máquina virtual de punto de conexión con privilegios.

    a. En las instalaciones de Azure Stack Development Kit (ASDK), inicie sesión en el host físico.

    b. En los sistemas de varios nodos, el host debe ser un sistema que pueda tener acceso al punto de conexión con privilegios.

3. [Descargue el archivo de binarios del proveedor de recursos SQL](https://aka.ms/azurestacksqlrp) y ejecute el extractor automático para extraer el contenido en un directorio temporal.

4. El certificado raíz de Azure Stack se recupera desde el punto de conexión con privilegios. Para ASDK, como parte de este proceso se crea un certificado autofirmado. Para varios nodos, debe proporcionar un certificado adecuado.

    Si tiene que proporcionar su propio certificado, es necesario el siguiente certificado:

    Un certificado comodín para \*.dbadapter.\<región\>.\<fqdn externo\>. Este certificado debe ser de confianza, como podría ser emitido por una entidad de certificación. Es decir, la cadena de confianza debe existir sin necesidad de certificados intermedios. Se puede usar un solo certificado de sitio con el nombre explícito de la máquina virtual [sqladapter] utilizado durante la instalación.


5. Abra una consola de PowerShell con privilegios elevados **nueva** (administrativos) y cambie al directorio al que extrajo los archivos. Use una nueva ventana para evitar problemas que puedan surgir de los módulos de PowerShell incorrectos que ya están cargados en el sistema.

6. [Instale Azure PowerShell versión 1.2.11](azure-stack-powershell-install.md).

7. Ejecute el script DeploySqlProvider.ps1 con los parámetros que se enumeran a continuación.

El script sigue estos pasos:

- Cargue los certificados y los demás artefactos en una cuenta de almacenamiento de Azure Stack.
- Publique los paquetes de la galería para poder implementar las bases de datos SQL a través de ella.
- Publique un paquete de galería para implementar los servidores de hospedaje.
- Implemente una máquina virtual con la imagen de Windows Server 2016 que creó en el paso 1 e instale el proveedor de recursos.
- Registre un registro de DNS local que se asigne a la VM del proveedor de recursos.
- Registre el proveedor de recursos en Azure Resource Manager local (usuario y administrador).

> [!NOTE]
> Si la instalación tarda más de 90 minutos, se puede producir un error y verá un mensaje de error en la pantalla y en el archivo de registro, pero la implementación vuelve a intentarse desde el paso que no pudo completarse. Es posible que los sistemas que no cumplan las especificaciones recomendadas de memoria y vCPU no puedan implementar SQL RP.
>

Este es un ejemplo que puede ejecutar desde el símbolo del sistema de PowerShell (pero cambie la información de la cuenta y la contraseña según sea necesario):

```
# Install the AzureRM.Bootstrapper module, set the profile, and install AzureRM and AzureStack modules
Install-Module -Name AzureRm.BootStrapper -Force
Use-AzureRmProfile -Profile 2017-03-09-profile
Install-Module -Name AzureStack -RequiredVersion 1.2.11 -Force

# Use the NetBIOS name for the Azure Stack domain. On ASDK, the default is AzureStack
$domain = "AzureStack"
# Point to the directory where the RP installation files were extracted
$tempDir = 'C:\TEMP\SQLRP'

# The service admin account (can be AAD or ADFS)
$serviceAdmin = "admin@mydomain.onmicrosoft.com"
$AdminPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force
$AdminCreds = New-Object System.Management.Automation.PSCredential ($serviceAdmin, $AdminPass)

# Set the credentials for the Resource Provider VM
$vmLocalAdminPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force
$vmLocalAdminCreds = New-Object System.Management.Automation.PSCredential ("sqlrpadmin", $vmLocalAdminPass)

# and the cloudadmin credential required for Privileged Endpoint access
$CloudAdminPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force
$CloudAdminCreds = New-Object System.Management.Automation.PSCredential ("$domain\cloudadmin", $CloudAdminPass)

# change the following as appropriate
$PfxPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force

# Change directory to the folder where you extracted the installation files
# and adjust the endpoints
.$tempDir\DeploySQLProvider.ps1 -AzCredential $AdminCreds -VMLocalCredential $vmLocalAdminCreds -CloudAdminCredential $cloudAdminCreds -PrivilegedEndpoint '10.10.10.10' -DefaultSSLCertificatePassword $PfxPass -DependencyFilesLocalPath $tempDir\cert
 ```

### <a name="deploysqlproviderps1-parameters"></a>Parámetros de DeploySqlProvider.ps1
Puede especificar estos parámetros en la línea de comandos. Si no lo hace, o se produce un error en la validación de algún parámetro, se le pide que proporcione los requeridos.

| Nombre de parámetro | Descripción | Comentario o valor predeterminado |
| --- | --- | --- |
| **CloudAdminCredential** | La credencial del administrador de nube, necesaria para el acceso al punto de conexión con privilegios. | _obligatorio_ |
| **AzCredential** | Proporcione las credenciales de la cuenta de administrador de servicio de Azure Stack. Use las mismas credenciales que para la implementación de Azure Stack. | _obligatorio_ |
| **VMLocalCredential** | Defina las credenciales para la cuenta de administrador local de la máquina virtual del proveedor de recursos SQL. | _obligatorio_ |
| **PrivilegedEndpoint** | Proporcione la dirección IP o el nombre DNS del punto de conexión con privilegios. |  _obligatorio_ |
| **DependencyFilesLocalPath** | El archivo PFX de certificados se debe colocar también en este directorio. | _opcional_ (_obligatorio_ para varios nodos) |
| **DefaultSSLCertificatePassword** | La contraseña para el certificado .pfx | _requerido_ |
| **MaxRetryCount** | Defina el número de veces que desea volver a intentar cada operación si se produce un error.| 2 |
| **RetryDuration** | Defina el tiempo de expiración entre reintentos, en segundos. | 120 |
| **Desinstalación** | Se quita el proveedor de recursos y todos los recursos asociados (vea las notas siguientes) | No |
| **DebugMode** | Impide la limpieza automática en caso de error. | No |


## <a name="verify-the-deployment-using-the-azure-stack-portal"></a>Comprobación de la implementación mediante Azure Stack Portal

> [!NOTE]
>  Una vez completado el script de instalación, deberá actualizar el portal para ver la hoja de administración.


1. Inicie sesión en el portal de administración como administrador de servicios.

2. Compruebe que la implementación se realizó correctamente. Busque **Grupos de recursos** &gt;, haga clic en el grupo de recursos **system.\<location\>.sqladapter** y compruebe que las cuatro implementaciones se realizaron correctamente.

      ![Comprobación de la implementación de RP de SQL](./media/azure-stack-sql-rp-deploy/sqlrp-verify.png)





## <a name="removing-the-sql-adapter-resource-provider"></a>Eliminación del proveedor de recursos del adaptador de SQL

Para quitar el proveedor de recursos, es esencial quitar primero todas las dependencias.

1. Asegúrese de tener el paquete de implementación original que descargó para esta versión del proveedor de recursos.

2. Todas las bases de datos de usuario deben eliminarse del proveedor de recursos (no se eliminan los datos). Los propios usuarios deben hacerlo.

3. El administrador debe eliminar los servidores de hospedaje del adaptador de SQL.

4. El administrador debe eliminar los planes que hagan referencia al adaptador de SQL.

5. El administrador debe eliminar las SKU y las cuotas asociadas al adaptador de SQL.

6. Vuelva a ejecutar el script de implementación con el parámetro -Uninstall, los puntos de conexión de Azure Resource Manager, DirectoryTenantID y las credenciales para la cuenta de administrador del servicio.


## <a name="next-steps"></a>Pasos siguientes


Pruebe otros [servicios PaaS](azure-stack-tools-paas-services.md), como el [proveedor de recursos de MySQL Server](azure-stack-mysql-resource-provider-deploy.md) y el [proveedor de recursos de App Services](azure-stack-app-service-overview.md).
