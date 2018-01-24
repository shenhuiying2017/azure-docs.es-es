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
ms.date: 01/09/2018
ms.author: JeffGo
ms.openlocfilehash: e2f29a1686d0f4baa88b9d37bac0261952f4caa6
ms.sourcegitcommit: 9292e15fc80cc9df3e62731bafdcb0bb98c256e1
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/10/2018
---
# <a name="use-sql-databases-on-microsoft-azure-stack"></a>Uso de bases de datos SQL en Microsoft Azure Stack

*Se aplica a: sistemas integrados de Azure Stack y kit de desarrollo de Azure Stack*

Use el adaptador del proveedor de recursos de SQL Server para exponer las bases de datos SQL como un servicio de [Azure Stack](azure-stack-poc.md). Después de instalar el proveedor de recursos y conectarse a una o varias instancias de SQL Server, usted y sus usuarios pueden crear:
- Bases de datos para las aplicaciones nativas de la nube
- Sitios web basados en SQL
- Cargas de trabajo basadas en SQL que no tengan que aprovisionar una máquina virtual (VM) que hospede SQL Server cada vez.

El proveedor de recursos no admite todas las funcionalidades de administración de bases de datos de [Azure SQL Database](https://azure.microsoft.com/services/sql-database/). Por ejemplo, los grupos de bases de datos elásticas y la posibilidad de aumentar o disminuir el rendimiento de las bases de datos automáticamente no están disponibles. Sin embargo, el proveedor de recursos admite operaciones similares de creación, lectura, actualización y eliminación (CRUD). La API no es compatible con SQL DB.

## <a name="sql-resource-provider-adapter-architecture"></a>Arquitectura del adaptador del proveedor de recursos de SQL
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
    
    >[!NOTE]
    > El script se *debe* ejecutar en un sistema con Windows 10 o Windows Server 2016 con la versión más reciente del runtime de .NET instalada. De lo contrario la instalación puede generar errores. El host de ASDK cumple estos criterios.


3. Descargue el binario del proveedor de recursos SQL y ejecute el extractor automático para extraer el contenido en un directorio temporal.

    >[!NOTE] 
    > La compilación del proveedor de recursos corresponde a las compilaciones de Azure Stack. Debe descargar el binario correcto para la versión de Azure Stack que se ejecuta.

    | Compilación de Azure Stack | Instalador de SQL RP |
    | --- | --- |
    |1.0.180102.3 o 1.0.180106.1 (varios nodos) | [SQL RP, versión 1.1.14.0](https://aka.ms/azurestacksqlrp1712) |
    | 1.0.171122.1 | [SQL RP, versión 1.1.12.0](https://aka.ms/azurestacksqlrp1711) |
    | 1.0.171028.1 | [SQL RP, versión 1.1.8.0](https://aka.ms/azurestacksqlrp1710) |
  

4. El certificado raíz de Azure Stack se recupera desde el punto de conexión con privilegios. Para ASDK, como parte de este proceso se crea un certificado autofirmado. Para varios nodos, debe proporcionar un certificado adecuado.

    Si tiene que proporcionar su propio certificado, tendrá que colocar un archivo PFX en **DependencyFilesLocalPath** (véase más abajo) como se indica a continuación:

    - Un certificado comodín para \*.dbadapter.\< región\>.\< fqdn externo\> o un certificado de un sitio único con un nombre común de sqladapter.dbadapter.\< región\>.\< fqdn externo\>
    - Este certificado debe ser de confianza, como podría ser emitido por una entidad de certificación. Es decir, la cadena de confianza debe existir sin necesidad de certificados intermedios.
    - En DependencyFilesLocalPath solo existe un archivo de certificado individual.
    - El nombre de archivo no debe contener caracteres especiales.


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

# Use the NetBIOS name for the Azure Stack domain. On ASDK, the default is AzureStack and the default prefix is AzS
# For integrated systems, the domain and the prefix will be the same.
$domain = "AzureStack"
$prefix = "AzS"
$privilegedEndpoint = "$prefix-ERCS01"

# Point to the directory where the RP installation files were extracted
$tempDir = 'C:\TEMP\SQLRP'

# The service admin account (can be AAD or ADFS)
$serviceAdmin = "admin@mydomain.onmicrosoft.com"
$AdminPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force
$AdminCreds = New-Object System.Management.Automation.PSCredential ($serviceAdmin, $AdminPass)

# Set credentials for the new Resource Provider VM
$vmLocalAdminPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force
$vmLocalAdminCreds = New-Object System.Management.Automation.PSCredential ("sqlrpadmin", $vmLocalAdminPass)

# and the cloudadmin credential required for Privileged Endpoint access
$CloudAdminPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force
$CloudAdminCreds = New-Object System.Management.Automation.PSCredential ("$domain\cloudadmin", $CloudAdminPass)

# change the following as appropriate
$PfxPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force

# Change directory to the folder where you extracted the installation files
# and adjust the endpoints
. $tempDir\DeploySQLProvider.ps1 -AzCredential $AdminCreds `
  -VMLocalCredential $vmLocalAdminCreds `
  -CloudAdminCredential $cloudAdminCreds `
  -PrivilegedEndpoint $privilegedEndpoint `
  -DefaultSSLCertificatePassword $PfxPass `
  -DependencyFilesLocalPath $tempDir\cert
 ```

### <a name="deploysqlproviderps1-parameters"></a>Parámetros de DeploySqlProvider.ps1
Puede especificar estos parámetros en la línea de comandos. Si no lo hace, o se produce un error en la validación de algún parámetro, se le pide que proporcione los requeridos.

| Nombre de parámetro | DESCRIPCIÓN | Comentario o valor predeterminado |
| --- | --- | --- |
| **CloudAdminCredential** | La credencial del administrador de nube, necesaria para el acceso al punto de conexión con privilegios. | _requerido_ |
| **AzCredential** | Proporcione las credenciales de la cuenta de administrador de servicio de Azure Stack. Use las mismas credenciales que para la implementación de Azure Stack. | _requerido_ |
| **VMLocalCredential** | Defina las credenciales para la cuenta de administrador local de la máquina virtual del proveedor de recursos SQL. | _requerido_ |
| **PrivilegedEndpoint** | Proporcione la dirección IP o el nombre DNS del punto de conexión con privilegios. |  _requerido_ |
| **DependencyFilesLocalPath** | El archivo PFX de certificados se debe colocar también en este directorio. | _opcional_ (_obligatorio_ para varios nodos) |
| **DefaultSSLCertificatePassword** | La contraseña para el certificado .pfx | _requerido_ |
| **MaxRetryCount** | Defina el número de veces que desea volver a intentar cada operación si se produce un error.| 2 |
| **RetryDuration** | Defina el tiempo de expiración entre reintentos, en segundos. | 120 |
| **Desinstalación** | Se quita el proveedor de recursos y todos los recursos asociados (vea las notas siguientes) | Sin  |
| **DebugMode** | Impide la limpieza automática en caso de error. | Sin  |


## <a name="verify-the-deployment-using-the-azure-stack-portal"></a>Comprobación de la implementación mediante Azure Stack Portal

> [!NOTE]
>  Una vez completado el script de instalación, deberá actualizar el portal para ver la hoja de administración.


1. Inicie sesión en el portal de administración como administrador de servicios.

2. Compruebe que la implementación se realizó correctamente. Busque **Grupos de recursos** &gt;, haga clic en el grupo de recursos **system.\<location\>.sqladapter** y compruebe que las cuatro implementaciones se realizaron correctamente.

      ![Comprobación de la implementación de RP de SQL](./media/azure-stack-sql-rp-deploy/sqlrp-verify.png)


## <a name="update-the-sql-resource-provider-adapter-multi-node-only-builds-1710-and-later"></a>Actualización del adaptador del proveedor de recursos de SQL (solo varios nodos, compilaciones 1710 y posteriores)
Cada vez que se actualice la compilación de Azure Stack, se publicará un nuevo adaptador del proveedor de recursos de SQL. Aunque el adaptador existente puede seguir funcionando, es aconsejable actualizar a la compilación más reciente tan pronto como sea posible tras la actualización de Azure Stack. El proceso de actualización es muy similar al proceso de instalación que se ha descrito anteriormente. Se creará una nueva máquina virtual con el código RP más reciente y la configuración se migrará a esta nueva instancia, lo que incluye base de datos y la información del servidor de hospedaje, así como el registro DNS necesario.

Use el script de UpdateSQLProvider.ps1 con los mismos argumentos que antes. Aquí también debe proporcionar el certificado.

> [!NOTE]
> La actualización solo se admite en sistemas de varios nodos.

```
# Install the AzureRM.Bootstrapper module, set the profile, and install AzureRM and AzureStack modules
Install-Module -Name AzureRm.BootStrapper -Force
Use-AzureRmProfile -Profile 2017-03-09-profile
Install-Module -Name AzureStack -RequiredVersion 1.2.11 -Force

# Use the NetBIOS name for the Azure Stack domain. On ASDK, the default is AzureStack and the default prefix is AzS
# For integrated systems, the domain and the prefix will be the same.
$domain = "AzureStack"
$prefix = "AzS"
$privilegedEndpoint = "$prefix-ERCS01"

# Point to the directory where the RP installation files were extracted
$tempDir = 'C:\TEMP\SQLRP'

# The service admin account (can be AAD or ADFS)
$serviceAdmin = "admin@mydomain.onmicrosoft.com"
$AdminPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force
$AdminCreds = New-Object System.Management.Automation.PSCredential ($serviceAdmin, $AdminPass)

# Set credentials for the new Resource Provider VM
$vmLocalAdminPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force
$vmLocalAdminCreds = New-Object System.Management.Automation.PSCredential ("sqlrpadmin", $vmLocalAdminPass)

# and the cloudadmin credential required for Privileged Endpoint access
$CloudAdminPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force
$CloudAdminCreds = New-Object System.Management.Automation.PSCredential ("$domain\cloudadmin", $CloudAdminPass)

# change the following as appropriate
$PfxPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force

# Change directory to the folder where you extracted the installation files
# and adjust the endpoints
. $tempDir\UpdateSQLProvider.ps1 -AzCredential $AdminCreds `
  -VMLocalCredential $vmLocalAdminCreds `
  -CloudAdminCredential $cloudAdminCreds `
  -PrivilegedEndpoint $privilegedEndpoint `
  -DefaultSSLCertificatePassword $PfxPass `
  -DependencyFilesLocalPath $tempDir\cert
 ```

### <a name="updatesqlproviderps1-parameters"></a>Parámetros de UpdateSQLProvider.ps1
Puede especificar estos parámetros en la línea de comandos. Si no lo hace, o se produce un error en la validación de algún parámetro, se le pide que proporcione los requeridos.

| Nombre de parámetro | DESCRIPCIÓN | Comentario o valor predeterminado |
| --- | --- | --- |
| **CloudAdminCredential** | La credencial del administrador de nube, necesaria para el acceso al punto de conexión con privilegios. | _requerido_ |
| **AzCredential** | Proporcione las credenciales de la cuenta de administrador de servicio de Azure Stack. Use las mismas credenciales que para la implementación de Azure Stack. | _requerido_ |
| **VMLocalCredential** | Defina las credenciales para la cuenta de administrador local de la máquina virtual del proveedor de recursos SQL. | _requerido_ |
| **PrivilegedEndpoint** | Proporcione la dirección IP o el nombre DNS del punto de conexión con privilegios. |  _requerido_ |
| **DependencyFilesLocalPath** | El archivo PFX de certificados se debe colocar también en este directorio. | _opcional_ (_obligatorio_ para varios nodos) |
| **DefaultSSLCertificatePassword** | La contraseña para el certificado .pfx | _requerido_ |
| **MaxRetryCount** | Defina el número de veces que desea volver a intentar cada operación si se produce un error.| 2 |
| **RetryDuration** | Defina el tiempo de expiración entre reintentos, en segundos. | 120 |
| **Desinstalación** | Se quita el proveedor de recursos y todos los recursos asociados (vea las notas siguientes) | Sin  |
| **DebugMode** | Impide la limpieza automática en caso de error. | Sin  |



## <a name="remove-the-sql-resource-provider-adapter"></a>Quitar el adaptador del proveedor de recursos SQL

Para quitar el proveedor de recursos, es esencial quitar primero todas las dependencias.

1. Asegúrese de tener el paquete de implementación original que descargó para esta versión del adaptador del proveedor de recursos SQL.

2. Todas las bases de datos de usuario deben eliminarse del proveedor de recursos (no se eliminan los datos). Los propios usuarios deben hacerlo.

3. El administrador debe eliminar los servidores de hospedaje del adaptador del proveedor de recursos SQL.

4. El administrador debe eliminar los planes que hagan referencia al adaptador del proveedor de recursos SQL.

5. El administrador debe eliminar las SKU y las cuotas asociadas al adaptador del proveedor de recursos SQL.

6. Vuelva a ejecutar el script de implementación con el parámetro -Uninstall, los puntos de conexión de Azure Resource Manager, DirectoryTenantID y las credenciales para la cuenta de administrador del servicio.


## <a name="next-steps"></a>pasos siguientes

[Agregar servidores host](azure-stack-sql-resource-provider-hosting-servers.md) y [crear bases de datos](azure-stack-sql-resource-provider-databases.md).

Pruebe otros [servicios PaaS](azure-stack-tools-paas-services.md), como el [proveedor de recursos de MySQL Server](azure-stack-mysql-resource-provider-deploy.md) y el [proveedor de recursos de App Services](azure-stack-app-service-overview.md).
