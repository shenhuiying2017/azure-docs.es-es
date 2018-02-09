---
title: Uso de bases de datos SQL en Azure Stack | Microsoft Docs
description: "Aprenda a implementar las bases de datos SQL como un servicio en Azure Stack y los pasos para implementar de forma rápida el adaptador del proveedor de recursos de SQL Server."
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
ms.openlocfilehash: bf52ed4986b4e0930b57721c0e38bbf748045a36
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/01/2018
---
# <a name="use-sql-databases-on-microsoft-azure-stack"></a>Uso de bases de datos SQL en Microsoft Azure Stack

*Se aplica a: sistemas integrados de Azure Stack y Kit de desarrollo de Azure Stack*

Use el adaptador del proveedor de recursos de SQL Server para exponer las bases de datos SQL como un servicio de [Azure Stack](azure-stack-poc.md). Después de instalar el proveedor de recursos y conectarse a una o varias instancias de SQL Server, usted y sus usuarios pueden crear:
- Bases de datos para las aplicaciones nativas de la nube.
- Sitios web basados en SQL.
- Cargas de trabajo basadas en SQL.
No tiene que aprovisionar una máquina virtual (VM) que hospede SQL Server cada vez.

El proveedor de recursos no admite todas las funcionalidades de administración de bases de datos de [Azure SQL Database](https://azure.microsoft.com/services/sql-database/). Por ejemplo, los grupos de bases de datos elásticas y la posibilidad de aumentar o disminuir el rendimiento de las bases de datos automáticamente no están disponibles. Sin embargo, el proveedor de recursos admite operaciones similares de creación, lectura, actualización y eliminación (CRUD). La API no es compatible con SQL Database.

## <a name="sql-resource-provider-adapter-architecture"></a>Arquitectura del adaptador del proveedor de recursos de SQL
El proveedor de recursos consta de tres componentes:

- **La VM del adaptador del proveedor de recursos de SQL**, que es una máquina virtual Windows que ejecuta los servicios del proveedor.
- **El proveedor de recursos en sí**, que procesa las solicitudes de aprovisionamiento y expone los recursos de las bases de datos.
- **Los servidores que hospedan SQL Server**, que proporcionan capacidad para las bases de datos, conocidos como servidores de hospedaje.

Debe crear una o varias instancias de SQL Server o proporcionar acceso a instancias externas de SQL Server.

## <a name="deploy-the-resource-provider"></a>Implementar el proveedor de recursos

1. Si no lo ha hecho ya, registre el kit de desarrollo y descargue la imagen de Windows Server 2016 Datacenter Core, que puede descargarse desde Administración de Marketplace. Debe utilizar una imagen de Windows Server 2016 Core. También puede usar un script para crear una [imagen de Windows Server 2016](https://docs.microsoft.com/azure/azure-stack/azure-stack-add-default-image). (Asegúrese de seleccionar la opción Core). El entorno de tiempo de ejecución de .NET 3.5 ya no es necesario.

2. Inicie sesión en un host que pueda tener acceso a la VM de punto de conexión con privilegios.

    - En las instalaciones del Kit de desarrollo de Azure Stack, inicie sesión en el host físico.

    - En los sistemas de varios nodos, el host debe ser un sistema que pueda tener acceso al punto de conexión con privilegios.
    
    >[!NOTE]
    > El script se *debe* ejecutar en un sistema con Windows 10 o Windows Server 2016 con la versión más reciente del runtime de .NET instalada. De lo contrario, se produce un error de instalación. El host del SDK de Azure Stack cumple este criterio.


3. Descargue el binario del proveedor de recursos de SQL. A continuación, ejecute el autoextractor para extraer el contenido en un directorio temporal.

    >[!NOTE] 
    > La compilación del proveedor de recursos corresponde a las compilaciones de Azure Stack. Asegúrese de descargar el binario correcto para la versión de Azure Stack que se ejecuta.

    | Compilación de Azure Stack | Instalador del proveedor de recursos de SQL |
    | --- | --- |
    |1.0.180102.3, 1.0.180103.2 o 1.0.180106.1 (varios nodos) | [SQL RP, versión 1.1.14.0](https://aka.ms/azurestacksqlrp1712) |
    | 1.0.171122.1 | [SQL RP, versión 1.1.12.0](https://aka.ms/azurestacksqlrp1711) |
    | 1.0.171028.1 | [SQL RP, versión 1.1.8.0](https://aka.ms/azurestacksqlrp1710) |
  

4. El certificado raíz de Azure Stack se recupera del punto de conexión con privilegios. Para el SDK de Azure Stack, como parte de este proceso, se crea un certificado autofirmado. Para varios nodos, debe proporcionar un certificado adecuado.

   Para proporcionar su propio certificado, coloque un archivo .pfx en **DependencyFilesLocalPath** como se indica a continuación:

    - Un certificado comodín para \*.dbadapter.\<región\>.\<FQDN externo\> o un certificado de entidad de certificación único con un nombre común de sqladapter.dbadapter.\<región\>.\<FQDN externo\>.

    - Este certificado debe ser de confianza. Es decir, la cadena de confianza debe existir sin necesidad de certificados intermedios.

    - En DependencyFilesLocalPath solo existe un archivo de certificado individual.

    - El nombre de archivo no debe contener caracteres especiales.


5. Abra una consola de PowerShell con privilegios elevados **nueva** (administrativos) y cambie al directorio al que extrajo los archivos. Use una nueva ventana para evitar problemas que puedan surgir de los módulos de PowerShell incorrectos que ya están cargados en el sistema.

6. [Instale Azure PowerShell versión 1.2.11](azure-stack-powershell-install.md).

7. Ejecute el script DeploySqlProvider.ps1, que lleva a cabo estos pasos:

    - Carga los certificados y los demás artefactos en una cuenta de almacenamiento de Azure Stack.
    - Publica los paquetes de la galería para poder implementar las bases de datos SQL a través de esta.
    - Publica un paquete de galería para implementar los servidores de hospedaje.
    - Implementa una VM con la imagen de Windows Server 2016 que creó en el paso 1 y, a continuación, instala el proveedor de recursos.
    - Registra un registro de DNS local que se asigna a la VM del proveedor de recursos.
    - Registra el proveedor de recursos en la instancia local de Azure Resource Manager (usuario y administrador).

> [!NOTE]
> Si la instalación tarda más de 90 minutos, tal vez genere errores. Si se genera un error, verá un mensaje de error en la pantalla y en el archivo de registro, pero la implementación se volverá a intentar desde el paso que no pudo completarse. Es posible que los sistemas que no cumplan las especificaciones recomendadas de memoria y vCPU no puedan implementar el proveedor de recursos de SQL.
>

Este es un ejemplo que se puede ejecutar desde el símbolo del sistema de PowerShell. (Asegúrese de cambiar la información de la cuenta y las contraseñas según sea necesario).

```
# Install the AzureRM.Bootstrapper module, set the profile, and install the AzureRM and AzureStack modules.
Install-Module -Name AzureRm.BootStrapper -Force
Use-AzureRmProfile -Profile 2017-03-09-profile
Install-Module -Name AzureStack -RequiredVersion 1.2.11 -Force

# Use the NetBIOS name for the Azure Stack domain. On the Azure Stack SDK, the default is AzureStack and the default prefix is AzS.
# For integrated systems, the domain and the prefix are the same.
$domain = "AzureStack"
$prefix = "AzS"
$privilegedEndpoint = "$prefix-ERCS01"

# Point to the directory where the resource provider installation files were extracted.
$tempDir = 'C:\TEMP\SQLRP'

# The service admin account (can be Azure Active Directory or Active Directory Federation Services).
$serviceAdmin = "admin@mydomain.onmicrosoft.com"
$AdminPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force
$AdminCreds = New-Object System.Management.Automation.PSCredential ($serviceAdmin, $AdminPass)

# Set credentials for the new Resource Provider VM.
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

### <a name="deploysqlproviderps1-parameters"></a>Parámetros de DeploySqlProvider.ps1
Puede especificar estos parámetros en la línea de comandos. Si no lo hace, o se produce un error en la validación de algún parámetro, se le pedirá que proporcione los parámetros requeridos.

| Nombre de parámetro | DESCRIPCIÓN | Comentario o valor predeterminado |
| --- | --- | --- |
| **CloudAdminCredential** | Credencial del administrador de la nube, necesaria para el acceso al punto de conexión con privilegios. | _Obligatorio_ |
| **AzCredential** | Credenciales de la cuenta de administrador de servicio de Azure Stack. Use las mismas credenciales que para la implementación de Azure Stack. | _Obligatorio_ |
| **VMLocalCredential** | Credenciales para la cuenta de administrador local de la VM del proveedor de recursos de SQL. | _Obligatorio_ |
| **PrivilegedEndpoint** | Dirección IP o nombre DNS del punto de conexión con privilegios. |  _Obligatorio_ |
| **DependencyFilesLocalPath** | El archivo .pfx de certificados se debe colocar también en este directorio. | _Opcional_ (_obligatorio_ para varios nodos) |
| **DefaultSSLCertificatePassword** | Contraseña para el certificado .pfx. | _Obligatorio_ |
| **MaxRetryCount** | Número de veces que quiere volver a intentar cada operación si se produce un error.| 2 |
| **RetryDuration** | Intervalo de tiempo de expiración entre reintentos, en segundos. | 120 |
| **Desinstalación** | Se quita el proveedor de recursos y todos los recursos asociados (vea las notas siguientes). | Sin  |
| **DebugMode** | Impide la limpieza automática en caso de error. | Sin  |


## <a name="verify-the-deployment-using-the-azure-stack-portal"></a>Comprobación de la implementación mediante Azure Stack Portal

> [!NOTE]
>  Cuando finalice la ejecución del script de instalación, deberá actualizar el portal para ver la hoja de administración.


1. Inicie sesión en el portal de administración como administrador de servicios.

2. Compruebe que la implementación se realizó correctamente. Vaya a **Grupos de recursos**. A continuación, seleccione el grupo de recursos **system.\<ubicación\>.sqladapter**. Compruebe que las cuatro implementaciones se realizaron correctamente.

      ![Comprobar la implementación del proveedor de recursos de SQL](./media/azure-stack-sql-rp-deploy/sqlrp-verify.png)


## <a name="update-the-sql-resource-provider-adapter-multi-node-only-builds-1710-and-later"></a>Actualización del adaptador del proveedor de recursos de SQL (solo varios nodos; compilaciones 1710 y posteriores)
Cada vez que se actualiza la compilación de Azure Stack, se publica un nuevo adaptador del proveedor de recursos de SQL. Puede que el adaptador existente siga funcionando. Sin embargo, se recomienda actualizar a la compilación más reciente lo antes posible tras la actualización de Azure Stack. 

El proceso de actualización es similar al proceso de instalación que se ha descrito anteriormente. Se crea una nueva VM con el código del proveedor de recursos más reciente. Además, se migra la configuración a esta nueva instancia, incluida la información de la base de datos y del servidor de hospedaje. También se migra el registro DNS necesario.

Use el script de UpdateSQLProvider.ps1 con los mismos argumentos que hemos descrito antes. Aquí también debe proporcionar el certificado.

> [!NOTE]
> Este proceso de actualización solo se admite en sistemas de varios nodos.

```
# Install the AzureRM.Bootstrapper module, set the profile, and install the AzureRM and AzureStack modules.
Install-Module -Name AzureRm.BootStrapper -Force
Use-AzureRmProfile -Profile 2017-03-09-profile
Install-Module -Name AzureStack -RequiredVersion 1.2.11 -Force

# Use the NetBIOS name for the Azure Stack domain. On the Azure Stack SDK, the default is AzureStack and the default prefix is AzS.
# For integrated systems, the domain and the prefix are the same.
$domain = "AzureStack"
$prefix = "AzS"
$privilegedEndpoint = "$prefix-ERCS01"

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

### <a name="updatesqlproviderps1-parameters"></a>Parámetros de UpdateSQLProvider.ps1
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



## <a name="remove-the-sql-resource-provider-adapter"></a>Quitar el adaptador del proveedor de recursos de SQL

Para quitar el proveedor de recursos, es esencial quitar antes todas las dependencias.

1. Asegúrese de tener el paquete de implementación original que descargó para esta versión del adaptador del proveedor de recursos de SQL.

2. Todas las bases de datos de usuario deben eliminarse del proveedor de recursos. (Eliminar las bases de datos de usuario no elimina los datos). Los propios usuarios deben hacer esta tarea.

3. El administrador debe eliminar los servidores de hospedaje del adaptador del proveedor de recursos de SQL.

4. El administrador debe eliminar los planes que hagan referencia al adaptador del proveedor de recursos de SQL.

5. El administrador debe eliminar cualquier SKU y cuota asociadas con el adaptador del proveedor de recursos de SQL.

6. Vuelva a ejecutar el script de implementación con los siguientes elementos:
    - Parámetro -Uninstall
    - Puntos de conexión de Azure Resource Manager
    - DirectoryTenantID
    - Credenciales para la cuenta de administrador del servicio


## <a name="next-steps"></a>pasos siguientes

[Agregar servidores host](azure-stack-sql-resource-provider-hosting-servers.md) y [crear bases de datos](azure-stack-sql-resource-provider-databases.md).

Pruebe otros [servicios PaaS](azure-stack-tools-paas-services.md), como el [proveedor de recursos de MySQL Server](azure-stack-mysql-resource-provider-deploy.md) y el [proveedor de recursos de App Services](azure-stack-app-service-overview.md).
