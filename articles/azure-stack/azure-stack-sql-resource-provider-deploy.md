---
title: Uso de bases de datos SQL en Azure Stack | Microsoft Docs
description: Aprenda a implementar las bases de datos SQL como un servicio en Azure Stack y los pasos para implementar de forma rápida el adaptador del proveedor de recursos de SQL Server.
services: azure-stack
documentationCenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/07/2018
ms.author: mabrigg
ms.reviewer: jeffgo
ms.openlocfilehash: 4d2a00f04e5b07aeb3585fb3ab6c8966e0de7e19
ms.sourcegitcommit: 8c3267c34fc46c681ea476fee87f5fb0bf858f9e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 03/09/2018
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

> [!NOTE]
> Los servidores de hospedaje que se instalan en sistemas integrados de Azure Stack se deben crear mediante una suscripción de inquilino. No se pueden crear a partir de la suscripción de proveedor predeterminada. Se deben crear desde el portal de inquilino o desde una sesión de PowerShell con un inicio de sesión adecuado. Todos los servidores de hospedaje son máquinas virtuales facturables y deben tener las licencias adecuadas. El administrador del servicio puede ser el propietario de la suscripción de inquilino.

## <a name="deploy-the-resource-provider"></a>Implementar el proveedor de recursos

1. Si no lo ha hecho ya, registre el kit de desarrollo y descargue la imagen de Windows Server 2016 Datacenter Core, que puede descargarse desde Administración de Marketplace. Debe utilizar una imagen de Windows Server 2016 Core. También puede usar un script para crear una [imagen de Windows Server 2016](https://docs.microsoft.com/azure/azure-stack/azure-stack-add-default-image). (Asegúrese de seleccionar la opción Core).

2. Inicie sesión en un host que pueda tener acceso a la VM de punto de conexión con privilegios.

    - En las instalaciones del Kit de desarrollo de Azure Stack, inicie sesión en el host físico.

    - En los sistemas de varios nodos, el host debe ser un sistema que pueda tener acceso al punto de conexión con privilegios.
    
    >[!NOTE]
    > El script se *debe* ejecutar en un sistema con Windows 10 o Windows Server 2016 con la versión más reciente del runtime de .NET instalada. De lo contrario, se produce un error de instalación. El host del SDK de Azure Stack cumple este criterio.


3. Descargue el binario del proveedor de recursos de SQL. A continuación, ejecute el autoextractor para extraer el contenido en un directorio temporal.

    >[!NOTE] 
    > El proveedor de recursos tiene una compilación mínima correspondiente de Azure Stack. Asegúrese de descargar el binario correcto para la versión de Azure Stack que se ejecuta.

    | Compilación de Azure Stack | Instalador del proveedor de recursos de SQL |
    | --- | --- |
    | 1802: 1.0.180302.1 | [SQL RP, versión 1.1.18.0](https://aka.ms/azurestacksqlrp1802) |
    | 1712: 1.0.180102.3, 1.0.180103.2 o 1.0.180106.1 (varios nodos) | [SQL RP, versión 1.1.14.0](https://aka.ms/azurestacksqlrp1712) |
    | 1711: 1.0.171122.1 | [SQL RP, versión 1.1.12.0](https://aka.ms/azurestacksqlrp1711) |
    | 1710: 1.0.171028.1 | [SQL RP, versión 1.1.8.0](https://aka.ms/azurestacksqlrp1710) |
  

4. El certificado raíz de Azure Stack se recupera del punto de conexión con privilegios. Para el SDK de Azure Stack, como parte de este proceso, se crea un certificado autofirmado. En sistemas integrados, debe proporcionar un certificado adecuado.

   Para proporcionar su propio certificado, coloque un archivo .pfx en **DependencyFilesLocalPath** como se indica a continuación:

    - Un certificado comodín para \*.dbadapter.\<región\>.\<FQDN externo\> o un certificado de entidad de certificación único con un nombre común de sqladapter.dbadapter.\<región\>.\<FQDN externo\>.

    - Este certificado debe ser de confianza. Es decir, la cadena de confianza debe existir sin necesidad de certificados intermedios.

    - Solo puede existir un archivo de certificado en el directorio que apunta al parámetro DependencyFilesLocalPath.

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
    - Opcionalmente, instala una única actualización de Windows durante la instalación del proveedor de recursos.

8. Se recomienda descargar la imagen de Windows Server 2016 Core más reciente de Marketplace Management (Administración de Marketplace). Si tiene que instalar una actualización, puede colocar un único paquete .MSU en la ruta de acceso local de la dependencia. Si se encuentra más de un archivo .MSU, se producirá un error en el script.


Este es un ejemplo que se puede ejecutar desde el símbolo del sistema de PowerShell. (Asegúrese de cambiar la información de la cuenta y las contraseñas según sea necesario).

```
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
Cuando las compilaciones de Azure Stack se actualicen, podría lanzarse un nuevo adaptador del proveedor de recursos de Azure SQL. Aunque el adaptador existente continúa funcionando, se recomienda actualizar a la compilación más reciente lo antes posible. Las actualizaciones deben instalarse en orden: no se pueden omitir versiones (consulte la tabla del paso 3 de [Implementar el proveedor de recursos](#deploy-the-resource-provider)).

Para actualizar el proveedor de recursos, use el script *UpdateMySQLProvider.ps1*. El procedimiento es similar al usado para instalar un proveedor de recursos, como se describe en la sección [Implementar el proveedor de recursos](#deploy-the-resource-provider) de este artículo. El script se incluye con la descarga del proveedor de recursos.

El script *UpdateSQLProvider.ps1* crea una nueva máquina virtual con el código del proveedor de recursos más reciente y migra la configuración de la máquina virtual antigua a la nueva. La configuración que se migra incluye información de base de datos y del servidor de hospedaje, así como el registro DNS necesario.

El script requiere el uso de los mismos argumentos que se describen para el script DeploySqlProvider.ps1. Proporcione aquí también el certificado. 

Se recomienda descargar la imagen de Windows Server 2016 Core más reciente de Marketplace Management (Administración de Marketplace). Si tiene que instalar una actualización, puede colocar un único paquete .MSU en la ruta de acceso local de la dependencia. Si se encuentra más de un archivo .MSU, se producirá un error en el script.

Este es un ejemplo del script *UpdateSQLProvider.ps1* que puede ejecutar desde el símbolo del sistema de PowerShell. Asegúrese de cambiar la información de la cuenta y las contraseñas según sea necesario: 

> [!NOTE]
> El proceso de actualización solo se aplica a sistemas integrados.

```
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


## <a name="collect-diagnostic-logs"></a>Recopilación de registros de diagnóstico
El proveedor de recursos SQL es una máquina virtual bloqueada. Si es necesario recopilar registros de la máquina virtual, se proporciona para tal fin un punto de conexión _DBAdapterDiagnostics_ de JEA (Just Enough Administration) de PowerShell. Hay dos comandos disponibles a través de este punto de conexión:

* Get-AzsDBAdapterLog: prepara un paquete ZIP que contiene registros de diagnóstico del proveedor de recursos y lo coloca en la unidad de usuario de la sesión. El comando se puede invocar sin ningún parámetro y recopila las últimas cuatro horas de registros.
* Remove-AzsDBAdapterLog: limpia los paquetes de registros existentes en la máquina virtual del proveedor de recursos.

Se crea una cuenta de usuario llamada _dbadapterdiag_ durante la implementación o la actualización del proveedor de recursos para conectar con el punto de conexión de diagnóstico y extraer los registros del proveedor de recursos. La contraseña de esta cuenta es la misma que la proporcionada para la cuenta de administrador local durante la implementación o actualización.

Para usar estos comandos, deberá crear una sesión remota de PowerShell a la máquina virtual del proveedor de recursos e invocar el comando. También puede proporcionar los parámetros FromDate y ToDate. Si no especifica uno o ninguno de estos parámetros, el valor de FromDate será cuatro horas antes de la hora actual y el valor de ToDate será la hora actual.

Este script de ejemplo muestra el uso de estos comandos:

```
# Create a new diagnostics endpoint session.
$databaseRPMachineIP = '<RP VM IP>'
$diagnosticsUserName = 'dbadapterdiag'
$diagnosticsUserPassword = '<see above>'

$diagCreds = New-Object System.Management.Automation.PSCredential `
        ($diagnosticsUserName, $diagnosticsUserPassword)
$session = New-PSSession -ComputerName $databaseRPMachineIP -Credential $diagCreds `
        -ConfigurationName DBAdapterDiagnostics

# Sample captures logs from the previous one hour
$fromDate = (Get-Date).AddHours(-1)
$dateNow = Get-Date
$sb = {param($d1,$d2) Get-AzSDBAdapterLog -FromDate $d1 -ToDate $d2}
$logs = Invoke-Command -Session $session -ScriptBlock $sb -ArgumentList $fromDate,$dateNow

# Copy the logs
$sourcePath = "User:\{0}" -f $logs
$destinationPackage = Join-Path -Path (Convert-Path '.') -ChildPath $logs
Copy-Item -FromSession $session -Path $sourcePath -Destination $destinationPackage

# Cleanup logs
$cleanup = Invoke-Command -Session $session -ScriptBlock {Remove- AzsDBAdapterLog }
# Close the session
$session | Remove-PSSession
```

## <a name="maintenance-operations-integrated-systems"></a>Operaciones de mantenimiento (sistemas integrados)
El proveedor de recursos SQL es una máquina virtual bloqueada. La actualización de la seguridad de la máquina virtual del proveedor de recursos se puede realizar mediante el punto de conexión _DBAdapterMaintenance_ de JEA (Just Enough Administration) de PowerShell.

Para facilitar estas operaciones, se proporciona un script con el paquete de instalación del proveedor de recursos.

### <a name="update-the-virtual-machine-operating-system"></a>Actualización del sistema operativo de la máquina virtual
Hay varias maneras de actualizar la máquina virtual Windows Server:
* Instalar el paquete más reciente del proveedor de recursos mediante una imagen de Windows Server 2016 Core actualmente revisada
* Instalar un paquete de Windows Update durante la instalación o actualización del proveedor de recursos


### <a name="update-the-virtual-machine-windows-defender-definitions"></a>Actualización de las definiciones de Windows Defender de máquina virtual

Siga estos pasos para actualizar las definiciones de Defender:

1. Descargue la actualización de las definiciones de Windows Defender desde [esta página](https://www.microsoft.com/en-us/wdsi/definitions).

    En esa página, en "Manually download and install the definitions" (Descargar e instalar manualmente las definiciones), descargue el archivo de 64 bits "Windows Defender Antivirus for Windows 10 and Windows 8.1" (Windows Defender Antivirus para Windows 10 y Windows 8.1). 
    
    Vínculo directo: https://go.microsoft.com/fwlink/?LinkID=121721&arch=x64

2. Cree una sesión de PowerShell al punto de conexión de mantenimiento de la máquina virtual del adaptador del proveedor de recursos SQL.
3. Copie el archivo de actualización de definiciones en la máquina del adaptador de base de datos mediante la sesión del punto de conexión de mantenimiento.
4. En la sesión de PowerShell de mantenimiento, invoque el comando _Update-DBAdapterWindowsDefenderDefinitions_.
5. Después de la instalación, se recomienda quitar el archivo de actualización de definiciones usado. Se puede quitar en la sesión de mantenimiento mediante el comando _Remove-ItemOnUserDrive)_.


Este es un script de ejemplo para actualizar las definiciones de Defender (sustituya la dirección o el nombre de la máquina virtual por el valor real):

```
# Set credentials for the diagnostic user
$diagPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force
$diagCreds = New-Object System.Management.Automation.PSCredential `
    ("dbadapterdiag", $vmLocalAdminPass)$diagCreds = Get-Credential

# Public IP Address of the DB adapter machine
$databaseRPMachine  = "XX.XX.XX.XX"
$localPathToDefenderUpdate = "C:\DefenderUpdates\mpam-fe.exe"
 
# Download Windows Defender update definitions file from https://www.microsoft.com/en-us/wdsi/definitions. 
Invoke-WebRequest -Uri https://go.microsoft.com/fwlink/?LinkID=121721&arch=x64 `
    -Outfile $localPathToDefenderUpdate 

# Create session to the maintenance endpoint
$session = New-PSSession -ComputerName $databaseRPMachine `
    -Credential $diagCreds -ConfigurationName DBAdapterMaintenance
# Copy defender update file to the db adapter machine
Copy-Item -ToSession $session -Path $localPathToDefenderUpdate `
     -Destination "User:\mpam-fe.exe"
# Install the update file
Invoke-Command -Session $session -ScriptBlock `
    {Update-AzSDBAdapterWindowsDefenderDefinitions -DefinitionsUpdatePackageFile "User:\mpam-fe.exe"}
# Cleanup the definitions package file and session
Invoke-Command -Session $session -ScriptBlock `
    {Remove-AzSItemOnUserDrive -ItemPath "User:\mpam-fe.exe"}
$session | Remove-PSSession
```

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


## <a name="next-steps"></a>Pasos siguientes

[Agregar servidores host](azure-stack-sql-resource-provider-hosting-servers.md) y [crear bases de datos](azure-stack-sql-resource-provider-databases.md).

Pruebe otros [servicios PaaS](azure-stack-tools-paas-services.md), como el [proveedor de recursos de MySQL Server](azure-stack-mysql-resource-provider-deploy.md) y el [proveedor de recursos de App Services](azure-stack-app-service-overview.md).
